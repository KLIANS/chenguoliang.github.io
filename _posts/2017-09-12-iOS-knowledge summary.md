---
layout: post
title:  "iOS知识点大总结"
date: 2017-09-12
desc: "iOS知识点大总结"
keywords: "iOS"
categories: [iOS]
tags: [iOS,Objective-C]
icon: icon-html
---

记录一些常用和不常用的iOS知识点，防止遗忘丢失。（来源为收集自己项目中用到的或者整理看到博客中的知识点），如有错误，欢迎大家批评指正；如有好的知识点，也欢迎大家联系我，添加上去。谢谢！

***

> 一、调用代码使APP进入后台，达到点击Home键的效果。（私有API）

    [[UIApplication sharedApplication] performSelector:@selector(suspend)];

suspend的英文意思有：暂停; 悬; 挂; 延缓;


