---
title: Vue+webpack+laravel开发
date: 2017-04-24 12:00:00
tags: 
- Vue
- webpack
- laravel
- web
---

这是一次现代化web全栈开发的首次尝试。
<!--more-->

## 概念和想法
使用前后端分离的架构，通过Http协议进行数据传输。

## Laravel 5.2 用到的技术

* 使用OAuth2.0 来进行验证用户及提供API权限问题
* 使用dingo API来实现API接口
* API采用RESTful思想设计
* 使用云片网服务进行短信发送与验证
* 代码架构充分分离，分别为
	* Controller（接受请求）
	* Service（处理主要逻辑）
	* Repository （处理数据库Model）
	* Presenters（处理模版页面逻辑 **由于我是作为api service，所以不需要** ）

### 前端
主要考虑了既然开发了通过API提供服务的后端，那么网站前端也使用相应的技术比较方便。

我选择了vue+webpack实现的单页面应用。

#### webpack的全局变量

当我需要使用开源的类库时，引入变成了一个问题。

例如jquery，当我在网上找了所有方法，并分别尝试了：

* 在ProvidePlugin中当作插件载入
* 在main.js中require载入
* 使用expose-loader暴露全局

均失败的结果，让我对自己的项目是否破损了产生怀疑。

后来，在一次搜索中发现有人和我遇到了同样的情况，各种方法尝试，在调用`$`会出现未定义的问题，但配置没问题。

有帮助者给出了解决方案：[https://github.com/SimulatedGREG/electron-vue/issues/36](https://github.com/SimulatedGREG/electron-vue/issues/36)

> It looks like you got everything setup properly. The error you are receiving is from ESLint's no-undef rule. Since jQuery uses the global variable $ or jQuery, ESLint needs to be configured to know about it. Within your .eslintrc.js file, add them to globals.

 `.eslintrc.js`

```json
{
// Other configs...
  "globals": {
    "$": true,
    "jQuery": true
  }
}
```


也就是说`ESLint`需要配置，并设置为全局。

#### components的组装
在vue中，一个非常重要的理念就是使用component。可以提高开发效率，增加高复用性。

例如我在写后台管理页面时，会需要用到相同的`sidebar`，那么如何进行类似继承的复用，代码如下：

```javascript
import Vue from 'vue'
import Router from 'vue-router'

import NotFoundView from '@/components/public/404'
import DashBoard from '@/components/public/dashboard'

import AnalyticView from '@/components/analytic/show'
// ...

Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/',
      component: DashBoard,
      children: [
        {
          path: 'analytic',
          component: AnalyticView,
          name: 'analytic'
        }
      ]
    },
    {// not found handler
      path: '*',
      component: NotFoundView
    }
  ]
})
```

注意，这里的`dashborad.vue`的代码
```html
<template lang="html">
  <div class="dashboard">
    <sidebar />
    <div class="main">
      <router-view></router-view>
    </div>
  </div>
</template>

<script>
import Sidebar from './sidebar'

export default {
  name: 'Dashboard',
  components: { Sidebar }
}
</script>

<style lang="stylus">
  .main
    margin-left 120px
    margin-right 20px
    margin-top 20px
</style>
```
需要将`<router-view></router-view>`继续往下传递。
