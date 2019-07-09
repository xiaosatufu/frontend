# Koa

- 基于Node.js：Node.js模块
- Web框架：不是命令行工具，不是算法
- 利用async函数，丢弃回调函数
- 增强错误处理：try catch
- 没有捆绑任何中间件

## 安装搭建Koa

- 初始化项目

  ```js
  npm init
  ```

  

- 安装Koa

  ```js
  npm install koa --save
  ```

  

- 编写Hello 

  ```js
  //index.js
  const Koa = require('koa')
  conat app = new Koa()
  app.use((ctx)=>{
  	ctx.body = 'hello'
  })
  app.listen(3000)
  ```

  

- 学会自动重启

  ```js
  npm i nodemon --save-dev
  //package.json
  "start":"nodemon index.js"
  ```

## Koa中间件与洋葱模型

### 学习async await

### 学习编写Koa中间件

### 学习洋葱模型

## 路由简介

- 决定了不同URL是如何被不同地执行的
- 在Koa中，是一个中间件

### 为什么要用路由

- 如果没有路由会怎么样？
  - 所有请求都做了相同的事
  - 所有请求都会返回相同的结果
- 路由存在的意义
  - 处理不同地URL
  - 处理不同地HTTP方法
  - 解析URL上的参数

### 编写Koa路由中间件

- 处理不同地URL
- 处理不同的HTTP方法
- 解析URL上的参数

```js
const Koa = require('koa')
const app = new Koa()
app.use((ctx)=>{
    // ctx.body = 'x2xx'
    if (ctx.url==='/') {
        ctx.body='home page'
    } else if(ctx.url==='/users') {
        if (ctx.method==='GET') {
            ctx.body='users list'
        } else if (ctx.method==='POST') {
            ctx.body='create user'
        } else {
            ctx.body='405 方法不允许'
        }
    } else if (ctx.url.match(/\/users\/\w+/)) {
        const userId = ctx.url.match(/\/users\/(\w+)/)[1]
        ctx.body = userId
    } else {
        ctx.body = '404'
    }
})

app.listen(3001)
```

### 使用Koa-router实现路由

- 更优雅的实现路由基本功能
- 高级路由功能，前缀，多个中间件

```js
npm i koa-router --save
```

```js
const Koa = require('koa')
const Router = require('koa-router')
const app = new Koa()
const router = new Router()
const usersRouter = new Router({prefix:'/users'})

//多中间件鉴权
const auth = async (ctx,next) =>{
    if (ctx.url !=='/users') {
        ctx.throw(401)
    }
    await next()
}

router.get('/',(ctx)=>{
    ctx.body='hom1e page'
})
 
usersRouter.get('/', auth,(ctx)=>{
    ctx.body='users list'
})

usersRouter.post('/', auth,(ctx)=>{
    ctx.body='create user'
})

usersRouter.get('/:id', auth,(ctx)=>{
    ctx.body=`user：${ctx.params.id}`
})

app.use(router.routes())
app.use(usersRouter.routes())


app.listen(3001)
```

## HTTP options 方法的作用是什么

