# REST

- **Re**presentational **S**tate **T**ransfer
- Representational:数据的表现形式（JSON、XML...）
- State:当前状态或者数据
- Transfer：数据传输

## REST的六个限制

### 客户-服务器（Client-Server）

- 关注点分离
- 服务端专注数据存储，提升了简单性
- 前端专注用户界面，提升了可移植性

### 无状态（Stateless）

- 所用用户会话信息都保存在客户端
- 每次请求必须包括所有信息，不能依赖上下文信息
- 服务端不用保存会话信心，提升了简单性，可靠性，可见性

### 缓存（Cache）

- 所有服务端响应都要被标位可缓存或不可缓存
- 减少前后端交互，提升了性能

### ★统一接口（Uniform Interface）

- 接口设计尽可能统一通用，提升了简单性，可见性
- 接口与实现解耦，使前后端可以独立开发迭代

#### 统一接口的限制

##### 资源的标识

- 资源是任何可以命名的实物，比如用户，评论等
- 每个资源可以通过URL被唯一地标识
  - https://api.github.com/users
  - https://api.github/users/lewis617

##### 通过表述来操作资源

- 表述就是Representation,比如JSON，XML等
- 客户端不能直接操作（比如SQL）服务端资源
- 客户端应该通过标书（比如JSON）来操作资源

##### 自描述消息

- 每个消息（请求或响应）必须提供足够的信息让接受者理解
- 媒体信息（application/json application/xml）
- HTTP方法：GET（查）/POST（增）/DELETE（删）
- 是否缓存：Cache-Control

##### 超媒体作为应用状态引擎

- 超媒体：带文字的连接
- 应用状态：一个网页
- 引擎：驱动、跳转
- 合起来：点击链接跳转到另一个网页

### 分层系统（Layered System）

- 每层只知道相邻的一层，后面隐藏的就不知道了
- 客户端不知道是和代理还是真实服务器通信
- 其它层：安全层，负载均衡，缓存层等

### 按需代码（Code-On-Demand可选）

- 客户端可以下载运行服务端的代码（比如JS）
- 通过减少一些功能，简化了客户端

## RESTful API具体是什么样子？

- 基本的URI，如https://api.github.com/user
- 标准的HTTP方法，如GET,POST,PUT,PATCH,DELETE
- 传输的数据媒体类型，如JSON,XML

## RESTful API 设计最佳实践

### 请求设计规范

- URI使用名词，尽量使用复数，如/users
- URI使用嵌套表示关联关系吗，如/users/12/repos/5
- 使用正确的HTTP方法，如GET/POST/PUT/DELETE
- 不符合CURD的情况：POST/action/子资源

### 响应设计规范

- 查询
- 分页
- 字段过滤
- 状态码
- 错误处理

### 安全

- HTTPS
- 鉴权
- 限流

### 开发者友好

- 文档
- 超媒体