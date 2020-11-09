---
title: 深入浅出React
date: 2020-11-09 19:48:27
tags: React, React Hook
---
### useEffect():允许函数组件执行副作用操作
useEffect可以接受两个参数，分别是**回调函数**和**依赖数组**
```javascript
  useEffect(callback, [])
```
- 每一次渲染后都执行的副作用：传入回调函数，不传依赖数组
```javascript
  useEffect(callback)
```
