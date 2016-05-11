title: 使用淘宝 NodeJS 镜像加速 Electron Node-Sass 的安装速度
date: 2016-05-10 22:30:24
tags: NodeJS
category: Frontend
---

在使用了 cnpm 或者配置了 npmrc 中的registry 和 disturl 后，npm 包本身的下载速度虽然不错了，不过还是有一些特别的包安装速度感人，如 Electron-prebuilt 和 node-sass，这两个包都有一些 install 或者 postinstall 脚本，这些脚本会下载一些二进制文件，而默认都是从 Github 下载的，幸运的是它们都有环境变量的配置来更改默认的下载地址。

## 配置 npm

由于我不想单独再使用 cnpm 来安装模块，直接在 npmrc 中添加一些配置，也可以达到 cnpm 的效果：

```rc
# .npmrc
registry=https://registry.npm.taobao.org/
disturl=https://npm.taobao.org/dist
```

## Electron-download 与 node-sass

Electron-prebuilt使用 electron-download 来下载 electron 二进制文件并默认缓存在 HOME/.electron/ 目录下。

electron-download 模块 index.js 中 download 函数构建 url 的时候就优先检查环境环境 ELECTRON_MIRROR:

```js
var url = process.env.NPM_CONFIG_ELECTRON_MIRROR ||
    process.env.ELECTRON_MIRROR ||
    opts.mirror ||
    'https://github.com/electron/electron/releases/download/v'
  url += process.env.ELECTRON_CUSTOM_DIR || opts.customDir || version
  url += '/'
```


node-sass 模块 extension.js 中的 getBinaryUrl 则会检查环境变量 SASS_BINARY_SITE:

```js
function getBinaryUrl() {
  var site = getArgument('--sass-binary-site') ||
             process.env.SASS_BINARY_SITE  ||
             process.env.npm_config_sass_binary_site ||
             (pkg.nodeSassConfig && pkg.nodeSassConfig.binarySite) ||
             'https://github.com/sass/node-sass/releases/download';

	return [site, 'v' + pkg.version, getBinaryName()].join('/');
}
```

## 淘宝镜像

最后，添加环境变量，或者按照[淘宝镜像](https://npm.taobao.org/mirrors)上的方法：

```bash
export ELECTRON_MIRROR="https://npm.taobao.org/mirrors/electron/"
export SASS_BINARY_SITE="https://npm.taobao.org/mirrors/node-sass"
```

## 当然，更简单的办法则是直接上全局代理
