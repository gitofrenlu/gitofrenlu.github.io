# node 网页爬虫

<!-- author:jingxian.liang@mljr.com -->

## 原理

从已知的 `url` 中获取对应的 `html` 页面，通过类似 `jqueryDOM` 操作获取相应节点的数据，组装完成后，以 `post` 的形式把这些数据传递给下级。

![](https://www.femans.com/demo/mljr/node-rep.png)

## 使用的 node 依赖包

- superagent
- cheerio
- koa 的相关中间件

### superagent

一个轻量的,渐进式的 ajax api,可读性好,学习曲线低,内部依赖 nodejs 原生的请求 api,适用于 nodejs 环境下 [使用文档](https://www.npmjs.com/package/superagent)

```javascript
request
  .get("/some-url")
  .query({ action: "edit", city: "London" }) // query string
  .end((err, res) => {
    // Do something
  });
```

### cheerio

cheerio 是 jquery 核心功能的一个快速灵活而又简洁的实现，主要是为了用在服务器端需要对 DOM 进行操作的地方

```javascript
var cheerio = require("cheerio");
$ = cheerio.load('<h2 class = "title">Hello world</h2>');
$("h2.title").text("Hello there!");
$("h2").addClass("welcome");
$.html();
```

[官方使用文档](https://www.npmjs.com/package/cheerio)

[中文参考文档](https://www.jianshu.com/p/629a81b4e013)

## DEMO

### app.js

```javascript
const Koa = require("koa");
const app = new Koa();
const static = require("koa-static");
const bodyParser = require("koa-bodyparser");
const index = require("./routes/index");

// 处理POST
app.use(bodyParser());
app.use(
  static(__dirname + "/views", {
    extensions: ["html"]
  })
);
// routes
app.use(index.routes(), index.allowedMethods());

app.listen(8081);
```

### router.js

```javascript
const router = require("koa-router")();
const superagent = require("superagent");
const cheerio = require("cheerio");

router.post("/api/spider", async (ctx, next) => {
  let params = ctx.request.body;
  const oInfo = await superagent.get(params.urlName).set({
    Accept: "application/json"
  });
  const $ = cheerio.load(oInfo.text);
  ctx.body = {
    status: 0,
    data: {
      title: $(params.domTitle).text() || "获取标题异常",
      content: $(params.domContent).text() || "获取正文异常"
    }
  };
});
router.post("/api/spider/getList", async (ctx, next) => {
  let params = ctx.request.body;
  let result = [];
  const oInfo = await superagent.get(params.urlName).set({
    Accept: "application/json"
  });
  const $ = cheerio.load(oInfo.text);

  $(params.domName).each((index, item) => {
    var url = $(item)
      .find(params.domItem)
      .attr("href");
    result.push({
      title:
        $(item)
          .find(params.domItem)
          .text() || "获取标题异常",
      url:
        $(item)
          .find(params.domItem)
          .attr("href") || "获取url异常",
      date:
        $(item)
          .find(params.domTime)
          .text() || "获取日期异常"
    });
  });
  ctx.body = {
    status: 0,
    data: {
      list: result
    }
  };
});

module.exports = router;
```

[前端运行实例 demo](http://node.femans.com/)
