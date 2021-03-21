---
title: this及使用场景
date: 2021-03-20 18:06:57
tags: this
---
- 谈谈你对this的了解及使用场景？
  +this的五种情况分析：
    Q1：函数执行，看方法前面是否有"点"，没有“点”，this是window「严格模式下是undefined」，有“点”，“点”前面是谁this就是谁
    Q2:给当前元素的某个事件行为绑定方法，当事件行为触发，方法中的this就是当前元素本身
「排除attachEvent」
    Q3:构造函数中的this指向当前类的实例
    Q4:箭头函数中没有执行主体，所用到的this都是其所处上下文中的this
    Q5:可以基于Function.prototype上的call/apply/bind去改变this指向
    + 手撕call/bind源码
    + 掌握this的好玩应用：鸭子类型
```js
  // Q1
  const fn = function fn() {
    console.log(this)
  }

  let obj = {
    name: 'dreamer',
    fn: fn,
  }

  fn() // window 严格模式undefined
  obj.fn() // obj

  // Q2

  document.body.addEventListener('click', function () {
    console.log(this)
  })

  // Q3
  function Factory() {
    this.name = 'dreamer'
    this.age = 25
    console.log(this)
  }

  let f = new Factory()
  f()

  // Q4
  let demo = {
    name: 'dreamer',
    fn() {
      console.log(this)
      setTimeout(function() {
        console.log(this)
      }, 1000)

      setTimeout(() => {
        console.log(this)
      }, 1000)
    }
  }

  demo.fn()

  // Q5
  let obj = {
    name: 'dreamer',
  }
  function func(x, y) {
    console.log(this, x, y)
  }

  // func函数基于__proto__找到Function.prototype.call方法执行
  // 在call方法内部「call执行的时候」call(context => obj, ...params => [10, 20])
  //   + 吧function中的this改为obj
  //   + 并且吧params接受的值当作实参传递给func函数
  //   + 并且让func函数；立即执行
  func.call(obj, 10, 20)
  func.apply(obj, [10, 20])

  // func函数基于__proto__找到Function.prototype.bind,并把bind方法执行
  // 在bind内部，
    // 和call/apply的区别，并没有吧func立即执行
    // 把传递进来的obj/10/20存储起来「闭包」
    // 执行bind返回一个新的函数，例如：proxy，吧proxy绑定给元素的事件，当事件触发执行的是返回的proxy，在proxy内部，再去把func执行，把this和值都改变为之前存储的哪些内容
  document.body.addEventListener('click', func.bind(obj, 10, 20))
```
- call
```js
  // 原理：就是利用“点”定this机制，context.xxx = self "obj.xxx = func" => obj.xxx
  Function.prototype.call = function (context, ...params) {
    // func.call(obj, 10, 20)
    // this -> func context -> obj params -> [10, 20]
    let self = this
    let key = Symbol('Call')
    let result
    context === null ? context = window : null
    !/^(object|function)$/i.test(typeof context) ? context = Object(context) : null
    context[key] = self
    result = context[key](...params)

    delete context[key]
    return result
  }
```
- bind
```js
  Function.prototype.bind = function(context, ...params) {
    let self = this

    return function proxy(...args) {
      // 执行并且改变this指向 args -> 是执行proxy的时候kennel传递的值
      self.apply(context, params.concat(args))
    }
  }
```