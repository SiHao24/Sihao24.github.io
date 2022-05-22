---
title: Vue学习记录
date: 2021-08-16 11:22:05
tags: Vue全局API
---
- Vue.use(plugin)做了什么？
  - 首先判断该插件是否已经安装过
  - 如果没有，则执行插件提供的install方法安装插件，具体做什么由插件自己决定

- Vue.mixin(options)做了什么？
  - 负责在Vue的全局配置上合并options配置，然后在每个组件生成vnode时会讲全局配置合并到组件自身的配置上来
    - 标准化options对象上的props，inject，directive选项的格式
    - 处理options上的extends和minxins，分别将他们合并到全局配置上
    - 然后将options配置和全局配置进行合并，选项冲突时options配置会覆盖全局配置

- Vue.component(compName, Comp)做了什么？
  - 负责注册全局组件，其实就是将组建配置注册到全局配置的components选项上(options.components)，然后各个子组件在生成vnode时会讲全局的components选项合并到局部的components配置项上
  - 如果第二个参数为空，则表示获取compName的组件构造函数
  - 如果Comp是组件配置对象，则使用Vue.extend方法得到组件构造函数，否则直接进行下一步
  - 在全局配置上设置组件信息，this.options.components.compName = ComConstructor


- Vue.directive('my-directive', { xx })做了什么？
在全局注册my-directive指令，然后每个子组件在生成vnode时会讲全局的directives选项合并到局部的directives选项中，原理同Vue.component方法
- 如果第二个参数为空，则获取指定指令的配置对象
- 如果不为空，如果第二个参数是一个函数的话，则生成配置对象{bind: 第二个参数，update:第二个参数}
- 然后将指令配置对象设置到全局配置上this.options.directives['my-directive'] = {xx}

- Vue.filter('my-filter', function(val) {xx})做了什么？
负责在全局注册过滤器my-filter，然后每个子组件在生成vnode时会讲全局的filters选项合并到局部的filters选项中，原理是：
  - 如果没有提供第二个参数，则获取my-filter过滤器的回调函数
  - 如果提供了第二个参数，则设置this.options.filters['my-filter'] = function(val) { xx}


- Vue.extend(options)做了什么？
Vue.extend基于Vue创建一个子类，参数options会作为该子类的默认全局配置，就像Vue的默认全局配置一样，所以通过Vue.extend扩展一个子类，一大用处就是内置一些公共配置，共子类使用
  - 定义子类构造函数，这里和Vue一样，也是调用_init(options)
  - 合并Vue的配置和options，如果选项冲突，则options的选项会覆盖Vue的配置项
  - 给子类定义全局的API，值为Vue的全局API，比如Sub.extend = Super.extend，这样子类同样可以扩展出其他子类
  - 返回子类Sub

- Vue.set(target, key, val)做了什么？
由于Vue无法探测普通新增property（比如：this.muObject.newProperty = 'hi'），所以通过Cue.set为向响应式对象中添加一个property，可以确保这个新的property同样是响应式的，且触发试图更新
  - 更新数组指定下表的元素Vue.set(array, idx, val)，内部通过splice方法实现响应式更新
  - 更新对象已有属性，Vue.set(obj, key, val)直接更新即可=>obk[key] = val
  - Vue.set(obj, key, val)，如果obj不是响应式对象，会执行obj[key] = val,但不会做响应式处理
  - Vue.set(obj, key, val)为响应式对象obj增加一个新的key，则通过defineReactive方法设置响应式，并触发依赖更新

- Vue.delete(target, val)做了什么？
删除对象的property，如果对象是响应式的，确保删除能触发更新视图，这个方法主要用于避开Vue不能检测到property被删除的限制，但是你应该很少会使用它，当然同样不能删除跟级别的响应式属性

  - Vue.delete(array, idx),删除指定下表的元素，内部通过splice方法实现
  - 删除响应式对象上的某个属性，Vue.delete(obj, key),内部是执行delete obj.key，然后执行依赖更新即可

- Vue.nextTick(cb)做了什么？
Vue.nextTick(cb)犯法的作用是延迟回调函数cb的执行，一般用于this.key = newVal更改数据后，想立即获取更改过后的DOM数据
其内部的执行过程
  - this.key = 'new val'触发依赖通知更新，将负责更新的watcher放入watcher队列
  - 将刷新watcher队列的函数放到callbacks数组中
  - 在浏览器的异步任务队列中放入一个刷新callbacks数组的函数
  - Vue.nextTick(cb)来插队，将cb函数放入callbacks数组
  - 待将来的某个时刻执行刷新callbacks数组的函数
  - 然后执行callbacks数组中的众多函数，触发watcher.run执行，更新DOM
  - 由于cb函数是在后面放到callbacks数组，所以这就保证了先完成的DOM更新，在执行cb函数
