# koa2基础知识分享
<!-- author:jingxian.liang@mljr.com -->
koa 是由 Express 原班人马打造的，致力于成为一个更小、更富有表现力、更健壮的 Web 框架。 使用 koa 编写 web 应用，通过组合不同的 generator，可以免除重复繁琐的回调函数嵌套， 并极大地提升错误处理的效率。koa 不在内核方法中绑定任何中间件， 它仅仅提供了一个轻量优雅的函数库，使得编写 Web 应用变得得心应手。

## 安装
Koa 依赖 `node v7.6.0` 或 ES2015及更高版本和 `async` 方法支持.
你可以使用自己喜欢的版本管理器快速安装支持的 node 版本：
```
$ nvm install 8
$ npm i koa
```

## Hello world

- nodejs原生
```js
const http = require('http');

const hostname = '127.0.0.1';
const port = 3000;

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello World\n');
});

server.listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}/`);
});
```

- koa2 

```js
const  Koa = require("koa");
const app = new Koa();

function httpServer(ctx){
    ctx.body = "hello world";
}
app.use(httpServer);
app.listen(3000);
```

## 中间件(洋葱圈)
- resquest -->  response  之间发生
![avatar](https://femans.com/images/koa_middleware.jpeg)
## koa 中间件的机制  --  洋葱圈
中间件A -->  中间件B --> 中间件C --> 中间件B --> 中间件A  响应结果

koa2中间件的实现原理(use   next  [async,await] listen)
next();

1. 首先看 use ，就是push一个函数到 this.middleware
[fn1,fn2,fn3]

2. 再看listen, 方法里面 http.createServer(this.callBack), this.callBack返回的是 function(req,res){......}的函数
，连起来就是 http.createServer(function(req,res){....})

3. 最后看callback里面的核心方法， compose(this.middleware) 返回一个promise，处理完毕后再执行 handleResponse

这三个连起来，就是每次请求的时候，先进入callback, compose中间件，执行完毕后，接着处理请求。那剩下的重点变为 compose 

callback --> compose 

compose   核心的核心就是[dispatch], dispatch会根据 middleware 的长度，依次执行。

```js
if (i === middleware.length) fn = next
      if (!fn) return Promise.resolve()

return Promise.resolve(fn(context, dispatch.bind(null, i + 1)));       
```

## compose官网实现逻辑
```js
'use strict'

/**
 * Expose compositor.
 */

module.exports = compose

/**
 * Compose `middleware` returning
 * a fully valid middleware comprised
 * of all those which are passed.
 *
 * @param {Array} middleware
 * @return {Function}
 * @api public
 */

function compose (middleware) {
  // 传入的 middleware 参数必须是数组
  if (!Array.isArray(middleware)) throw new TypeError('Middleware stack must be an array!')

  // middleware 数组的元素必须是函数
  for (const fn of middleware) {
    if (typeof fn !== 'function') throw new TypeError('Middleware must be composed of functions!')
  }

  /**
   * @param {Object} context
   * @return {Promise}
   * @api public
   */

  // 返回一个函数闭包, 保持对 middleware 的引用
  return function (context, next) {
    //context:
    // 这里的 context 参数是作为一个全局的设置, 所有中间件的第一个参数就是传入的 context, 这样可以在 context 中对某个值或者某些值做"洋葱处理"
    //next:
    // 这个传入的 next 函数  是在所有中间件执行后的"最后"一个函数, 这里的"最后"并不是真正的最后,
    // 而是像上面那个图中的圆心, 执行完圆心之后, 会返回去执行上一个中间件函数(middleware[length - 1])剩下的逻辑


    // index 是用来记录中间件函数运行到了哪一个函数
    let index = -1

    // 执行第一个中间件函数
    return dispatch(0)
    function dispatch (i) {
      // i 是洋葱模型的记录已经运行的函数中间件的下标, 如果一个中间件里面运行两次 next, 那么 i 是会比 index 小的.
      // 如果对这个地方不清楚可以查看下面的图
      if (i <= index) return Promise.reject(new Error('next() called multiple times'))
      index = i
      let fn = middleware[i]
      // 这里的 next 就是一开始 compose 传入的 next, 意味着当中间件函数数列执行完后, 执行这个 next 函数, 即圆心
      if (i === middleware.length) fn = next

      // 如果没有函数, 直接返回空值的 Promise
      if (!fn) return Promise.resolve()
      try {
        // 为什么这里要包一层 Promise? 
        // 因为 async 需要后面是 Promise, 然后 next 函数返回值就是 dispatch 函数的返回值, 所以运行 async next(); 需要 next 包一层 Promise
        // next 函数是固定的, 可以执行下一个函数
        return Promise.resolve(fn(context, dispatch.bind(null, i + 1)));
      } catch (err) {
        return Promise.reject(err)
      }
    }
  }
}

```





- 至于在一个中间件函数中两次调用 next 函数导致出错, 我这里提供一个简单的例子供大家参考:

```js
async function first(ctx, next) {
  console.log('1');
  await next();
  await next(); // 两次调用 next
  console.log(ctx);
};

async function second(ctx, next) {
  console.log('2');
  await next();
};

async function third(ctx, next) {
  console.log('3');
  await next();
  console.log('4');
};

const middleware = [first, second, third];

const com = compose(middleware);

com('ctx', function() {
  console.log('hey');
});
```
## 参考资料
[demo](http://gitlab.mljr.com/FELab/fe-share-code/tree/master/koa-share)

[koa2](https://koa.bootcss.com/)


