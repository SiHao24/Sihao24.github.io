##  什么是Hook Event
  Hook Event是Vue的自定义事件结合生命周期狗子实现的一种从组件外部为组件注入额外生命周期方法的功能

## Hook Event是如何实现的？
  ```js
    <comp @hook:lifecyleMethod="method" />
  ```
  - 处理组件自定义事件的时候(vm.$on)如果发现组件有hook:xx格式的事件(xx为Vue的声明周期函数)，则将vm_.hasHookEvent置为true，表示该组件有Hook Event
  - 在组件生命周期方法被触发的时候，内部会通过 callHook方法来执行这些生命周期函数，在生命周期函数执行之后，如果发现vm_.hasHookEvent为true，则表示当前组件有Hook Event，通过vm.$emit('hook:xx')触发Hook Event的执行
