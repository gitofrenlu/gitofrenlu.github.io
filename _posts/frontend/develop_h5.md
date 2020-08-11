# 开发h5项目
<!-- author:lingke.wang@mljr.com -->

所有项目开发均基于[nodejs](https://nodejs.org/zh-cn/)，需先安装。

## 安装vue-cli

现所有项目均基于[vue-cli](https://cli.vuejs.org/zh/)生成。

```bash
# taobao npm 
npm config set registry https://registry.npm.taobao.org

npm install -g @vue/cli
```

## 创建项目

目前项目已整理为通用模板，直接通过`vue-cli`进行生成。

```bash
# 创建项目，project-name为项目名称
vue create --preset gitlab.mljr.com:FEApply/vue-cli3-preset --clone project-name
```
![](https://ws1.sinaimg.cn/large/006tNc79ly1g2bdomitodj30vd0ehq3g.jpg)

选择第一个即可。

项目模板为公司内部，[gitlab地址](http://gitlab.mljr.com/FEApply/vue-cli3-preset)

## 项目目录

``` bash
├── .env '默认环境变量，可在此配置本地开发时的baseUrl'
├── .env.production '上线时需要修改的配置，此处会将baseUrl改为//s1.mljr.com/project-name'
├── .env.test '代码部署至测试环境需要修改的配置'
├── .git
├── .gitignore
├── .gitlab-ci.yml 'gitlab-ci配置'
├── README.md
├── babel.config.js
├── deploy.sh '上线时所用到的脚本'
├── node_modules
├── package-lock.json
├── package.json
├── public 'index.html存放处'
├── src '项目文件'
└── vue.config.js 'webpack相关配置'
```
`src`目录

```bash
├── components `组件`
├── config `配置`
├── images `图片`
├── page `页面`
│   ├── hello
│   └── vuex
├── router `路由`
├── store `vuex`
├── main.js `入口`
└── App.vue `根元素`
```

## 开发

### 启动服务
```bash
npm run serve
```
即可看到本地服务地址，访问即可。

`npm run`的相关脚本在`package.json`的`scripts`中。

![](https://ws4.sinaimg.cn/large/006tNc79ly1g2f0fjgxogj30a303e3yd.jpg)

`Local`地址仅限本机访问

`Network`地址为局域网内都可访问(如需手机调试，请使用`Network`地址)。

### 页面加载流程

#### main.js

入口为`main.js`，挂载`App.vue`和路由进行初始化，主要代码：

```js
...
import App from "./App";
import router from "./router";
...
new Vue({
  el: "#app",
  ...
  router,
  components: { App },
  template: "<App/>"
});
```

#### App.vue

`App.vue`为根元素，控制整个项目的路由。

```html
<div id="app">
  <router-view />
</div>
```

#### router

读取`router/index.js`中的路由配置，进行页面显示，[vue-router文档](https://router.vuejs.org/zh/)

```js
import hello from "@/page/hello";
...
{
  path: "/",
  name: "hello",
  component: hello
}
```
`path`为访问路径，`/`则为首页，`component`为此路由需显示的组件或页面。

`@`此处表示`src`目录，已默认在`webpack`配置。

#### hello

从路由的配置来看，最终显示的页面在`src/page/hello`中。

如果是目录，默认读取`index.vue`文件。

### 新建页面

在`src/page`中新建目录，如`test`，并新建`index.vue`文件。

`html`、`js`、 `css`均写在一个文件中，编译时由`webpack`进行提取并生成独立的文件。

#### 编写html

`html`采用`pug`，请法可参考[jade](https://segmentfault.com/a/1190000000357534#articleHeader7)

```pug
<template lang="pug">
.test 我是测试内容
</template>
```

#### 编写js

需`export`一个默认对象，可使用最新的语法，编译时会转换浏览器可识别的代码。

```js
<script>
export default {
  data() {
    return {
    };
  }
};
</script>
```
::: warning 
一个页面可以不写html和css，但必须`export`一个默认对象。
:::

#### 编写css

`css`采用`sass`来进行编写，可参考[sass参考手册](http://sass.bootcss.com/docs/sass-reference/)。

```html
<style lang="scss" scoped>
.test{
}
</style>
```

`scope`表示该样式的作用域仅针对此页面。

### 添加路由

在`router/index.js`中进行添加，有如下俩种方式：

1、先引入组件，此种方式在最终打包的时候，会和其它`js`文件打包在一起。
```js
import test from "@/page/test";
...
{
  path: "/test",
  component: test
}
```

2、在访问当前路由的时候再进行加载，此种方式打包出来为单独的`js`文件。
```js
{
  path: "/test",
  component: resolve => require(["@/page/test"], resolve)
}
```

### 请求接口

在`<script>`中添加如下代码

```js
import { ajax } from "@/config/Util";
ajax(
  "/staff/getStaffInfoForSimbaClassById",
  {
    source: 0,
    userId: 241
  },
  {
    headers: {
      "Content-Type": "application/x-www-form-urlencoded",
      sign: "foobar"
    }
  }
).then(data => {
  console.log(data);
});
```

此时请求的是本地接口，直接`404`。

![](https://ws1.sinaimg.cn/large/006tNc79ly1g2f6cjohf7j30n406gmxe.jpg)

修改`vue.config.js`中的`proxy`即可进行配置。

```js
devServer: {
  proxy: {
    "/staff": {
      target: "http://10.250.1.122:8088/",
      changeOrigin: true,
      pathRewrite: {
        "^/staff": "/staff"
      }
    }
  }
}
```

修改完需重启服务才生效。

![](https://ws1.sinaimg.cn/large/006tNc79ly1g2f6jcgsprj30ni05wt8w.jpg)


在`vue.config.js`中进行配置：

<img src="https://ws1.sinaimg.cn/large/006tNc79ly1g2ew4f17d6j30wa0qgq3v.jpg" width="450">

### 适配

目前主要使用`rem`进行适配，`rem`适配的本质是基于`html`进行等比缩放，一般基于宽度。

假如设计稿是基于`iphone6`来做适配，即宽度为`375px`。

将屏幕宽度分为`375`份，则每一份的宽度为`X = 屏幕宽度 / 375`。

此时设计稿中所有元素的尺寸只需乘以`X`即可达到适配的目的，这个最终尺寸的单位就是`rem`。

实际开发中元素的尺寸按标注来写，通过`postcss`的插件`postcss-pxtorem`进行自动转换。

设计稿标注：

![](https://ws1.sinaimg.cn/large/006tNc79ly1g2ewvglxqhj30ag0ij74q.jpg)

CSS:

![](https://ws3.sinaimg.cn/large/006tNc79ly1g2ey6h1l6cj3099068glr.jpg)

转换后的`rem`尺寸：
![](https://ws2.sinaimg.cn/large/006tNc79ly1g2exd3wfd8j30i20713ys.jpg)

![](https://ws3.sinaimg.cn/large/006tNc79ly1g2exarhoj7j30bm0abdfw.jpg)


### 真机调试

使用[charles](https://www.charlesproxy.com/)抓包，并进行代理。

1、手机和电脑连同一个wifi，并设置手机代理，如下：

<img src="https://ws1.sinaimg.cn/large/006tNc79ly1g2bgycywzhj30u01hcdgk.jpg" width="250">

服务器为本机`ip`地址，端口固定为`8888`。

2、在`charles`中允许来自手机的连接。

![](https://ws3.sinaimg.cn/large/006tNc79ly1g2bh5r5hlqj30f8067t8r.jpg)

即可抓取手机上所有的`http`请求。

此时可以抓到辛巴教室首页的`html`

![](https://ws2.sinaimg.cn/large/006tNc79ly1g2blos8zo8j312z0u0juf.jpg)

右键选择`Map Remote`

<img src="https://ws2.sinaimg.cn/large/006tNc79ly1g2bls9ab4sj30kc18gaby.jpg" width="250">

进行如下配置，需将`Host`和`Port`，换成本机

![](https://ws3.sinaimg.cn/large/006tNc79ly1g2bluvjuhaj30bg0g1dfx.jpg)

### 部署

#### 部署到测试环境

`npm run qa`即可。

```bash
npm run qa
```

#### 部署至生产环境

- 使用`gitlab-runner`进行自动化部署

- 执行`npm run www`在本地进行提交
