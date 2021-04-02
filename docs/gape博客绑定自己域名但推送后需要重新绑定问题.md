---
layout: 解决hexo搭载github
title: gape博客绑定自己域名但推送后需要重新绑定问题
date: 2021-03-02 15:07:48
tags:
---

## 1.问题描述

使用hexo将自己博客搭载在github上时，如果自己有单独的域名且希望使用该域名访问自己的博客，需要在github的博客项目下设置选项中的`GitHub Pages`填写自己的域名并保存。

![alt](1.png)

但可能碰到每次你执行 `hexo d`操作之后都需要重新去绑定，那是因为推送后的hexo代码自动把CNAME文件清楚了。

## 2.解决方法

将存放自己域名的`CNAME`无格式文件放到本地hexo项目的`source`文件夹下，重新generate一遍之后再向github推送即可。