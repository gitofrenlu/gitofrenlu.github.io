# backstopjs
<!-- author:ying.lin@mljr.com -->

# 什么是backstop
BackstopJS就是一个能够实现css自动化回归测试的工具，BackstopJS是一个基于比较网站快照的变化的回归测试工具，非常适合给项目中的样式做回归测试，可以确保我们在重构网站样式的时候样式不发生变化，而且他支持设置多种浏览器尺寸，可以测试响应式布局。
比较下resemble.js 和phantomCss,backstop算是比较容易上手使用的,方便配置,phantomCss配合casper进行截图回归测试还是挺不错的,但是两张图diff的话不是很稳定,并且慢,目前phantomCss已经不维护了,不再支持最新phantomjs,resemblejs呢,入手困难,文档不容易理解,网络上只有英文文档,国内介绍resemblejs的文章很少.
## 安装方法:
```
npm i backstopjs -g
```
输入
```
backstop -v //显示版本号标明安装成功
```

## 目录介绍

```js
├── backstop_data
│   └── bitmaps_reference //存储样板图的地方
│   └── bitmaps_test  //截图存储的地方
│   └── engine_scripts //配置静态服务器,自定义脚本casper/puppet/chromy
│       └──clickAndHoverHelper
│       └──loadCookies  
│       └──onBefore
│       └──onReady 
│       └──waitForHelperHelper       
│   └── html_report  //执行test后弹出的html界面
├── backstop.json  //backstop的配置文件

```
配置文件介绍 ----连蒙带猜
```json
{
    //测试用例id，用于屏幕截图命名。BackstopJS将自动为您生成一个，以避免命名与BackstopJS资源的冲突。
    "id": "backstop_default",
    //测试视口，就是配置各种分辨率
    "viewports": [{
            "label": "phone", // iphone 
            "width": 320,
            "height": 480
        },
        {
            "label": "phone", // iphone 
            "width": 750,
            "height": 960
        },
        {
            "label": "tablet", //平板
            "width": 1024,
            "height": 768
        }
    ],
    //在执行所有脚本前、页面加载后执行的脚本。通过他我们可以执行用express做一个静态服务器 支持casper chromy puppet
    "onBeforeScript": "puppet/onBefore.js",
    "onReadyScript": "puppet/onReady.js",
    //测试用例
    "scenarios": [{
        "label": "BackstopJS Homepage", //测试名称
        "cookiePath": "backstop_data/engine_scripts/cookies.json", //设置cookies
        "url": "https://garris.github.io/BackstopJS/", //所测试的url
        "referenceUrl": "", //创建引用时指定不同的状态或环境。
        "readyEvent": "", //预定义的字符串记录到控制台来触发屏幕捕获。---实现异步交互
        "readySelector": "", //等到此选择器存在后再继续 ---实现异步交互
        "delay": 0, //等待几秒后
        "hideSelectors": [], //设置为visibility的选择器数组：hidden
        "removeSelectors": [], //设置为display的选择器数组：none
        "hoverSelector": "", //满足上述条件后 - 使用此脚本修改屏幕前的UI状态镜头，例如悬停，点击等
        "clickSelector": "", //在屏幕截图之前单击指定的DOM元素。
        "clickSelectors": "", // *仅限Puppeteer *获取selctors数组 - 模拟多个顺序点击交互。
        "postInteractionWait": 0, //在与hoverSelector或clickSelector交互后等待选择器（可选择接受以ms为单位的等待时间。想法用于单击或悬停元素转换。默认使用onReadyScript）
        "selectors": [], //选择需要截图的选择器
        "selectorExpansion": true, //定位元素
        "expect": 0, //跟选择器配合使用,说期望找到的选择器的数量跟配置的数量是否匹配,不匹配的话表示测试失败
        "misMatchThreshold": 0.1, //允许通过测试的不同像素的百分比
        "requireSameDimensions": true //测试必须与参考尺寸相同
    }],
    "paths": {
        "bitmaps_reference": "backstop_data/bitmaps_reference", //存储样板图
        "bitmaps_test": "backstop_data/bitmaps_test", //截图输出路径
        "engine_scripts": "backstop_data/engine_scripts", //js配置路径
        "html_report": "backstop_data/html_report", //显示对比图的html
        "ci_report": "backstop_data/ci_report"
    },
    //报告的形式，支持命令行和浏览器两种
    "report": ["browser"],
    "engine": "casper", //配置引擎属性,slimerjs(Gecko / Mozilla,需要安装),casper,chromy(webkit)
    "engineOptions": { //配置引擎属性的默认值
        "casperFlags": [
            "--engine=slimerjs",
            "--proxy-type=http",
            "--proxy=proxyIp:port",
            "--proxy-auth=user:pass"
        ]
    },
    "asyncCaptureLimit": 5, //一次能捕获5个屏幕
    "asyncCompareLimit": 50, //配置测试期间所需的RAM量
    //是否打印测试日志
    "debug": false,
    "debugWindow": false,
    "resembleOutputOptions": {  //比较差异输出图片的配置
        "errorColor": {
            "red": 255,
            "green": 0,
            "blue": 255
        },
        "errorType": "movement",
        "transparency": 0.3,
        "ignoreAntialiasing": true
    }
}

```
## 使用方法
backstop很方便,开箱即用
在文件里执行,便工具初始化了
```
backstop init 
```

里面有写好的例子直接执行,在backstop_data/bitmaps_test 可以看到截图
```
backstop test
```
把上一个backstop test所存储的截图设置为样板图,再次执行 backstop test的时候,当前截图跟样板图进行比较,生成diff图
```
backstop approve 
```
执行backstop reference ,能把当前配置的图片存储到bitmaps_reference,当再次执行 backstop test 的时候,会把当前截图跟样板图进行比较,生成diff图
```
backstop reference 
```
如何对UI?  
可以跟设计稿比较,把设计稿按照存到bitmaps_reference,命名规范按照backstop给的格式命名,直接执行backstop test 就能看到设计稿跟自己写的网页区别哪里,节约对UI的时间


还能为了能够和服务器集成，我们使用commonjs模块的形式调用backstopjs，
引入方法
```js
const  backstop  =  require（' backstopjs '）;

//执行
backstop('test')
  .then(() => {
    // test successful
  }).catch(() => {
    // test failed
  });
```

例子
```js
var http = require('http');
var express = require('express');
var backstop = require('backstopjs');
var path = require('path');
var app = express();
app.use("/", express.static(path.join(__dirname ,'../html/')));

// 创建服务端
var sever = http.createServer(app).listen('3000', function() {
    //执行backstop test
    backstop('test').then(function () {
        sever.close();
    });

     //执行backstop reference
    // backstop('reference').then(function () {
       // sever.close();
   // });
});
 ```

当前项目里安装配置backstop,可通过npm 执行脚本,只要全局安装了packstopjs,在package.json配置,执行npm run init 便能使用了
 ```json
 "scripts": {
  "approve": "backstop approve",
  "test": "backstop test", //注意别跟vue-cli下的e2e/unit自动化测试命令重复了
  "init": "backstop init"
}
 ```

 ## 有两个疑问?

 - backstop.json 里的 viewports 截图大小配置不管用,除了官网,我暂时没查出什么原因
 - 执行 backstop test 有时候会报错,但是不影响使用
