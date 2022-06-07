---
title: 如何得到一个干净的shinyBoard文件
date: 2022-06-07 17:40:27
categories: R
tags: private
---

使用shinyBoard的时候所有的东西都写在同一个app.r里面实在是丑陋

查了官方的问文档和教程都是写在app.r里面的

后来找个一个方法 可以把server和ui的文件放在app.r所在的目录下，rstudio会自动检测。

虽然还是很丑，没有模块化开发的感觉。