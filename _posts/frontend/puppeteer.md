# puppeteer简介
<!-- author:lingke.wang@mljr.com -->

## 安装

```bash
npm i puppeteer
# or "yarn add puppeteer"
```
`chrome`浏览器比较大，安装时可能会报以下错误：

![](http://ww4.sinaimg.cn/large/006tNc79ly1g40kwux1j7j30sx07mq36.jpg)


通过设置环境变量跳过`chrome`下载：

```bash
env PUPPETEER_SKIP_CHROMIUM_DOWNLOAD=true npm i puppeteer
```

安装完成之后，查看`node_modules`目录中`puppeteer`中引用的`chrome`版本:

![](http://ww4.sinaimg.cn/large/006tNc79ly1g40l1ut8nbj308701ia9u.jpg)

然后去[https://npm.taobao.org/mirrors/chromium-browser-snapshots/](https://npm.taobao.org/mirrors/chromium-browser-snapshots/)手动下载对应的版本。


## 使用

使用时需手动配置`chrome`路径

``` js
const puppeteer = require('puppeteer');

(async () => {
// const pathToExtension = require('path').join(__dirname, 'chrome-win/chrome.exe');
const pathToExtension = require('path').join(__dirname, 'Chromium.app/Contents/MacOS/Chromium');
const browser = await puppeteer.launch({
    headless: false,
    executablePath: pathToExtension,
});    
})();
```
官网[https://pptr.dev/](https://pptr.dev/)