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

- 检测服务器所支持的请求方法
- CORS中的预检请求

### allowdMethods的作用

- 响应options方法，告诉它所支持的请求方法
- 相应的返回405（不允许） 和501（没实现）

```js
app.use(usersRouter.allowedMethods())
```

## RESTful API最佳实践--增删改查应该返回什么响应?

```js

router.get('/', (ctx) => {
    ctx.body = 'home page'
})

usersRouter.get('/', (ctx) => {
    ctx.body = [{
        name: 'lilei'
    }, {
        name: 'hanmeimei'
    }]
})
usersRouter.post('/', (ctx) => {
    ctx.body = {
        name: 'lilei'
    }
})
usersRouter.get('/:id', (ctx) => {
    ctx.body = {
        name: 'lilei'
    }
})
usersRouter.put('/:id', (ctx) => {
    ctx.body = {
        name: 'lilei2'
    }
})
usersRouter.delete('/:id', (ctx) => {
    ctx.status = 204;
})
```

## 控制器简介

### 什么是控制器

- 拿到路由分配的任务，并执行
- 在Koa中，是一个中间件

### 为什么要用控制器

- 获取HTTP请求参数

  - Query String 如?q=keyword
  - Router Params 如：/users/:id
  - Body,如{name："李雷"}
  - Header，如Accept，Cookie

- 处理业务逻辑

- 发送HTTP响应

  - 发送Status，如200/400等

  - 发送Body，如{name："李雷"}

  - 发送Header ，如Allow，Content-type

    ```js
    ctx.set('Allow','GET POST')
    ```

    ```js
    //内存数据
    const db = [{name:'lilei'}]
    
    router.get('/', (ctx) => {
        ctx.body = 'home page'
    })
    
    usersRouter.get('/', (ctx) => {
        ctx.body = db
    })
    usersRouter.post('/', (ctx) => {
        // ctx.set('Allow','GET POST')
        db.push(ctx.request.body)
        ctx.body = ctx.request.body
    })
    usersRouter.get('/:id', (ctx) => {
    
        ctx.body = db[ctx.params.id*1]
    })
    usersRouter.put('/:id', (ctx) => {
        db[ctx.params.id*1] = ctx.request.body
        ctx.body =  ctx.request.body
    })
    usersRouter.delete('/:id', (ctx) => {
        db.splice(ctx.params.id*1,1)
        ctx.status = 204;
    })
    ```

    

### 编写控制器最佳实践

- 每个资源的控制器放在不同的文件里
- 尽量使用类+类方法的形式编写控制器
- 严谨的错误处理

## 更合理的目录结构

- 将路由单独放在一个目录
- 将控制器单独放在一个目录
- 使用类+类方法的方式组织控制器 

## 错误处理

- 编程语言或计算机硬件里的一种机制
- 处理软件或信息系统中出现的异常状况

### 异常状况有哪些

- 运行时错误，都返回500
- 逻辑错误，如找不到（404）、先决条件失败（412），无法处理实体（参数格式不对422）等

### 为什么要用错误处理

- 防止程序挂掉
- 告诉用户错误信息
- 便于开发者调试

### Koa自带的错误处理

- 制造404、412、500三种错误
- 了解Koa自带的错误处理做了什么

### 自己编写错误处理中间件

- 自己编写错误处理中间件
- 制造404/412/500三种错误测试

### 使用koa-json-error进行错误处理

- 安装koa-json-error

  ```js
  npm i koa-json-error --save
  ```

  

- 使用koa-json-error的默认配置处理错误

- 修改配置使其在生产环境下禁用错误堆栈返回

```js
//设置环境变量
npm i cross-ene --save-dev
//package.json
"start": "cross-env NODE_ENV=production nodemon app",
"dev":"nodemon app"
//index.js
const error = require('koa-json-error')
app.use(error({
    postFormat: (e, { stack, ...rest }) => process.env.NODE_ENV === 'production' ? rest : { stack, ...rest }
}))
```

### 使用koa-parameter校验参数

- 安装koa-parameter
- 使用koa-parameter校验参数

```
cnpm i koa-parameter --save
```

> 通常用于校验请求体

```js
//index.js
app.use(bodyparser()) 
app.use(parameter(app))
//controllers/users.js
create(ctx) {
    ctx.verifyParams({
        name: { type: 'string', required: true },
        age: { type: 'number', required: false }
    });
    db.push(ctx.request.body)
    ctx.body = ctx.request.body
}
```

## NoSQL数据库分类

- 列存储（HBase）
- 文档存储（MongoDB）
- Key-Value存储（Redis）
- 图存储（FlockDB）
- 对象存储（db4o）
- XML存储（BaseX）

### 为什么要用NoSQL

- 简单（没有原子性，一致性，隔离性等复杂规范）
- 便于横向扩展
- 适合超大规模的数据的存储
- 很灵活地存储复杂结构的数据（Schema Free）

### MongoDB

- 面向文档存储的开源数据库
- 由C++编写而成

#### 为什么要用MongoDB

- 性能好（内存计算）
- 大规模数据存储（可拓展性）
- 可靠安全（本地复制，自动故障转移）
- 方便存储复杂数据结构（Schema Free）

### 云数据库MongoDB Atlas

- 注册用户
- 创建集群
- 添加数据库用户
- 设置IP地址白名单
- 获取连接地址

```
mongodb+srv://yongshuang:<password>@koa-jgtfu.mongodb.net/test?retryWrites=true&w=majority
```

### 用Mongoose连接MongoDB

- 安装Mongoose

  ```js
  npm i mongoose --save
  ```

  

- 用Mongoose连接MongoDB

### 设计用户模块的Schema

- 分析用户模块的属性
- 编写用户模块的Schema
- 使用Schema生成用户Model

```js
//models/users.js
const mongoose = require('mongoose')
const { Schema,model } = mongoose;
const userSchema = new Schema({
    name: { type: String, required: true },
    age: { type: Number, required: false, default: 0 }
})

module.exports = model('User',userSchema)
```

### 使用MongoDB实现用户的增删改查

- 用Mongoose实现增删改查接口
- 用Postman测试接口

```js

const User = require('../models/users')
class UsersCtl {
    async find(ctx) {
        ctx.body = await User.find()
    }
    async findById(ctx) {
        const user = await User.findById(ctx.params.id)
        if (!user) { ctx.throw(404) }
        ctx.body = user
    }
    async create(ctx) {
        ctx.verifyParams({
            name: { type: 'string', required: true }
        });
        const user = await new User(ctx.request.body).save()
        ctx.body = user

    }
    async update(ctx) {
        ctx.verifyParams({
            name: { type: 'string', required: true }
        });
        const user = await User.findByIdAndUpdate(ctx.params.id, ctx.request.body)
        if (!user) {
            ctx.throw(404, '用户不存在')

        }
        ctx.body = user
    }
    async delete(ctx) {
        const user = await User.findByIdAndRemove(ctx.params.id)
        if (!user) {
            ctx.throw(404, '用户不存在')
        }
        ctx.status = 204;
    }
}

module.exports = new UsersCtl()
```

## Session简介

### Session的优势

- 相比JWT，最大的优势就在于可以主动清除session
- session 保存在服务器端，相对较为安全
- 结合cookie使用，较为灵活，兼容性较好

### Session的劣势

- cookie+session在跨域场景表现并不好
- 如果是分布式部署，需要做多机共享session机制
- 基于cookie的机制很容易被
- 查询session信息可能会有数据库查询操作

### Session相关的概念介绍

- session：主要放在服务器端，相对安全
- cookie：主要放在客户端，并且不是很安全
- sessionStorage：仅在当前会话下有小，关闭页面或浏览器 后被清除
- localStorage：除非被清除，否则永久保存