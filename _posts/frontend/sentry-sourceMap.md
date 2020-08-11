# sentry sourceMap 配置
<!-- author:lu.ren@mljr.com -->

##  最终效果
![](http://ww1.sinaimg.cn/large/005Y476rly1g4lhscw84wj30vx0goacc.jpg)

## 配置步骤
1.创建token
---

![](http://ww1.sinaimg.cn/large/005Y476rly1g4lhuxv0f1j308b0btgma.jpg)
![](http://ww1.sinaimg.cn/large/005Y476rly1g4lhwko4uvj316r0j0q5t.jpg)

2.引入包
---
```javascript
npm install --save-dev @sentry/webpack-plugin
```

3.创建 **.sentryclirc**
---
```javascript
[auth]
token=d1d0981928a54d52b918993de796fa97c6c5c529b5aa4d3896813d001c3a5ccd

[defaults]
url=http://femonitor.mljr.com/
project={project-name}
org=sentry
```
**point:**

1.http://femonitor.mljr.com/{org}/{project}/

eg: http://femonitor.mljr.com/sentry/sourcemap-rl/

4.配置webpack插件
---

旧版脚手架
---
**webpack.conf.js**
```javascript
const SentryCliPlugin = require('@sentry/webpack-plugin');

plugins: [
    new SentryCliPlugin({
      release: ,
      include: './dist/customer-straight/static/js/', //必须，array or string，指定sentry-cli扫描的文件夹，它会将js和匹配的map文件上传
      urlPrefix: `~/backup/customer-straight/static/js`, //可选，重要！！！上传到Sentry的SourceMap及源文件的名称，用于解析时的匹配
      ignoreFile: '.sentrycliignore', //可选，string, 扫描时所忽略的文件的配置文件所在位置,格式和.gitignore一样
      // configFile: 'sentry.properties', //可选，string，sentry-cli的配置文件位置，
      deleteAfterCompile: true
    }),
  ],
```
**config/index.js**
```
productionSourceMap: true
```
vue-cli3
---
**vue.conf.js**
```javascript
const SentryPlugin = require("@sentry/webpack-plugin");

module.exports = {
  productionSourceMap: true,
  ...
  chainWebpack: config => {
   
    if (process.env.NODE_ENV === "production") {
    config.plugin("sentry").use(SentryPlugin, [
      {
        release: "",
        include: "./dist/fe-car-boss-op/static/js/", //必须，array or string，指定sentry-cli扫描的文件夹，它会将js和匹配的map文件上传
        urlPrefix: "~/fe-car-boss-op/static/js", //可选，重要！！！上传到Sentry的SourceMap及源文件的名称，用于解析时的匹配
        ignoreFile: ".sentrycliignore", //可选，string, 扫描时所忽略的文件的配置文件所在位置,格式和.gitignore一样
        // configFile: 'sentry.properties', //可选，string，sentry-cli的配置文件位置，
        ignore: ["node_modules"],
        deleteAfterCompile: true
      }
    ]);
    }
  }
}
```

5.在声明sentry时添加release
---
```javascript
Sentry.init({
  dsn: "",
  integrations: [
    new Integrations.Vue({
      Vue,
      attachProps: true
    })
  ],
  release: ""
});
```

6.补充
---
<p style="color:red">线上安全考虑不能上传sourcemap文件，故在同步至线上git地址时删除.map文件</p>

**deploy.sh**
```javascript
  rsync -av -e ssh --exclude='*.js.map' fe-car-boss-op/* fe-car-boss-op-release/
```
**.gitlab-ci.yml**
```javascript
  rsync -auvzP --delete --exclude='*.js.map' ./dist/$CI_PROJECT_NAME $STATIC_PATH
```
### 原理
**Sentry服务器SourceMap解析的原理就是读取出错的Javascript文件末尾的SourceMap指令，得到SourceMap文件路径，再与对应Releases下的Artifacts进行匹配，从而进行解析。**