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
## 使用State Hook
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
## 使用Effect Hook
可以在函数组件中执行副作用操作（数据获取，设置订阅以及手动更改React组件中的DOM都属于副作用），可以吧useEffect看作是:componentDidMount,componentDidUpdate和componentWillUnMount这三个函数的组合。
- useEffect做了什么？
告诉React组件需要在渲染后执行某些操作，React会保存传递的参数（称之为“effect”），并且在执行DOM更新之后调用它。
- 为什么在组件内部调用useEffect?
将useEffect放在组件内部让我们可以在effect中直接访问state变量（或其他props）。不需要特殊的API来读取——已经保存在函数作用域中。Hook使用了Javascript的闭包机制，而不用在JavaScript已经提供了解决方案的情况下，还引入特定的React API。
- useEffect会在每次渲染后都执行吗？
默认情况下，在第一次渲染之后和每次更新之后都会执行。React保证了每次运行effect的同时，DOM已经更新完毕。    
使用useEffect调度的effect**不会阻塞**浏览器更新屏幕
#### 需要清除的effect
- 使用class的实例
```javascript
  class FriendStatus extends React.Component {
    constructor(props) {
      super(props)
      this.state = { isOnline: null, count: 0 }
      this.handleStatusChange = this.handleStatusChange.bind(this)
    }

    componentDidMount() {
      document.title = `You clicked ${this.state.count} times`
      ChatAPI.subscribeToFriendStatus(
        this.props.friend.id,
        this.handleStatusChange
      )
    }

    componentWillUnmount() {
      ChatAPI.unsubscribeFromFriendStatus(
        this.props.friend.id,
        this.handleStatusChange
      )
    }

    componentDidUpdate() {
      document.title = `You clicked ${this.state.count} times`
    }

    handleStatusChange(status) {
      this.setState({ isOnline: status.isOnline })
    }

    render() {
      if (this.state.isOnline === null) {
        return 'Loading...'
      }

      return this.state.isOnline ? 'Online' : 'OffLine'
    }
  }
```
- 使用Hook实例
```javascript
  import React, { useState, useEffect } from 'react'

  function FriendStatus(props) {
    const [isOnline, setIsOnline] = useState(null)

    useEffect(() => {
      function handleStatusChange(status) {
        setIsOnline(status,isOnline)
      }

      ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange)
      // Specify how to clean up after this effect

      return function cleanUp() {
        ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange)
      }
    })

    if (isOnline === null) {
      return 'Loading...'
    }

    return isOnline ? 'Online' : 'OffLine'
  }
```
- 为什么要在effect中返回一个函数？
effect可选的清除机制。每个effect都可以返回一个清除函数。
- React何时清除effect？
在组件卸载是执行清除操作。
#### 使用Effect的提示
会在调用一个新的effect之前对前一个effect进行清理
- 通过跳过Effect进行性能优化
每次渲染后都会执行清理或者执行effect可能会导致性能问题。在class组件中，可以通过componentDidUpdate中添加prevProps和prevState的比较逻辑解决
```javascript
  componentDidUpdate(prevProps, prevState) {
    if (prevState.count !== this.state.count) {
      document.title = `You clicked ${this.state.count} times`
    }
  }
```
如果某些特定值在两次重渲染之间没有发生变化，可以通知React跳过对effect的调用，只要传递数组作为useEffect的第二个可选参数即可。
```javascript
  useEffect(() => {
    document.title = `You clicked ${this.state.count} times`
  }, [count]) // 仅在count更新时更新
```
