---
title: Vue知识点收集
date: 2021-01-11 14:48:05
tags: Vue
---
- 1、Vue-router路由有几种模式？区别是什么？
  * hash模式
    1、url路径会出现"#"字符
    2、hash值不包括在http请求中，它是交由前端路由处理，所以hash改变值是不会刷新页面，也不会向服务器发送请求
    3、hash值的改变会触发hashchange事件
  * history模式
    1、整个地址重新加载，可以保存历史记录，方便前进后退
    2、依赖H5 API和后台配置，没有后台配置的话，页面刷新时会出现404
    3、提供pushState和replaceState方法