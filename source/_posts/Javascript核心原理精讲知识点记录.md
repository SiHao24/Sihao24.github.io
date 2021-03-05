---
title: Javascript核心原理精讲知识点记录
date: 2021-03-02 09:54:05
tags: javascript核心知识
---
### javascript的数据类型
  - undefined
  - null
  - Boolean
  - Number
  - String
  - Symbol
  - BigInt
  - Object
    - Array
    - RegExp
    - Date
    - Math
    - Function
前**7**种为基础类型，最后一种（Object）为引用类型
1）**基础类型**存储在**栈内存**，被引用或拷贝是，会创建一个完全相等的变量
2）**引用类型**存储在**堆内存**，存储的是地址，多个引用指向同一个地址，这里会涉及一个**共享**的概念
### 数据类型的检测
  - 1、typeof
    基础类型没什么问题，typeof null返回object，引用类型Object，用typeof来判断的话，除了function会判断为OK以外，其余都是‘object’，是无法判断出来的。
  - 2、instanceof