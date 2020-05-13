---
title: 学习React
date: 2020-05-12 09:59:23
tags: react
---
### 高阶组件
(HOC)是React中用于复用组件的一种高级技巧。HOC自身不是ReactAPI的一部分，它是一种基于React的组合特性而形成的**设计模式**  
具体而言，**高阶组件是参数为组件，返回值为新组件的函数**，组件是将props转换成UI，而高阶组件是将组件转换成另一个组件。
<!-- more -->
需要一个抽象，允许在一个地方定义逻辑，并在许多组件之间共享它。这正是高阶组件擅长的地方。
HOC不会修改传入的组件，也不会使用继承来复制其行为。相反，HOC通过**将组件包装在容器组件来形成新组件**。HOC是纯函数，没有副作用。   
HOC不应该修改传入组件，而应该使用组合的方式，通过将组件包装在容器组件中实现功能:
```javascript
  function logProps(WrappedComponent) {
    return class extends React.Component {
      componentDidUpdare(preProps) {
        console.log('Current Props: ', this.props)
        console.log('Previous Props: ', preProps)
      }

      render() {
        return <WrappedComponent {...this.props} />
      }
    }
  }
```
- HOC与容器组件的相似之处： 
容器组件担任分离将高层和底层关注的责任，有容器管理订阅和状态，并将prop传递给处理渲染UI。HOC使用容齐作为其实现的一部分，可以讲HOC视为参数化容器组件。  
- 将不相关的props传递给被包裹的组件
- 最大化可组合性
- 包装显示名称以便轻松调试