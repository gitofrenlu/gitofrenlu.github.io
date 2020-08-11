# React 基础(1)

# React 项目(PC)

**前言**:

1. 应用框架`Dva(antd)`对`react`和`redux`进行了封装，简化了开发代价]、`UI`库是`antd`

2. 参考资料 [antd官网地址](https://ant.design)

3. 地址上面有快速入手应用实例，可以照上面来做

**一：安装、创建项目**   
1. `dva-cli`  

```
$ npm install dva-cli -g  
```

2. 创建新应用 

```
$ dva new dva-quickstart  
$ cd dva-quickstart $ npm start  
```

**二**：其中要说明的就是`mock`数据，我当时项目里应用的是`mock2easy`,[mock2easy官网](https://www.npmjs.com/package/mock2easy)

上面地址中有介绍用法和一些规则，下面我就来说一下在项目里面怎样应用
+ `npm install mock2easy --save`
+ 可能刚刚`init`下来的项目中没有这个`js`，如果没有自己在根目录加一个`.roadhogrc.mock.js`
 里面的配置如下：(这个相当于利用`mock2easy`自己本地起了一个服务)  

```
var mock2easy = require('mock2easy');

var defaultConfig = {
    port: 8006,
    lazyLoadTime: 3000,
    database: 'mock2easy',
    doc: 'doc',
    ignoreField: [],
    interfaceSuffix: '.json',
    preferredLanguage: 'en'
};

mock2easy(defaultConfig, function (app) {
    app.listen(defaultConfig.port, function () {
        console.log(('mock2easy is starting , please visit : http://127.0.0.1:' + defaultConfig.port).bold.cyan);
    });
});
```

**三**：第三点要说明的是这个应用的构建工具是`roadhog`(底层实现的是`webpack`),如果有些需要自己配置得到可以上网查询配置信息  

**四**：目录结构如下：
```
    ├── ├── mock2easy                       mock包
    ├── build                           打包后的文件目录
    ├── src                             项目源码目录    
    │   ├── components                     公共组件目录
    │   ├── routes                         前端页面存放地
    │   ├── models                         应用逻辑层(redux-saga)
    │   └── router                         前端路由 
    │   ├── service                        服务层代码,主要供models层调用
    │   └── utils                          前端路由 
    │   ├── index.js                       自动生成,主入口
    │   ├── index.css                      自动生成
    ├── .babelrc                        ES6语法编译配置
    ├── .editorconfig                   代码格式
    ├── .eslintrc                       eslint配置
    ├── .gitignore                      git上传忽略配置
    ├── .roadhog                        配置文件,底层实现webpack
    ├── .roadhogrc.mock.js              配置本地静态服务
    ├── package.json                    npm配置
    ├── README.md                       项目说明
    
```

说明：其中主要都在`src`中，`routes`这个文件夹写主页面(可以改成其他名字`contains`)、`router`配置路由的地方、models主要存放逻辑的、services中主要的发请求的代码写在里面


# React 项目(移动)

**前言**： 

1. `antd-mobile` 有基于`web`的和基于`rn`的，我当时用的是`web`的，所以下面主要讲一下`web`的配置 

2. [antd-mobile参考文档](https://mobile.ant.design/docs/react/introduce-cn)  

一：安装和初始化#（可以使用 dva / `create-react-app` 新创建的空项目，你也可以从 官方示例 脚手架里拷贝并修改）

1. 我当时用的是第二种方法，第一种init下来的项目构建工具是`roadhug`(底层基于`webpack`),第二个`init`下来的项目是webpack，可能现在大多数项目里面用的都是webpack,好上手对于我们  

2. 拉下代码后就开始`npm install`你需要的工具（这个看项目需要） 
 
3. 配置

+ 引入样式`import 'antd-mobile/dist/antd-mobile.css'`;  
+ 配置`.babelrc`  
+ `Webpack.config.js`里面基本都有了，输入、输出、`loaders`等，但是需要我们额外配置，自己就在里面加，比如：

// `px`转成`rem`，根据实际情况是否添加
```
webpackConf.module.loaders.push({
    test:/\.(jsx|js|css|less)$/,
    loader:'webpack-px-to-rem',
    //这个配置是可选的 
    query: {
        // 1rem=npx，建议75，750宽度分成10rem 
        basePx: 100,
        //只会转换大于min的px 默认为0 
        //因为很小的px（比如border的1px）转换为rem后在很小的设备上结果会小于1px，有的设备就会不显示 
        min: 1,
        //转换后的rem值保留的小数点后位数 默认为3 
        floatWidth: 3
    }
});

webpackConf.externals = {
    'jquery': 'jQuery',
    'zepto': 'Zepto',
    "react": 'React',
    "react-dom": 'ReactDOM'
};
```