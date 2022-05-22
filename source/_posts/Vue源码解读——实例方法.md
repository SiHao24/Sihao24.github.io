---
title: Vue学习记录
date: 2021-08-16 11:22:05
tags: Vue实例方法
---
- vm.$set(obj, key, val)
vm.$set用于向响应式对象添加一个新的property，并确保这个新的property同样是响应式的，并触发试图更新。由于Vue无法探测对象新增属性或者通过索引为数组新增一个元素，比如：this.obj.newProperty = 'val'，this.arr[3] = 'val'，所以才有了vm.$set,他是Vue.set的别名
  - 为对象添加一个新的响应式数据：调用defineReactive方法为对象增加响应式数据，然后执行dep.notify进行依赖通知，更新视图
  - 为数组添加一个新的响应式数据，通过splice方法实现

- vm.$delete(obj, key)做了什么？
  vm.$delete用于删除对象上的执行属性。如果对象是响应式的，且能确保触发视图更新。该方法主要用于避开Vue不能检测属性被删除的情况。它是Vue.delete的别名
  - 删除数组指定下表的元素，内部通过splice方法来完成
  - 删除对象上的指定属性，则是先通过delete运算符删除该属性，然后执行dep.notigy进行依赖通知，更新视图


- vm.$watch(expOrFn, callback, [options])做了什么？
  vm.$watch负责观察Vue实例上的一个表达式或者一个函数计算结果的变化，当恰发生变化时，回调函数就会被执行，并为回调函数传递两个参数，第一个为更新后的新值，第二个为旧值

  这里要注意一点的是：如果观察的是一个对象，比如：数组，当你用数组的方法，比如push为数组新增一个元素时，回调函数被触发时传递的新值和旧值相同，因为它们指向同一个引用，所以在观察一个对象并且在回调函数中的新老值是否相仿的判断是需要注意

  vm.$watch的第一个参数直接受接单的响应式数据的键路径，对于复杂的表达式建议使用函数作为第一个参数

  至于vm.$watch的内部原理是：
    - 设置options.user = true,标志是一个用户watcher
    - 实例化一个watcher实例，当检测到数据更新时，通过watcher去触发回调函数的执行，并传递新老值作为会回调函数的参数
    - 返回一个unwatch函数，用于取消观察


- vm.$on(event, callback)做了什么？
  监听当前实例上的自定义事件，事件可由vm.emit触发，回调函数会接受所有传入事件触发函数(vm.emit)的额外参数
  vm.$on的原理很简单，就是处理传递的event和callback两个参数，将注册的事件和回调函数以键值对的形式存储到vm._event对象中，vm._events = { eventName: [cb1, cb2, ...], ...}


- vm.$emit(eventName, [...args])做了什么？
  触发当前实例上指定的事件，附加参数都会传递给事件的回调函数
  其内部原理就是执行vm._events[eventName]中所有的回调函数

  > 从$on和$emit的实现原理也能看出，组件的自定义事件其实是谁触发谁监听，所以在这会再回头看Vue源码解读（2）——Vue初始化过程中关于initEvent的解释就会明白在说什么，因为组件自定义事件的处理内部用的就是vm.$on,vm.$emit


- vm.$off([event, callback])做了什么？

  移除自定义事件监听器。即移除vm._events对象上的相关数据
  - 如果没有提供参数，则移除实例的所有事件监听
  - 如果只提供了event参数，则移除实例上该事件的所有监听器
  - 如果两个参数都提供了，则移除实例上该事件对应的监听器


- vm.$once(event, callback)做了什么？
  监听一个自定义事件，但是该事件只会被触发一次，一旦触发以后监听器就会被移除
  其内部的原理是：
    - 包装用户传递的回调函数，当包装函数执行的时候，除了会助兴用户回调函数之外还会执行vm.$off(event,包装函数)移除该事件
    - 用vm.$on(event, 包装函数)注册事件

- vm._update(vnode,hydrating)做了什么？
  用于源码内部的实例方法，负责更新页面，是页面渲染的入口，其内部根据是否存在preVnode来决定是首次渲染还是页面更新，从而调用__patch__函数时传递不同的参数。


- vm.$forceUpdate()做了什么？
  迫使Vue实例重新渲染，它仅仅影响组件实例本身和插入插槽内容的子组件，而不是所有的子组件。其内部原理也很简单，就是直接调用vm._watcher.update()，它就是watcher.update()方法，执行该方法触发组件更新



- vm.$destroy()做了什么？
  完全销毁一个实例。清理他与其他实例的链接，解绑他的全部指令和事件监听器，在执行过程中会调用beforeDestroy和destroy两个钩子函数，在大多数业务开发场景下用不到该方法，一般都通过v-if指令来操作，其内部原理就是：
    - 调用beforeDestroy钩子函数
    - 将自己从$parent移除，从而销毁和$parent的关系
    - 通过watcher.teardown()来移除依赖监听
    - 通过vm.__patch__(vnode, null)方法来销毁节点
    - 调用destroyed钩子函数
    - 通过vm.$off方法来移除所有的事件监听
  

- vm.$nextTick(cb)做了什么？
  cm.$nextTick是Vue.nextTick的别名，起作用是延迟回调函数cb的执行，一般用于this.key = newVal更改数据后，想立即获取更改后的DOM数据
  其内部的执行过程是：
    - this.key = 'ne val'，触发依赖通知更新，将负责更新的watcher放入watcher队列
    - 将刷新watcher队列的函数放到callbacks数组中
    - 在浏览器的异步任务队列中放入一个刷新callbacks数组的函数
    - vm.$nextTick(cb)来插队，将cb函数放入callbacks数组
    - 待将来的某个时刻执行刷新callbacks数组的函数
    - 然后执行callbacks数组中的众多函数，触发watcher.run的执行，更新DOM
    - 由于cb函数是在后面放到callbacks的数组，所以就保证了先完成的DO吗更新，再执行cb函数



- vm._render做了什么？
  它是一个用于源码内部的实例方法，负责生成vnode。其关键代码就一行，执行render函数生成vnode，不过其中加了大量的异常处理代码
