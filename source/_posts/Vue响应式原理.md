---
title: Vue响应式原理
date: 2022-01-19 09:24:41
tags: Vue响应式
---
### Vue响应式原理是怎么实现的？
- 响应式的核心是通过**Object.defineProperty**拦截对数据的访问和设置
- 响应式的数据分为两类
  - 对象，循环遍历对象的所有属性，为每个属性设置getter,setter，以达到拦截访问和设置的目的，如果属性值依旧是对象，择地柜为属性值上的每个key设置getter，setter
    - 访问数据时（obj.key）进行依赖收集，在dep中存储相关的watcher
    - 设置数据时有dep通知相关的watcher去更新
  - 数组，增强数组的那七个可以更爱自身的原型方法，然后拦截对这些方法的操作
    - 添加新数据时进行响应式处理，然后由dep通知watcher去更新
    - 删除数据时，也要由dep通知watcher去更新

### methods、computed、watch有什么区别？
```html
<!DOCTYPE html>
<html lang="en">

<head>
  <title>methods、computed、watch 有什么区别</title>
</head>

<body>
  <div id="app">
    <!-- methods -->
    <div>{{ returnMsg() }}</div>
    <div>{{ returnMsg() }}</div>
    <!-- computed -->
    <div>{{ getMsg }}</div>
    <div>{{ getMsg }}</div>
  </div>
  <script src="../../dist/vue.js"></script>
  <script>
    new Vue({
    el: '#app',
    data: {
      msg: 'test'
    },
    mounted() {
      setTimeout(() => {
        this.msg = 'msg is changed'
      }, 1000)
    },
    methods: {
      returnMsg() {
        console.log('methods: returnMsg')
        return this.msg
      }
    },
    computed: {
      getMsg() {
        console.log('computed: getMsg')
        return this.msg + ' hello computed'
      }
    },
    watch: {
      msg: function(val, oldVal) {
        console.log('watch: msg')
        new Promise(resolve => {
          setTimeout(() => {
            this.msg = 'msg is changed by watch'
          }, 1000)
        })
      }
    }
  })
  </script>
</body>

</html>
```
- 使用场景
  - methods一般用于封装一些较为复杂的逻辑处理方式（同步、异步）
  - computed一般用于封装一些简单的同步逻辑，将经过处理的数据返回，然后显示在木板中，以减轻模板的重量
  - watch一般用于当需要在数据变化时执行异步或者开销较大的操作

- 区别
  - methods VS computed
  >> 通过实例会发现，如果在一次渲染中，由多个地方使用了同一个methods或computed属性，methods会被执行多次，而computed的回调函数则只会执行一次

  >> 在一次渲染中，多次访问computedProperty，只会在第一次执行computed属性的回调韩素，后续的其他访问，则直接使用第一次的执行结果（watch.value），而这一切的实现原理则是通过对watcher.dirty属性的控制实现的。而methods，每一次的访问则是简单的方法调用(this.xxMethods)
  
  - computed VS watch
    >> computed和watch而本质是一样的，内部都是通过Watcher来实现的，其实没什么区别，非要说区别的话就两点：1、使用场景上的区别，2、computed默认是懒执行，切不可更改

  - methods VS watch
    >> methods和watch之间其实没什么可比的，完全是两个东西，不过在使用上可以吧watch中的一些逻辑抽到methods中，提高代码的可读性
