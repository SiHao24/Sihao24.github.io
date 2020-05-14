---
layout: 学习React Hook
title: Hook
date: 2020-05-14 16:39:55
tags: React Hook
---
## Hook简介
React16.8新特性,可以再不编写class的情况下使用state以及其他的React特性。
- 完全可选
- 100%向后兼容
- 现在可用
<!-- more -->
### 动机
- 在组件只见服用状态逻辑很难 React需要为**共享状态逻辑**提供更好的原生途径。Hook使你在无需修改组件结构的情况下复用状态逻辑。
- 复杂组件变得难以理解 Hook将组件中相互关联的部分拆分成更小的函数（比如设置订阅或请求数据）
- 难以理解的class **this**工作机制 class不能很好的压缩，并且会使热重载出现不稳定的情况。
### 渐进策略
- 没有计划从React中移除class
## Hook概览
```javascript
  import React, { useState } from 'react'

  function Example() {
    const [count, setCount] = useState(0)

    return <>
      <div>
        <p>You clicked {count} times</p>
        <button onClick={() => setCount(count + 1)}>Click me</button>
      </div>
    </>
  }
```
useState会返回一对值：**当前**状态和一个更新当前状态的**函数**，可以在事件处理函数中或其他一些地方调用这个函数。类似于class组件中**this.setState**, 但是**不会把新的state和旧的state进行合并**。  
useState唯一的参数就是初始state。不同于this.state，这里的state不一定要是一个对象，完全取决于使用场景。初始state参数只有在第一次渲染时会被用到。
**数组解构**的语法让我们在调用usetState时可以给state变量取不同的名字。
- 什么是Hook？
Hook是一些可以让你在函数组件里“钩入”React state及生命周期等特性的函数。Hook不能在class组件中使用——这使得不使用class也能使用React。
- Hook使用规则
- 1.只能在**函数最外层**调用Hook 不能在循环、条件判断或者子函数中调用
- 2.只能在React的函数组件中调用Hook
Hook是一种复用状态逻辑的方式，它不复用state本身，事实上Hook的每次调用都有一个完全独立的state——可以在单个组件多次调用同一个自定义Hook
class中形式：
```javascript
  class Example extends React.Component {
    constructor(props) {
      super(props)
      this.state = {
        count: 0
      }
    }
  }
```
Hook方式：
```javascript
  import React, { useState } from 'react'

  function Example() {
    //  声明一个叫“count”的变量
    const [count, setCount] = useState(0)
  }
```
- 调用useState的方法时做了什么？
定义了一个“state变量”，变量名字自定义，它与class里面的this.state提供的功能完全相同，在函数退出后变量就会“消失”，而state中的变量会被React保留。
- useState需要那些参数？
唯一的参数就是初始state。类型根据需要自定义，可以是字符串、数字，对象。。。
- useState的方法返回值是什么？
当前state以及更新state的函数，和class里面this.state.count类似，唯一区别就是**需要成对的获取他们**
state只在组件首次渲染的时候被创建，在下一次重新渲染时，useState返回当前的state。
- 方括号的作用
**数组解构**第一个值是当前的state，第二个值是更新state的函数。
```javascript
  const [fruit, setFruit] = useState('banana')
  // 完全相同的功能
  const fruitStateVariable = useState('banana')
  const fruit = fruitStateVariable[0]
  const serFruit = fruitStateVariable[1]
```
this.setState中，更新state变量总是**替换**而不是合并它