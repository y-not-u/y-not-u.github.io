---
title: 提交第一个包到 AUR
date: 2024-07-17 10:20:18
tags:
   - Linux
---

![image.png](https://cdn.sa.net/2024/07/17/H5oViWD91IGEdS8.png)

用了几年 Arch Linux，最大的快乐之一就是来自于强大的 Arch User Repository(AUR) 社区，其拥有令人惊叹的多样性软件安装包，一键即可安装众多软件。

最近在使用一款 [Msty](https://msty.app) 的 AI 客户端，一个通过 Electron 技术打包的软件。官网提供了 AppImage 程序，Linux 下载后就能使用，但是每次更新安装都比较麻烦，而且 AUR 上没有提供这款软件的包，我便起了歹心想贡献一把。

<!-- more -->

## 编写 PKGBUILD

`PKGBUILD` 文件是核心，包含了版本信息、资源管理、依赖管理、安装步骤等。

以下是我对 Msty AppImage 做的一份 `PKGBUILD`

```
# Maintainer: y-not-u <voganwong@hotmail.com>

pkgbase=msty
pkgname=msty-bin
_pkgname="${pkgname%-bin}"
pkgver=1.0.3
pkgrel=1
pkgdesc="The easiest way to use local and online AI models"
arch=('x86_64')
url="https://msty.app"
license=('custom')
depends=()
options=('!strip')
source=("$pkgname-$pkgver.AppImage::https://assets.msty.app/Msty_x86_64.AppImage"
    "$_pkgname.desktop"
    "$_pkgname.png")
sha256sums=('SKIP'
            'SKIP'
            'SKIP')
conflicts=("$_pkgname")

package() {
    cd "$srcdir"

    # Create directories
    install -dm755 "$pkgdir/usr/bin"
    install -dm755 "$pkgdir/opt/$pkgname"
    install -dm755 "$pkgdir/usr/share/applications"
    install -dm755 "$pkgdir/usr/share/icons/hicolor/256x256/apps"

    # Install AppImage
    install -Dm755 "$pkgname-$pkgver.AppImage" "$pkgdir/opt/$pkgname/$pkgname.AppImage"

      # Install icon
    install -Dm644 "$_pkgname.png" "$pkgdir/usr/share/icons/hicolor/256x256/apps/$_pkgname.png"

      # Install desktop file
    install -Dm644 "$_pkgname.desktop" "$pkgdir/usr/share/applications/$_pkgname.desktop"

    # Symlink executable
    ln -s "/opt/${pkgname}/${pkgname}.AppImage" "${pkgdir}/usr/bin/${_pkgname}"
}
```

其中 `source` 则代表包里携带的资源。 `AppImage` 文件从网络下载，而 `.desktop` 和 `icon` 文件则是仓库里我编写提交的。

`package` 部分则是执行的安装步骤，分门别类的将文件放到其合理位置。

进一步了解可以查看仓库：[https://github.com/y-not-u/Msty-AUR](https://github.com/y-not-u/Msty-AUR)

在编写完成 `PKGBUILD` 之后，需要执行命令生成 `.SRCINFO` ：

```bash
makepkg --printsrcinfo > .SRCINFO

# 本地测试安装
makepkg -si
```

> 每次更新 `PKGBUILD` 都需要重新生成 `.SRCINFO`

一切没问题后提交到仓库。

## 提交到 AUR 仓库

Arch Linux 除了强大的 AUR 之外，另一个最大亮点即是它友好的文档。我在中文官方文档上找到了提交到 AUR 十分详细明了的步骤。

[AUR 提交准则 -  Arch Linux 中文维基](https://wiki.archlinuxcn.org/wiki/AUR_提交准则)

我总结了几个重要的步骤：

1. 注册 [archlinux.org](http://archlinux.org) 的账户
2. 添加 ssh key 到账户里
3. git clone 想要提交的包地址，例如想创建的包名是 `awesome-example` 则是
   `git clone ssh://aur@aur.archlinux.org/awesome-example.git`

   因为没有这个包而会自动创建一个空的仓库，你需要把提交的文件都放入其中。

4. git push 前需要配置好 ssh Host User，不然推送验证不匹配

最后检查成果：

```bash
yay msty-bin
# 1 aur/msty-bin 1.0.3-1 (+0 0.00) (Installed)
#     The easiest way to use local and online AI models
# ==> Packages to install (eg: 1 2 3, 1-3 or ^4)
```

AUR 网址为：[https://aur.archlinux.org/packages/msty-bin](https://aur.archlinux.org/packages/msty-bin)

