# React

[TOC]

## 组件化

### 对组件化的理解

- 组建的封装
  - 封装视图
  - 数据
  - 变化逻辑（数据驱动视图变化）
- 组件的复用
  - props传递
  - 复用

### JSX本质是什么

#### JSX语法

- html形式
- 引入JS变量表达式
- if...else... 
- 循环
- style和className
- 事件
- JSX语法根本无法被浏览器所解析
- 那么它如何在浏览器运行？

#### JSX解析成JS

- JSX其实是语法糖
- 开发环境会将JSX编译成JS代码
- JSX的写法大大降低了学习成本和编码工作量
- 同时，JSX也会增加debug成本

#### JSX独立的标准

- JSX是React引入的，但不是React独有的
- React已经将它作为一个独立标准开放，其他项目也可以用
- React.createElement是可以自定义修改的
- 说明：本身功能已经完备；和其他标准兼容和扩展性没问题

### JSX和vdom什么关系

- 分析：为何需要vdom
  - vdom是React初次推广开来的，结合JSX
  - JSX就是模板，最终要渲染成html
  - 初次渲染+修改state后的re-render
  - 正好符合vdom的应用场景
- React.createElement和h函数
- 何时patch？
  - 初次渲染-ReactDOM.render(<App/>, container)
  - 会触发patch（container，vnode）
  - re-render-setState
  - 会触发patch(vnode,newVnode)
- 自定义组件的解析
  - 'div'-直接渲染<div>即可，vdom可以做到
  - Input和List，是自定义组件（class），vdom默认不认识
  - 因此Input和List定义的时候，必须声明render函数
  - 根据props初始化实例，然后执行实例的render函数
  - render函数返回的还是vnode对象

### setState的过程

- setState的异步
- vue修改属性也是异步
- setState的过程
  - 每个组件实例，都有renderComponent方法
  - 执行renderComponent会重新执行实例的render
  - render函数返回newVnode，然后拿到preVnode
  - 执行patch(preVnode,newVnode)

#### setState为何需要异步？

- 可能会一次执行多次setState
- 你无法规定，限制用户如何使用setState
- 没必要每次setState都重新渲染，考虑性能
- 即便是每次重新渲染，用户也看不到中间的效果
- 只看到最后的结果即可

### 简述React的setState

### 阐述自己如何比较react和vue

- 两者的本质区别
  - vue-本质是MVVM框架，由MVC发展而来
  - React-本质是前端组件化框架，由后一段组件化发展而来
- 看模板和组件化的区别
  - vue-使用模板（最初由angular提出）
  - React-使用JSX
  - 模板语法上->JSX
  - 模板分离上->vue
  - 组件化的区别
    - React本身就是组件化，没有组件化就不是React
    - vue也支持组件化，不过是在MVVM上的扩展
- 两者的共同点
  - 都支持组件化
  - 都是数据驱动和视图

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

