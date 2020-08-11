# vue项目接入sentry
<!-- author:lingke.wang@mljr.com -->

## sentry后台地址

[http://femonitor.mljr.com](http://femonitor.mljr.com)

限内网访问，外网仅开放`https://femonitor.mljr.com/api/{params}`接口。

## 新建项目

在首页，点击右上角`Add new...`，选择项目。

![](http://ww1.sinaimg.cn/large/006tNc79ly1g4ftuyjktij30l703ha9z.jpg)

在对应的地方输入`项目名`, `team`选`fe_team`, 然后创建项目即可。

![](http://ww1.sinaimg.cn/large/006tNc79ly1g4ftza6ohrj30pr0gvdgg.jpg)

创建完成之后，点击`Get your DSN`，并保存公用`DSN`。

![](http://ww2.sinaimg.cn/large/006tNc79ly1g4fwnxbwivj30ik069mx9.jpg)

![](http://ww2.sinaimg.cn/large/006tNc79ly1g4fwpa3yruj30nu0a0jrr.jpg)

## 使用sentry

### webpack项目

安装`@sentry/browser`(Sentry’s browser JavaScript SDK)。

安装`@sentry/integrations`(Vue Error Handling)。

```bash
npm i @sentry/browser @sentry/integrations
```

`main.js`中引入并配置即可，`project dsn`为上一步的公用`DSN`或[获取项目dsn](#获取项目dsn)。

**外网仅支持https，务必将DSN的http改为https**

**外网仅支持https，务必将DSN的http改为https**

**外网仅支持https，务必将DSN的http改为https**

```js
import * as Sentry from '@sentry/browser';
import * as Integrations from '@sentry/integrations';

const sentryDsn = `{{project dsn}}`;

const sentryEnv = (() => /^\d+$|localhost/g.exec(location.host.replace(/\.|:/g, ''))
  ? (/^192\.168/g.exec(location.host) ? 'test' : 'dev')
  : 'prod')();

Sentry.init({
  dsn: sentryEnv === 'dev' ? '' : sentryDsn,
  environment: sentryEnv,
  integrations: [
    new Integrations.Vue({
      Vue,
      attachProps: true,
    }),
  ],
});
```

需手动进行配置来捕获请求错误，在封装的`axios`函数中，错误处理的地方加上`sentry`即可，如：

```js
import * as Sentry from '@sentry/browser';

...
axios(...).then(res => {
  ...
}).catch((error) => {
  Sentry.captureException(error);
  ...
});
```


### fepack项目

1、下载此<a href="http://192.168.49.104/fe-source/sentry.js" target="blank" download>sentry.js</a>，并保存至`static/scripts/libs`目录中。

2、修改`page/index.jade`，引入`sentry.js`。

```pug
//- index.jade
...
script(src="/static/scripts/libs/sspa-vue.js")
//- 在vue后面引入
script(src="/static/scripts/libs/sentry.js")
...
```

3、修改`static/scripts/index.ts`文件，增加如下：

`project dsn`为上一步的公用`DSN`或[获取项目dsn](#获取项目dsn)。

**外网仅支持https，务必将DSN的http改为https**

**外网仅支持https，务必将DSN的http改为https**

**外网仅支持https，务必将DSN的http改为https**

```js

const sentryDsn = `{{project dsn}}`;

const sentryEnv = (() => /^\d+$|localhost/g.exec(location.host.replace(/\.|:/g, ''))
  ? (/^192\.168/g.exec(location.host) ? 'test' : 'dev')
  : 'prod')();

Sentry.init({
  dsn: sentryEnv === 'dev' ? '' : sentryDsn,
  environment: sentryEnv,
  integrations: [
    new Sentry.Integrations.Vue({
      Vue,
      attachProps: true,
    }),
  ],
});

```
需手动进行配置来捕获请求错误，在封装的`ajax`函数中，错误处理的地方加上`sentry`即可，如：

```js
$.ajax(params)
  .done((data, status, xhr) => {
    ...
  })
  .fail((jqXHR, textStatus, errorThrown) => {
    let args = [jqXHR, textStatus, errorThrown];
    ...
    Sentry.captureException(args);
  })
```

## 获取项目dsn

进入项目，点击`设置`，找到`dsn`即可。

**外网仅支持https，务必将DSN的http改为https**

**外网仅支持https，务必将DSN的http改为https**

**外网仅支持https，务必将DSN的http改为https**

![](http://ww4.sinaimg.cn/large/006tNc79ly1g4gr2ear0nj30a70353yc.jpg)

![](http://ww3.sinaimg.cn/large/006tNc79ly1g4gr1qpun6j30s80g3dgn.jpg)
 
 ## 错误忽略配置

 忽略单个错误，如忽略`Network Error`：

 ![](https://tva1.sinaimg.cn/large/006y8mN6ly1g8a99vywpkj309k02ct8j.jpg)

 ```javascript
 Sentry.init({
  ...
  ignoreErrors: ['Network Error']
});
 ```


忽略某个错误类型下的所有错误，如忽略所有`promise reject`报的错：

![](https://tva1.sinaimg.cn/large/006y8mN6ly1g8a9h61hxpj30j70cfwf9.jpg)

 ```javascript
 Sentry.init({
  ...
  beforeSend : function(data){
    if(data.exception.values[0].type === "UnhandledRejection"){
      return null
    }
    return data;
  }
});
 ```
 init的时候添加beforeSend钩子，返回null时不抛错
