## Koa 初识
### 基本用法
- 架设一个 http 服务

```js
const Koa = require('koa');
const app = new Koa();

app.listen(3000);
```
- Context对象
    - Koa 提供一个Context对象表示一次对话的上下文，表示一次对话的上下文，Context.response.body 属性就是发给用户的内容

```js
const Koa = require('koa');
const app = new Koa();

const main = ctx => {
  ctx.response.body = 'Hello World';
};

app.use(main); // 加载main 函数
app.listen(3000);
```
- HTTP Response的类型
    - Koa 默认返回的类型是 text/plain, 想返回其他类型 可以用 ctx.request.accepts 判断客户端希望接受什么数据，然后使用ctx.response.type 指定返回类型
```js
const main = ctx => {
  if (ctx.request.accepts('xml')) { // 判断客户端希望接受的数据 
    ctx.response.type = 'xml';
    ctx.response.body = '<data>Hello World</data>';
  } else if (ctx.request.accepts('json')) {
    ctx.response.type = 'json';
    ctx.response.body = { data: 'Hello World' };
  } else if (ctx.request.accepts('html')) {
    ctx.response.type = 'html';
    ctx.response.body = '<p>Hello World</p>';
  } else {
    ctx.response.type = 'text';
    ctx.response.body = 'Hello World';
  }
}
```
- 网页模板

```js
const fs = require('fs');

const main = ctx => {
  ctx.response.type = 'html';
  ctx.response.body = fs.createReadStream('./demos/template.html');
};
```
### 路由
- 原生路由 通过ctx.request.path 可以获取用户请求的路径
- 使用 koa-route

```js
const route = require('koa-route');

const about = ctx => {
  ctx.response.type = 'html';
  ctx.response.body = '<a href="/">Index Page</a>';
};

const main = ctx => {
  ctx.response.body = 'Hello World';
};
// 对应不同的处理函数
app.use(route.get('/', main));
app.use(route.get('/about', about));
```
- 静态资源 koa-static
- 重定向 ctx.response.redirect() 可以发出一个302跳转，导向另一个 路由

```js
const redirect = ctx => {
  ctx.response.redirect('/');
  ctx.response.body = '<a href="/">Index Page</a>';
};

app.use(route.get('/redirect', redirect));
```

### 中间件
- Koa 最重要的设计就是中间件

```js
const logger = (ctx, next) => {
  console.log(`${Date.now()} ${ctx.request.method} ${ctx.request.url}`);
  next();
}
app.use(logger); // 加载中间件
```
- 处于Request 与 Response的中间，用来实现某种中间功能，中间件默认接收两个参数 Context 对象与 next 函数
- 中间件栈 多个中间件会形成一个栈结构 以 先进后出的顺序执行

```text
1.执行最外层中间件
2.执行下一个中间件
。。。
4.最内层中间件执行
5.最内层中间件执行后将执行权交给上一层中间件
。。。
6.最外层中间件执行完毕，执行next函数后的代码
```
- 中间件内部没调用 next 执行权就不会传递下去
- 异步中间件 有异步操作（读取数据库）中间件必须写成 async函数

```js
const fs = require('fs.promised');
const Koa = require('koa');
const app = new Koa();

const main = async function (ctx, next) {
  ctx.response.type = 'html';
  ctx.response.body = await fs.readFile('./demos/template.html', 'utf8');
};

app.use(main);
app.listen(3000);
```
- 中间件合成 koa-compose 可以将多个中间件合成一个

```js
const middlewares = compose([logger, main]);
```

### 错误处理
- 500 错误 如果代码发生错误， 需要将错误信息返回给用户。HTTP约定这时候要返回 500 状态码
    - Koa 提供了ctx.throw() 用来抛出错误
    ```js
      ctx.throw(500) // 抛出500 错误
    ```
- 404 ctx.response.status 设置成 404 相当于 ctx.throw(404)

- 处理错误中间件
    - try.. catch 比较繁琐 需要写的多
    - 让最外层中间件 统一处理
    
    ```js
    const handler = async (ctx, next) => {
      try {
        await next();
      } catch (err) {
        ctx.response.status = err.statusCode || err.status || 500;
        ctx.response.body = {
          message: err.message
        };
      }
    };
    
    const main = ctx => {
      ctx.throw(500);
    };
    
    app.use(handler);
    app.use(main);
    ```
- error事件监听 运行过程一旦出错，Koa 会触发 error事件
- 释放error事件 如果被try。。catch捕获就不会触发error事件，这时必须手动调用 ctx.app.emit() 手动释放

```js
const handler = async (ctx, next) => {
  try {
    await next();
  } catch (err) {
    ctx.response.status = err.statusCode || err.status || 500;
    ctx.response.type = 'html';
    ctx.response.body = '<p>Something wrong, please contact administrator.</p>';
    // 释放后 error事件才能被监听
    ctx.app.emit('error', err, ctx);
  }
};

const main = ctx => {
  ctx.throw(500);
};

app.on('error', function(err) {
  console.log('logging error ', err.message);
  console.log(err);
});
```

### Web App


