### koa简介

首先介绍中间件是Koa中一个非常重要的概念。Koa应用程序就是包含一组中间件函数的对象，而且有了async/await语法糖，是中间件写起来更简单。

首先看最基础的hello wold 版本

```js
const koa = require('koa');
const app = new koa();

//基础 hello wold
app.use(async (ctx, next)=>{
  console.log(ctx.method, ctx.host, ctx.url)
  await next();
  ctx.body = 'hello world'
})
app.listen(3000);
npm install

node app.js
```

接下来抽象打印ctx方法 host url

```js
const koa = require('koa');
const app = new koa();

// 抽象打印ctx方法 host url
const logger = async (ctx, next) => {
  console.log(ctx.method, ctx.host, ctx.url)
  await next();
}

app.use(logger);
app.use(async (ctx, next)=>{
  ctx.body = 'hello world'
})


app.listen(3000);
```

此时logger就是中间件。 next用于把中间件的执行权交给下游的中间件。在next()之前使用await是因为next()会返回Promise对象。直到最后一个中间件执行完毕后再自下而上执行next()之后的代码。我们称为洋葱模型

用代码更好的展示执行顺序

```js
const koa = require('koa');
const app = new koa();

app.use(async (ctx, next) => {
  console.log(1);
  await next();
  console.log(6);
})

app.use(async (ctx, next) => {
  console.log(2);
  await next();
  console.log(5);
})

app.use(async (ctx, next) => {
  console.log(3);
  await next();
  console.log(4);
})

app.listen(3000, ()=>{
  console.log("server is running")
})  
```

### koa-compose

如果要将多个中间件合并成单一中间件,方便重用和导出，可以用koa-compose

```js
const koa = require('koa');
const app = new koa();
const compose = require('koa-compose');

middleware1 = async (ctx, next) => {
  console.log(1);
  await next();
  console.log(6);
}

middleware2 = async (ctx, next) => {
  console.log(2);
  await next();
  console.log(5);
}

middleware3 = async (ctx, next) => {
  console.log(3);
  await next();
  console.log(4);
}

const all = compose([middleware1, middleware2, middleware3])
app.use(all)

app.listen(3000, ()=>{
  console.log("server is running")
}) 
```

### koa-bodyparser

koa-bodeyparser可以把POST的参数解析到ctx.request.body中。如果熟悉Express的话会发现是一样的。

我们将会用到koa-static等会会详细介绍先可以忽略。

前台html

```js
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <form method="post">
        <input type="text" name="name" placeholder="input anything" />
        <button type="submit">submit</button>
    </form>
</body>
</html>
```

后台app.js

看是否打印了我们传递的数据

```js
const koa = require('koa');
const app = new koa();
const bodyparser = require('koa-bodyparser')
app.use(bodyparser({
  enableTypes:['json', 'form', 'text']
}))

app.use(require('koa-static')(__dirname + '/public'))

app.use(async (ctx, next)=>{
  await next();
  console.log(ctx.request.body)
  let retunData = ctx.request.body
  ctx.body = retunData
})

app.listen(3000);
```

### koa-router

加上koa-router中间件后我们可以直接使用get和post来定位并且减少代码量

```js
const koa = require('koa');
const app = new koa();
const bodyparser = require('koa-bodyparser')
const Router = require('koa-router')
const router = new Router() // 初始化router
app.use(bodyparser({
  enableTypes:['json', 'form', 'text']
}))

app.use(require('koa-static')(__dirname + '/public'))
app.use(router.routes()) // 加载router中间件
app.use(router.allowedMethods()) //对异常码处理


router.get('/', async (ctx, next) => {
  ctx.render('index')
})

router.post('/test1', async (ctx, next)=>{
  let retunData = ctx.request.body
  console.log(ctx.request.body)
  ctx.body = retunData
})
```

### koa-static和koa-view

我们在之前就已经用了koa-static，它是用来加载静态资源
 比如

```
app.use(require('koa-static')(__dirname + '/public'))
```

而koa-view用来加载html模板文件列入ejs或者pug

```js
const Koa = require('koa')
const app = new Koa()
const views = require('koa-views')
const bodyparser = require('koa-bodyparser')
const Router = require('koa-router')
const router = new Router() // 初始化router

// middlewares
app.use(bodyparser({
  enableTypes:['json', 'form', 'text']
}))

//两种写法
app.use(require('koa-static')(__dirname + '/public')) 

app.use(views(__dirname + '/views', {
  extension: 'pug'
}))


app.use(router.routes()) // 加载router中间件
app.use(router.allowedMethods()) //对异常码处理


router.post('/test1', async (ctx, next)=>{
  let retunData = ctx.request.body
  console.log(ctx.request.body)
  ctx.body = retunData
})

router.get('/', async (ctx, next) => {
  ctx.render('index')
})


app.listen(3000);
```

​                   



​        



# Express与Koa中间件机制分析（一）

> 提到 Node.js 开发，不得不提目前炙手可热的2大框架 Express 和 Koa。

> Express 是一个保持最小规模的灵活的 Node.js Web 应用程序开发框架，为 Web 和移动应用程序提供一组强大的功能。目前使用人数众多。

> Koa 是一个新的 web 框架，由 Express 幕后的原班人马打造， 致力于成为 web 应用和 API 开发领域中的一个更小、更富有表现力、更健壮的基石。 通过利用 async 函数，Koa 帮你丢弃回调函数，并有力地增强错误处理。 Koa 并没有捆绑任何中间件， 而是提供了一套优雅的方法，帮助您快速而愉快地编写服务端应用程序。

相信对这两大框架有一些了解的人都或多或少的会了解其中间件机制，Express 为线型模型，而 Koa 则为洋葱型模型。这个系列的博客主要讲解 Express 和 Koa 的中间件机制，本篇将主要讲解 Express 的中间件机制。

## Express 中间件

connect 曾经是 express 3.x 之前的核心，而 express 4.x 已经把 connect 移除，在 express 中自己实现了 connect 的接口，所以我们本篇中的源码解释将直接使用 connect 源码。

### 示例

下面将使用 Express 实现一个简单的 demo 来进行中间件机制的讲解

```js
var express = require('express');

var app = express();
app.use(function (req, res, next) {
    console.log('第一个中间件start');
    setTimeout(() => {
        next();
    }, 1000)
    console.log('第一个中间件end');
});
app.use(function (req, res, next) {
    console.log('第二个中间件start');
    setTimeout(() => {
        next();
    }, 1000)
    console.log('第二个中间件end');
});
app.use('/foo', function (req, res, next) {
    console.log('接口逻辑start');
    next();
    console.log('接口逻辑end');
});
app.listen(4000);
复制代码
```

此时的输出比较符合我们对 Express 线性的理解，其输出为

```
第一个中间件start
第一个中间件end
第二个中间件start
第二个中间件end
接口逻辑start
接口逻辑end
复制代码
```

但是，如果我们取消掉中间件内部的异步处理直接调用 next()

```js
var express = require('express');

var app = express();
app.use(function (req, res, next) {
    console.log('第一个中间件start');
    next()
    console.log('第一个中间件end');
});
app.use(function (req, res, next) {
    console.log('第二个中间件start');
    next()
    console.log('第二个中间件end');
});
app.use('/foo', function (req, res, next) {
    console.log('接口逻辑start');
    next();
    console.log('接口逻辑end');
});
app.listen(4000);
复制代码
```

输出结果为

```js
第一个中间件start
第二个中间件start
接口逻辑start
接口逻辑end
第二个中间件end
第一个中间件end
复制代码
```

这种结果不是和 Koa 的输出很相似吗？是的，但是它和剥洋葱模型还是不一样的，其实这种输出的结果是由于代码的同步运行导致的，并不是说 Express 不是线性的模型。

当我们的中间件内没有进行异步操作时，其实我们的代码最后是以下面这种方式运行的

```js
app.use(function middleware1(req, res, next) {
    console.log('第一个中间件start')
        // next()
        (function (req, res, next) {
            console.log('第二个中间件start')
                // next()
                (function (req, res, next) {
                    console.log('接口逻辑start')
                        // next()
                        (function handler(req, res, next) {
                            // do something
                        })()
                    console.log('接口逻辑end')
                })()
            console.log('第二个中间件end')
        })()
    console.log('第一个中间件end')
})
复制代码
```

导致这种运行方式的其实就是 connect 的实现方式，接下来我们进行其源码的解析

### connect 源码解析

connect的源码仅有200多行，但是这里讲解只选择其中部分核心代码，并非完整代码，查看全部代码请移步 [github](https://link.juejin.im?target=https%3A%2F%2Fgithub.com%2Fsenchalabs%2Fconnect%2Fblob%2Fmaster%2Findex.js)

中间件的挂载主要依赖 proto.use 和 proto.handle，这里我们删掉部分 if 判断以使我们更专注于其内部原理的实现

```js
proto.use = function use(route, fn) {
    var handle = fn;
    var path = route;

    // 这里是对直接填入回调函数的进行容错处理
    // default route to '/'
    if (typeof route !== 'string') {
        handle = route;
        path = '/';
    }
    .
    .
    .
    this.stack.push({ route: path, handle: handle });

    return this;
};
复制代码
```

proto.use 主要将我们需要挂载的中间件存储在其自身 stack 属性上，同时进行部分兼容处理，这一块比较容易理解。其中间件机制的核心为 proto.handle 内部 next 方法的实现。

```js
proto.handle = function handle(req, res, out) {
    var index = 0;
    var stack = this.stack;

    function next(err) {

        // next callback
        var layer = stack[index++];

        // all done
        if (!layer) {
            defer(done, err);
            return;
        }

        // route data
        var path = parseUrl(req).pathname || '/';
        var route = layer.route;

        // skip this layer if the route doesn't match
        if (path.toLowerCase().substr(0, route.length) !== route.toLowerCase()) {
            return next(err);
        }

        // call the layer handle
        call(layer.handle, route, err, req, res, next);
    }

    next();
};
复制代码
```

在删除到部分非核心代码后，可以清晰的看到，proto.handle 的核心就是 next 方法的实现和递归调用，对存在于 stack 中的中间件取出、执行。

这里便可以解释上文中异步和非异步过程中所输出的结果的差异了。

- 当有异步代码时，将会直接跳过继续执行，此时的 next 方法并未执行，需要等待当前队列中的事件全部执行完毕，所以此时我们输出的数据是线性的。
- 当 next 方法直接执行时，本质上所有的代码都已经为同步，所以层层嵌套，最外层的肯定会在最后，输出了类似剥洋葱模型的结果。

## 总结

connect 的实现其基本原理是维护一个 stack 数组，将所需要挂载的中间件处理后全部 push 到数组内，之后在数组内循环执行 next 方法，直至所有中间件挂载完毕，当然这过程中会做一些异常、兼容等的处理。

# Express与Koa中间件机制分析（二）

## 前言

在[Express与Koa中间件机制分析（一）](https://juejin.im/post/5c88d4bce51d4504743cf56f)中我们有提到，Express 为线型模型，而 Koa 则为洋葱型模型，之前我们已经通过解析 connect 的源码对 Express 中间件机制进行了分析，本篇文章我们将对 Koa 的部分源码进行分析以帮助大家来理解其中间件机制。

**koa1 基于的 co 库，所以 koa1 利用 Generator 来代替回调，而 koa2 由于 node 对 async/await 的支持，所以 koa2 利用的是 async/await**

目前大家常用的基本上都是 koa2，在这里我们先对于 Koa2 的实现进行分析，Koa1 先留个坑，之后再补充。

## Koa1 中间件

留坑待续...

## Koa2 中间件

Koa2 中间件的实现依赖于其自身的 koa-compose，下面我们看一下 compose 函数的实现。

```js
function compose (middleware) {
  // 判断参数是否合法，middleware 要求为数组且其中每个数组元素都为 function
  if (!Array.isArray(middleware)) throw new TypeError('Middleware stack must be an array!')
  for (const fn of middleware) {
    if (typeof fn !== 'function') throw new TypeError('Middleware must be composed of functions!')
  }

  /**
   * @param {Object} context
   * @return {Promise}
   * @api public
   */

  return function (context, next) {
    // last called middleware #
    let index = -1
    // 递归返回一个函数 该函数返回一个 Promise 的对象
    return dispatch(0)
    function dispatch (i) {
      // 当 next 方法被多次调用时会出现
      if (i <= index) return Promise.reject(new Error('next() called multiple times'))
      index = i
      let fn = middleware[i]
      // 最后一个中间件
      if (i === middleware.length) fn = next
      if (!fn) return Promise.resolve()
      // Promise 封装中间件 进行递归调用
      try {
        return Promise.resolve(fn(context, dispatch.bind(null, i + 1)));
      } catch (err) {
        return Promise.reject(err)
      }
    }
  }
}
复制代码
```

在 Koa 中的 use 函数内，中间件入栈，在 compose 函数中传入的 middleware 即为中间件数组。在 compose 中对该数组进行递归调用，返回一个 Promise 链。

下面通过一个例子来对中间件执行这一过程进行分析：

```js
// 中间件 fn1 和 fn2
async function fn1 (ctx, next) {
  console.log('first: start')
  await next()
  console.log('first: end')
}
async function fn2 (ctx, next) {
  console.log('second: start')
  await next()
  console.log('second: end')
}
// 模拟中间件数组
const arr = [fn1, fn2]
// 执行函数，这里返回的是一个 Promise 对象
compose(arr)()
// 执行后的结果
// first: start
// second: start
// second: end
// first: end
复制代码
```

其实，在 compose 内部递归执行的操作后，形成多个 Promise 层层嵌套（如下面代码所示），此时 next 函数其实就是下一个中间件，await 需要等待内部的 Promise ，所以其执行结果会呈现一个剥洋葱的模式。

```js
function mycompose() {
  return function () {
    const ctx = {}
    return Promise.resolve(fn1(ctx, () => {
      return Promise.resolve(fn2(ctx, () => {
      }))
    }))
  }
}
mycompose()()
复制代码
```

## 总结

Koa 相对于 Express 来说轻量了好多，它只是一个基础的结构，当需要到其他功能时，再使用相应的中间件，上文所讲到的 koa-compose 就是其中之一，其路由及其他功能又会在其他中间件实现，所以你可以看到，即使是 koa-compose 全部的代码依然很简练。



