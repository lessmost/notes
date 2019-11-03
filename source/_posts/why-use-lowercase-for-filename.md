---
title: 为什么 Google 代码规范推荐使用小写作为文件名
date: 2019-11-03 23:29:11
category: note
tags:
---

[Google 代码规范关于文件名部分](https://developers.google.com/style/filenames)写到：

> Make file and directory names lowercase. In general, separate words with hyphens, not underscores. Use only standard ASCII alphanumeric characters in file and directory names.

也就是推荐使用`my-useful-class.js`这样的命名规则，而不是`MyUsefulClass.js`这样的规则，我自己之前一直习惯后一种的规则，同时也在 OOP 语言中保持文件名和类名一样的规则，最近在项目中讨论文件名规范的时候看了下 Google 的规范，开始想使用小写加`-`命名文件背后有没有什么原因，还是只是一个喜好问题？自己想了一些可能的原因：

- 不同文件系统对文件大小写敏感不一样：比如在 MacOS 的 HFS+系统中同一目录下就不能有 README.md 和 readme.md，而在大多数 Linux 中的文件系统中都是可以的，这样如果文件名大小写都有，就很可能在有些文件系统从存在问题
- 网络地址 URL 的大小写敏感性：URL 中的域名是大小写不敏感的；PATH 路径部分则比较复杂，不同服务器的实现可能不同，将源代码通过 Web 服务访问如果设计大小写也要求服务器对 PATH 部分做到大小写敏感
- 在 Git 中转换文件大小写还算是比较麻烦的
