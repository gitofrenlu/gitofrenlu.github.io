# vue项目使用eslint
<!-- author:lingke.wang@mljr.com -->

基于[vue-cli3](vue-cli3-preset.html)新生成的项目已集成。

## 工作流程

每次进行`git commit`的时候会进行`npm run lint {提交的文件} --fix`，如果`lint`没通过，则无法进行提交。

![](https://ws1.sinaimg.cn/large/006tNc79ly1g2jng2awhpj315i0qcq3b.jpg)


## 配置

仅对于以前[vue-cli3](vue-cli3-preset.html)生成的项目。

### eslint autofix

开发当中如果编写的代码不符合`eslint`规则，在保存时会自动修复为符合规则的代码，效果如下：

![](https://ws3.sinaimg.cn/large/006tNc79ly1g2jnuffteog30cu05kaam.gif)

以前生成的项目，因为官方依赖`@vue/cli-plugin-eslint`的问题，导致`autofix`无效(新生成的项目自动为最新版本的官方依赖，已解决此问题)。此处进行更新即可。

```bash
npm i -D @vue/cli-plugin-eslint
```

### 配置eslint

```bash
npm i -D @vue/eslint-config-prettier eslint-plugin-prettier && npm uninstall @vue/eslint-config-airbnb
```

并将如下配置覆盖`package.json`中的`prettier`配置和`eslintConfig`配置：

```json
"prettier": {
  "printWidth": 120,
  "singleQuote": true, 
  "trailingComma": "es5"
},
"eslintConfig": {
  "root": true,
  "env": {
    "node": true
  },
  "extends": [
    "plugin:vue/essential",
    "@vue/prettier"
  ],
  "rules": {
    "no-console": "off",
    "import/extensions": "off",
    "import/no-dynamic-require": "off",
    "import/no-unresolved": "off",
    "import/no-cycle": "off",
    "global-require": "off",
    "no-alert": "off",
    "max-len": "off",
    "no-restricted-globals": "off",
    "no-unused-vars": "warn",
    "consistent-return": "off",
    "prefer-promise-reject-errors": "off",
    "no-param-reassign": "off",
    "no-shadow": "off",
    "no-lonely-if": "off"
  },
  "parserOptions": {
    "parser": "babel-eslint"
  }
},
```

### 配置hooks

安装[husky](https://github.com/typicode/husky)和[lint-staged](https://github.com/okonet/lint-staged)。

```bash
npm i -D husky lint-staged
```
在`package.json`中添加如下配置即可。

```js
"husky": {
  "hooks": {
    "pre-commit": "lint-staged"
  }
},
"lint-staged": {
  "*.{js,jsx,vue}": ["npm run lint", "git add"]
},
```

配置成功后，每次进行`commit`操作都会先`lint`提交的文件，如果有`error`，则提交失败，效果如下：

![](https://ws2.sinaimg.cn/large/006tNc79ly1g2jp46kuypg30dd0b21dj.gif)

## 其它

[eslint rules](https://eslint.org/docs/rules/)

[eslint rules中文版](http://eslint.cn/docs/rules/)

### 忽略单行检查

```js
const a = 1;
const b = {}; // eslint-disable-line
const c = true;
```

### 忽略某条规则
```js
/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  store,
  components: { App },
  template: '<App/>',
});
```

### 忽略多行检查

`/* eslint-disable */` 和 `/* eslint-enable */` 组合使用。

```js
/* eslint-disable */
const myCar = new Object();
myCar.make = 'Ford';
myCar.model = 'Mustang';
myCar.year = 1969;
/* eslint-enable */
```

### 忽略整个文件

```js
/* eslint-disable */
import Vue from 'vue';
import App from './App';
import router from './router';

Vue.config.productionTip = false;

new Vue({
  el: '#app',
  router,
  template: '<App/>',
  components: { App }
});
```

### 以下代码全部忽略
```js
import Vue from 'vue';
import App from './App';
import router from './router';
/* eslint-disable */
Vue.config.productionTip = false;

new Vue({
  el: '#app',
  router,
  template: '<App/>',
  components: { App }
});
```




