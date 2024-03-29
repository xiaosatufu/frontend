# Koa

- 基于Node.js：Node.js模块
- Web框架：不是命令行工具，不是算法
- 利用async函数，丢弃回调函数
- 增强错误处理：try catch
- 没有捆绑任何中件

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
  ```

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
  ```

 用Mongoose连接MongoDB

​```js
const mongoose = require('mongoose')
mongoose.connect(connectionStr,{ useNewUrlParser: true },()=>{
    console.log('MongoDB连接成功')
})
  ```



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

## JWT

### JWT简介

- JSON Web Token 是一个开放标准（RFC 7519）
- 定义了一种紧凑且独立的方式，可以将各方之间的信息作为JSON对象进行安全传输
- 该信息可以验证和信任，因为是经过数字签名的

### JWT构成

- 头部（Header）
  - typ：token的类型，这里固定为JWT
  - alg：使用的hash算法，例如：HMAC SHA256或者RSA 
- 有效载荷（Payload）
  - 存储需要传递的信息，如用户ID、用户名等
  - 还包含元数据，如过期时间，发布人等
  - 与Header不同，Payload可以加密
- 签名（Signature）
  - 对Header和Payload部分进行签名
  - 保证Token在传输的过程中没有被篡改或者损坏

### JWT vs. Session

- 可拓展性
- 安全性
- RESTful API、
- 性能
- 时效性

### 操作JWT

- 安装jsonwebtoken

  ```js
  npm install jsonwebtoken
  ```

- 签名

  ```js
  jwt = require('jsonwebtoken')
  token = jwt.sign({name:'lilei','secret'})
  
  ```

- 验证

  ```js
  jwt.verify(token,'secret')
  ```

  

## 实现用户注册

- 设计用户Schema
- 编写保证唯一性的逻辑

## 实现登录并获取Token

- 登录接口设计

- 用jsonwebtoken生成token

  ```js
  
  // controller/users.js
  const jsonwebtoken = require('jsonwebtoken')
  const User = require('../models/users')
  const { secret } = require('../config')
  async login(ctx) {
          ctx.verifyParams({
              name: { type: 'string', required: true },
              password: { type: 'string', required: true }
          })
          const user = await User.findOne(ctx.request.body)
          if (!user) {
              ctx.throw(401, '用户名或密码错误')
          }
          const { _id, name } = user
          const token = jsonwebtoken.sign({ _id, name }, secret, { expiresIn: '1D' })
          ctx.body = { token }
      }
  ```

  



## 编写Koa中间件实现用户认证与授权

- 认证：验证token，并获取用户信息
- 授权：使用中间件保护接口

[koa中间件实现用户认证与授权](https://github.com/xiaosatufu/koa-api/commit/453de35320c577af78559c7b4134fe351dce8593)

## Koa-jwt中间件实现用户认证与授权

- 安装koa-jwt
- 使用中间件保护接口
- 使用中间件获取用户信息

[使用koa-jwt中间件使用用户认证与授权](https://github.com/xiaosatufu/koa-api/commit/7f7be36adb76e794cf1a165a58fab98875a836ea)

## 上传图片需求分析

- 用户头像		

- 封面图片
- 问题和回答中的图片
- 话题图片

### 上传图片的功能点

- 基础功能：上传图片，生成图片链接
- 附加功能：限制上传图片的大小与类型，生成高中低三种分辨率的图片链接，生成CDN

### 上传图片的技术方案

- 阿里云OSS等云服务，推荐在生产环境下使用
- 直接上传到服务器，不推荐在生产环境下中使用

### 使用koa-body中间件获取上传的文件

- 安装koa-body，替换koa-bodyparser
- 设置图片上传目录
- 使用Postman上传文件

### 使用koa-static中间件生成图片链接

- 安装koa-static
- 设置静态文件目录
- 生成图片链接

### 编写前端页面上传文件

- 编写上传文件的前端页面
- 与后端接口联调测试

## 个人资料页需求分析

#### 个人资料功能点

- 不同类型（如字符串，数组）的属性
- 字段过滤

### 个人资料的Schema设计

- 分析个人资料的数据结构

### 个人资料的参数校验

- 分析个人资料的数据结构
- 编写代码校验个人资料参数
- 使用postman测试

### RESTful最佳实践--字段过滤

- 设计schema默认隐藏部分字段
- 通过查询字符串显示隐藏字段
- 使用postman测试

## 关注与粉丝需求 

- 关注，取消关注
- 获取关注人，粉丝列表（用户-用户多对多关系）

### 关注与粉丝的schema设计

- 分析关注与粉丝的数据结构
- 设计关注与粉丝的schema

### RESTful风格的关注与粉丝接口

- 实现获取关注人和粉丝列表接口
- 实现关注和取消关注接口

## 编写校验用户存在与否的中间件

```js
   async checkUserExist(ctx,next) {
        const user = await User.findById(ctx.params.id)
        if(!user) {ctx.throw(404,'用户不存在')}
        await next()
    }
```

## 话题模块

### 话题的功能点

- 话题的增改查
- 分页，模糊搜索
- 用户属性中话题的引用
- 关注或取消关注话题，用户关注的话题列表

## 分页

## 模糊搜索

## 用户属性中的话题引用

- 使用话题引用替代部分用户属性

## RESTful风格的关注话题接口

- 关注话题的逻辑（用户-话题多对多关系）

