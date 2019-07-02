# react

[TOC]



## 组件化

### 对组件化的理解

### jsx是什么

### jsx和vdom什么关系

### 简述React的setState

### 阐述自己如何比较react和vue

## react 生命周期函数

- 初始化阶段
  - getDefaultProps: 获取实例的默认属性
  - getInitialState:获取每个实例的初始化状态
  - componentWillMount：组件即将被装载，渲染到页面上
  - render：组件在这里生成虚拟的DOM节点
  - componentDidMount:组件真正在被装载之后

- 运行中状态：
  - componentWillReceiveProps:组件将要接收到属性的时候调用
  - shouldComponentUpdate：组件接收到新属性或者新状态的时候
  - componentWillUpdate:组件即将更新不能修改属性和状态
  - render：组件重新描绘
  - componentDidUpdate:组件已经更新

- 销毁阶段：
  - componentWillUnmount:组件即将被销毁

## redux

