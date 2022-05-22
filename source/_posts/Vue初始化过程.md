---
title: Vue初始化过程
date: 2022-01-06 09:24:59
tags: Vue
---

### Vue的初始化过程new Vue(options)都做了什么？

- 处理组件配置项
  - 初始化组件时进行了选项合并操作，将全局配置和冰岛跟组件的局部配置上
  - 初始化每个子组件时做了一些性能优化，将组件配置对象上的一些深层次属性放到vm.$options选项中，以提高代码的执行效率
- 初始化组件实例的关系属性，比如$parent,$children,$root,$refs
- 处理自定义事件
- 调用beforeCreate钩子函数
- 初始化组件的inject的配置项，得到ret[key] = val形式的配置对象，然后对该配置对象进行响应式处理，并代理每个key到vm实例上
- 数据响应式，处理props，methods，data，computed，watch等选项
- 解析组件配置项上的procide对象，将其挂载到vm._provided属性上
- 调用created钩子函数
- 如果发现配置项上有el选项，则自动调用$mount方法，也就是说有了el选项，就不需要手动调用$mount方法，反之，没提供el选项必须调用$mount
- 接下来则进入挂载阶段
