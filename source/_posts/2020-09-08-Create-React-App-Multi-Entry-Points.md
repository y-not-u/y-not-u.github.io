---
title: Create-React-App创建的项目进行多页面构建
date: 2020-09-08 12:00:00
tags: react,javascript
---

一个项目，两个页面，共用组件！没错，就是Multi Entry Point。
<!--more-->

## 背景

- 在一个前端项目中，需要有`前台用户`和`后台管理`两个站点
- 登录页面设计完全独立，与主界面不一致
- 拆分bundle文件，优化项目文件大小

等等情况都可以使用多个Entry Point解决。

由于通过`create-react-app`创建的React项目，webpack的配置是经过封装的。而且官方也说明，不会通过简易方式提供entry point的配置，需要自己对webpack.config.js进行自定义修改。

## 方案

主要参考来自两篇文章

- [StackOverflow](https://stackoverflow.com/a/61815449)
- [Github](https://github.com/danvc/create-react-app-multiple-entry-points)

1. 先通过`yarn eject` 把webpack的配置文件暴露出来。（曾经试过用craco进行更少副作用的配置，由于自由度不够强大，失败告终）

2. `/config/paths.js` 中添加appPages

   ```javascript
   const appPages = [
     {
       "name": "user",
       "title": "user",
       "appHtml": "public/user.html", // 注入的模板文件
       "appIndexJs": "src/pages/User/index" // 入口文件
     },
     {
       "name": "admin",
       "title": "admin",
       "appHtml": "public/admin.html", // 注入的模板文件
       "appIndexJs": "src/pages/Admin/index" // 入口文件
     }
   ].map(p => {
     return {
       ...p,
       appHtml: resolveApp(p.appHtml), // 注入的模板文件
       appIndexJs: resolveModule(resolveApp, p.appIndexJs) // 入口文件
     }
   })

   module.exports = {
     // ...
     appPages: appPages // 暴露出变量
   }
   ```

3. `webpack.config.js` 配置entry points

   ```javascript
   let entries = {};
   paths.appPages.forEach(appPage => {
     entries[appPage.name] = [
       isEnvDevelopment && require.resolve('react-dev-utils/webpackHotDevClient'),
       appPage.appIndexJs,
     ].filter(Boolean)
   });

   return {
     // ...
     /* entry: [
         isEnvDevelopment &&
            require.resolve('react-dev-utils/webpackHotDevClient'),
         paths.appIndexJs,
     ].filter(Boolean), */
     entry: entries // 注释掉默认的，配置webpack的多个entry
   }
   ```

4. `webpack.config.js`配置HtmlPlugin，进行静态页面的注入<srcipt>

   ```javascript
   const htmlPlugins = []
   paths.appPages.forEach(appPage => {
     htmlPlugins.push(
       new HtmlWebpackPlugin(
         Object.assign(
           {},
           {
             inject: true,
             template: appPage.appHtml,
             filename: `${appPage.name}.html`,
             title: appPage.title,
             chunks: [appPage.name]
           },
           isEnvProduction
           ? {
             minify: {
               removeComments: true,
               collapseWhitespace: true,
               removeRedundantAttributes: true,
               useShortDoctype: true,
               removeEmptyAttributes: true,
               removeStyleLinkTypeAttributes: true,
               keepClosingSlash: true,
               minifyJS: true,
               minifyCSS: true,
               minifyURLs: true,
             },
           }
           : undefined
         )
       )
     )
   })

   plugins: [
     ...htmlPlugins, // 填入plugins
     // ...把原来的看需求去留
   ]
   ```

5. `webpack.config.js` 更新ManifestPlugin加载新的entry point

   ```javascript
   new ManifestPlugin({
     fileName: 'asset-manifest.json',
     publicPath: paths.publicUrlOrPath,
     generate: (seed, files, entrypoints) => {
       const manifestFiles = files.reduce((manifest, file) => {
         manifest[file.name] = file.path;
         return manifest;
       }, seed);

       // 增加的代码
       let entrypointFiles = [];
       for (let [entryFile, fileName] of Object.entries((entrypoints))) {
         let notMapFiles = fileName.filter(fileName => !fileName.endsWith('.map'));
         entrypointFiles = entrypointFiles.concat(notMapFiles);
       }

       // const entrypointFiles = entrypoints.main.filter(
       //   fileName => !fileName.endsWith('.map')
       // );

       return {
         files: manifestFiles,
         entrypoints: entrypointFiles,
       };
     },
   }),

   ```

6. `webpack.config.js` 最重要的一步，更改生成的js文件名称

   ```javascript
   output: {
     path: isEnvProduction ? paths.appBuild : undefined,
       pathinfo: isEnvDevelopment,

       // 由bundle.js 改为[name].bundle.js
       filename: isEnvProduction
         ? 'static/js/[name].[contenthash:8].js'
         : isEnvDevelopment && 'static/js/[name].bundle.js',

       futureEmitAssets: true,
       chunkFilename: isEnvProduction
         ? 'static/js/[name].[contenthash:8].chunk.js'
         : isEnvDevelopment && 'static/js/[name].chunk.js',
       publicPath: paths.publicUrlOrPath,
       devtoolModuleFilenameTemplate: isEnvProduction
         ? info =>
           path
             .relative(paths.appSrc, info.absoluteResourcePath)
             .replace(/\\/g, '/')
         : isEnvDevelopment &&
           (info => path.resolve(info.absoluteResourcePath).replace(/\\/g, '/')),
         jsonpFunction: `webpackJsonp${appPackageJson.name}`,
         globalObject: 'this',
   }
   ```

7. 现在就能运行访问了。如果你要额外进行路由配置，则在`webpackDevServer.config.js`中配置historyApiFallback

   ```javascript
     historyApiFallback: {
       disableDotRule: true,
       verbose: true,
       rewrites: [
         { from: /^\/user/, to: '/user.html' },
         { from: /^\/admin/, to: '/admin.html' },
       ]
     },
   ```

