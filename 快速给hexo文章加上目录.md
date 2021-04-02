---
title: 快速给hexo文章加上目录
date: 2021-03-01 19:19:03
tags:
- Hexo
categories:
- 博客搭建
---

<!-- toc -->

## 1.环境准备

首先安装hexo-toc插件：

```
npm install hexo-toc --save
```
## 2.插件配置

之后在你文章的站点文件夹的`_config.yml`最后加上如下代码开启配置：

```java
toc:
  maxdepth: 3
  class: toc
  slugify: transliteration
  decodeEntities: false
  anchor:
    position: after
    symbol: '#'
    style: header-anchor
```

## 3.开启使用

最后在你想要使用目录的文章内容的前面加上一下代码即可开启一个十分简易的目录，效果如本文所示。

```java
<!-- toc -->
```

## 4.注意事项

后来发现不做第三步也能开启目录，需要注意到是如果你的目录上加了颜色，那无法自动跳转到该章节。