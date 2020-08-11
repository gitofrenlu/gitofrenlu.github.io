# electron

## 简介
[electron](https://electronjs.org/)是由Github开发，是一个用Html、css、JavaScript来构建桌面应用程序的开源库，可以打包为Mac、Windows、Linux系统下的应用。

electron是一个运行时环境，包含Node和Chromium，可以理解成把web应用运行在node环境中

## 结构

electron主要分为主进程和渲染进程，关系如下图

![](https://ws3.sinaimg.cn/large/0069RVTdly1fv81sysfcbj30mk0dn0th.jpg)

electron运行`package.json`中的`main`字段标明脚本的进程称为**主进程**

在主进程创建web页面来展示用户页面，**一个electron有且只有一个主进程**

electron使用Chromium来展示web页面，每个页面运行在自己的`渲染进程`中


## 快速开始

接下来，让代码来发声，雷打不动的`hello world`

创建文件夹，并执行`npm init -y`，生成`package.json`文件，下载`electron`模块并添加开发依赖
```bash
mkdir electron_hello && cd electron_hello && npm init -y && npm i electron -D
```
*下载速度过慢或失败，请尝试使用cnpm，安装方式如下*

```bash
# 下载cnpm
npm install -g cnpm --registry=https://registry.npm.taobao.org
# 下载electron
cnpm i electron -D
```

创建index.js，并写入以下内容

```js
const {app, BrowserWindow} = require('electron')

// 创建全局变量并在下面引用，避免被GC
let win

function createWindow () {
    // 创建浏览器窗口并设置宽高
    win = new BrowserWindow({ width: 800, height: 600 })
    
    // 加载页面
    win.loadFile('./index.html')
    
    // 打开开发者工具
    win.webContents.openDevTools()
    
    // 添加window关闭触发事件
    
    win.on('closed', () => {
        win = null  // 取消引用
    })
}

// 初始化后 调用函数
app.on('ready', createWindow)  

// 当全部窗口关闭时退出。
app.on('window-all-closed', () => {
   // 在 macOS 上，除非用户用 Cmd + Q 确定地退出，
   // 否则绝大部分应用及其菜单栏会保持激活。
   if (process.platform !== 'darwin') {
        app.quit()
   }
})
  
app.on('activate', () => {
// 在macOS上，当单击dock图标并且没有其他窗口打开时，
// 通常在应用程序中重新创建一个窗口。
    if (win === null) {
      createWindow()
    }
})
```

创建index.html
```html
<!DOCTYPE html>
<html>
    <head>
      <meta charset="UTF-8">
      <title>Hello World!</title>
    </head>
    <body>
        <h1 id="h1">Hello World!</h1>
        We are using node
        <script>
            document.write(process.versions.node)
        </script>
        Chrome
        <script>
            document.write(process.versions.chrome)
        </script>,
        and Electron
        <script>
            document.write(process.versions.electron)
        </script>
    </body>
</html>
```
最后，修改`packge.json`中的main字段,并添加start命令

```js
{
    ...
    main:'index.js',
    scripts:{
        "start": "electron ."
    }
}
```

执行`npm run start`后，就会弹出我们的应用来。

![](https://ws3.sinaimg.cn/large/0069RVTdly1fv81tdfadzj319q0zytau.jpg)

## 调试

我们知道electron有两个进程，主进程和渲染进程，开发过程中我们需要怎么去调试它们呢？老太太吃柿子，咱们捡软的来

### 渲染进程

BrowserWindow 用来创建和控制浏览器窗口，我们调用它的实例上的API即可

```js
win = new BrowserWindow({width: 800, height: 600})
win.webContents.openDevTools() // 打开调试
```

调试起来是和Chrome是一样的，要不要这么酸爽

![](https://ws3.sinaimg.cn/large/0069RVTdly1fv81txjwwgj31fo0rwq5v.jpg)

### 主进程

使用VSCode进行调试

使用VSCode打开项目，点击调试按钮

![](https://ws2.sinaimg.cn/large/0069RVTdly1fv81uaxukuj30r00momxj.jpg)

点击调试后的下拉框

![](https://ws4.sinaimg.cn/large/0069RVTdly1fv81uk8pdbj30gg0a8glj.jpg)

选择添加配置，选择node

![](https://ws4.sinaimg.cn/large/0069RVTdly1fv81urbc23j31he0emgmc.jpg)

此时会把默认的调试配置打开，大概长这样

![](https://ws2.sinaimg.cn/large/0069RVTdly1fv81v9pzzbj31900n8jse.jpg)

什么？你的不是，不是的话，就直接把下面的复制并替换你的配置

差不多这么办，那就把`configurations`里面第二项复制到你的`configurations`配置里面，第一个配置是用来调试node的

```js
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "启动程序",
      "program": "${workspaceFolder}/main.js"
    },
    {
        "name": "Debug Main Process",
        "type": "node",
        "request": "launch",
        "cwd": "${workspaceFolder}",
        "runtimeExecutable": "${workspaceFolder}/node_modules/.bin/electron",
        "windows": {
          "runtimeExecutable": "${workspaceFolder}/node_modules/.bin/electron.cmd"
        },
        "args" : ["."]
      }
  ]
}
```

可以看到`${workspaceFolder}`，这是关于VSCode的变量，用来表示当前打开的文件夹的路径

[更多变量请猛戳这里](https://code.visualstudio.com/docs/editor/variables-reference)

修改完配置后，我们调试面板，选择我们刚才配置的

![](https://ws4.sinaimg.cn/large/0069RVTdly1fv81w2b084j30ju05o0ss.jpg)

在代码中标记需要调试的地方，然后点击绿色的小三角，就可以愉快的调试了

![](https://ws3.sinaimg.cn/large/0069RVTdly1fv81w945muj31iu0usju4.jpg)


## 进程通信

在Electron中, GUI 相关的模块 (如 dialog、menu 等) 仅在主进程中可用, 在渲染进程中不可用。 为了在渲染进程中使用它们, ipc 模块是向主进程发送进程间消息所必需的，以下介绍几种进程间通讯的方法。

### 哥俩好

[ipcMain](https://electronjs.org/docs/api/ipc-main)和[ipcRenderer](https://electronjs.org/docs/api/ipc-renderer)是两个好基友，通过这两个模块可以实现进程的通信。

+ ipcMain
 在主进程中使用，用来处理渲染进程（网页）发送的同步和异步的信息
 
+ ipcRenderer
在渲染进程中使用，用来发送同步或异步的信息给主进程，也可以用来接收主进程的回复信息。

以上两个模块的通信，可以理解成`发布订阅模式`,接下来，我们看下它们具体的使用方法

**主进程**

```js
const {ipcMain} = require('electron')

// 监听渲染程序发来的事件
ipcMain.on('something', (event, data) => {
    event.sender.send('something1', '我是主进程返回的值')
})
```

**渲染进程**

```js
const { ipcRenderer} = require('electron') 

// 发送事件给主进程
ipcRenderer.send('something', '传输给主进程的值')  

// 监听主进程发来的事件
ipcRenderer.on('something1', (event, data) => {
    console.log(data) // 我是主进程返回的值
})
```
以上代码使用的是异步传输消息，electron也提供了同步传输的API。

*发送同步消息将会阻塞整个渲染进程，你应该避免使用这种方式 - 除非你知道你在做什么。*

*切忌用 ipc 传递大量的数据，会有很大的性能问题，严重会让你整个应用卡住。*


### [remote模块](https://electronjs.org/docs/api/remote)

使用 remote 模块, 你可以调用 main 进程对象的方法, 而不必显式发送进程间消息。

```js
const { dialog } = require('electron').remote
dialog.showMessageBox({type: 'info', message: '在渲染进程中直接使用主进程的模块'})
```


### [webContents](https://electronjs.org/docs/api/web-contents#contentssendchannel-arg1-arg2-)

webContents负责渲染和控制网页, 是 BrowserWindow 对象的一个属性, 我们使用send方法向渲染器进程发送异步消息。

**主进程**

```js
const {app, BrowserWindow} = require('electron')

let win

app.on('ready', () => {

    win = new BrowserWindow({width: 800, height: 600})
    
    // 加载页面
    win.loadURL('./index.html')
    
    // 导航完成时触发，即选项卡的旋转器将停止旋转，并指派onload事件后。
    win.webContents.on('did-finish-load', () => {

        // 发送数据给渲染程序
        win.webContents.send('something', '主进程发送到渲染进程的数据')
    })
})
```

**渲染进程**

```html
<!DOCTYPE html>
<html>
    <head>
    </head>
    <body>
        <script>
            require('electron').ipcRenderer.on('something', (event, message) => {
                console.log(message) // 主进程发送到渲染进程的数据
            })
        </script>
    </body>
</html>
```

### 渲染进程数据共享

更多情况下，我们使用HTML5 API实现，如localStorage、sessionStorage等，也可以使用electron的IPC机制实现

**主进程**

```js
global.sharedObject = {
    someProperty: 'default value'
}
```

**渲染进程**

第一个页面
```js
require('electron').remote.getGlobal('sharedObject').someProperty = 'new value'
```

第二个页面

 ```js
 console.log(require('electron').remote.getGlobal('sharedObject').someProperty) // new value
 ```

### 总结

以上四个方法均可实现主进程和渲染进程的通信，可以发现使用`remote`模块是最简单的，渲染进程代码中可以直接使用`electron`模块

## 常用模块

### 快捷键与菜单

#### 本地快捷键

![](https://ws2.sinaimg.cn/large/0069RVTdly1fv81wpvqxqj30kw06ojrj.jpg)

只有在应用聚焦的时候才会触发，主要代码如下

**官方文档亲测没有效果**

```js
const { Menu, MenuItem } = require('electron')
const menu = new Menu()
  
menu.append(new MenuItem({
  label: '自定义快捷键',
  submenu: [
    {
      label: '测试',
      accelerator: 'CmdOrCtrl+P',
      click: () => { 
          console.log('我是本地快捷键')
      }
    }
  ]
}))
Menu.setApplicationMenu(menu)
```

#### 全局快捷键

注册全局，无论应用是否聚焦，都会触发，使用`globalShortcut`来注册， 主要代码如下, 

```js
const {globalShortcut, dialog} = require('electron')

app.on('read', () => {
    globalShortcut.register('CmdOrCtrl+1', () => {
        dialog.showMessageBox({
            type: 'info',
            message: '你按下了全局注册的快捷键'
          })
    })
})
```



显示如下，使用了`dialog`模块



![](https://ws4.sinaimg.cn/large/0069RVTdly1fv81wwjgnyj310o0fcdgq.jpg)

#### 上下文菜单

上下文菜单就是我们点击右键的时候显示的菜单, 设置在渲染进程里面

![](https://ws1.sinaimg.cn/large/0069RVTdly1fv81x1sz5sj30o80ju0sr.jpg)


主要代码如下

```js
const remote = require('electron').remote;  // 通过remote模块使用主程序才能使用的模块
const Menu = remote.Menu;
const MenuItem = remote.MenuItem;
var menu = new Menu();
menu.append(new MenuItem({ label: 'MenuItem1', click: function() { console.log('item 1 clicked'); } }));
menu.append(new MenuItem({ type: 'separator' }));
menu.append(new MenuItem({ label: 'MenuItem2', type: 'checkbox', checked: true }));

window.addEventListener('contextmenu', (e) => {
  e.preventDefault();
  menu.popup(remote.getCurrentWindow());
}, false);
```

#### 应用菜单

![](https://ws1.sinaimg.cn/large/0069RVTdly1fv81x9rgxej319a0huaas.jpg)

上文中我们通过`Menu`模块创建了本地快捷键，应用菜单也是用`Menu`模块，我们看到长得好像一样，其实实现起来也差不多，我们这里使用创建模版的方式来实现，主要代码如下：

```js
const {app, Menu} = require('electron')
const template = [
  {
    label: '操作',
    submenu: [{
        label: '复制',
        accelerator: 'CmdOrCtrl+C',
        role: 'copy'
    }, {
        label: '粘贴',
        accelerator: 'CmdOrCtrl+V',
        role: 'paste'
    }, {
        label: '重新加载',
        accelerator: 'CmdOrCtrl+R',
        click: function (item, focusedWindow) {
            if (focusedWindow) {
                // on reload, start fresh and close any old
                // open secondary windows
                if (focusedWindow.id === 1) {
                    BrowserWindow.getAllWindows().forEach(function (win) {
                        if (win.id > 1) {
                            win.close()
                        }
                    })
                }
                focusedWindow.reload()
            }
        }
    }]
  },
  {
    label: '加载网页',
    submenu: [
      {
        label: '优酷',
        accelerator: 'CmdOrCtrl+P',
        click: () => { console.log('time to print stuff') }
      },
      {
        type: 'separator'
      },
      {
        label: '百度',
      }
    ]
  }
]

if (process.platform === 'darwin') {
    const name = electron.app.getName()
    template.unshift({
      label: name,
      submenu: [{
        label: `关于 ${name}`,
        role: 'about'
      }, {
        type: 'separator'
      }, {
        label: '服务',
        role: 'services',
        submenu: []
      }, {
        type: 'separator'
      }, {
        label: `隐藏 ${name}`,
        accelerator: 'Command+H',
        role: 'hide'
      }, {
        label: '隐藏其它',
        accelerator: 'Command+Alt+H',
        role: 'hideothers'
      }, {
        label: '显示全部',
        role: 'unhide'
      }, {
        type: 'separator'
      }, {
        label: '退出',
        accelerator: 'Command+Q',
        click: function () {
          app.quit()
        }
      }]
    })
}

app.on('read', () => {
    const menu = Menu.buildFromTemplate(template);
    Menu.setApplicationMenu(menu);
})

```

#### 系统托盘

![](https://ws4.sinaimg.cn/large/0069RVTdly1fv81xhmwd9j307o04a0sl.jpg)

主要代码
 
```js
const { app, Tray } = require('electron') 
/* 省略其他代码 */
let tray;
app.on('ready', () => {
    tray = new Tray(__dirname + '/build/icon.png');//系统托盘图标
    
    const contextMenu = Menu.buildFromTemplate([ // 菜单项
      {label: '显示', type: 'radio', click: () => {win.show()}},
      {label: '隐藏', type: 'radio', click: () => {win.hide()}},
    ])
    
    // tray.on('click', () => { //  鼠标点击事件最好和菜单只设置一种
    //   win.isVisible() ? win.hide() : win.show()
    // })
    
    tray.setToolTip('This is my application.') // 鼠标放上时候的提示
    
    tray.setContextMenu(contextMenu) // 应用菜单项
})

```

#### 模块一览

[Menu](https://electronjs.org/docs/api/menu)

[MenuItem](https://electronjs.org/docs/api/menu-item)

[globalShortcut](https://electronjs.org/docs/api/global-shortcut)

[dialog](https://electronjs.org/docs/api/dialog)

[tray](https://electronjs.org/docs/api/tray)

### 打开页面

#### shell

主进程打开页面，会调用默认的浏览器打开页面，使用`shell`模块

```js
const { app,shell } = require('electron')

app.on('ready', () => {
    shell.openExternal('github.com')
})
```
应用启动后就会使用默认浏览器打开`github`页面

#### window.open

渲染进程中使用，当前应用打开页面 

```js
window.open('github.com')
```

#### webview

使用`webview`可以在一个独立的 frame 和进程里显示外部 web 内容

![](https://ws2.sinaimg.cn/large/0069RVTdly1fv81xr1o1wj31go0xmq6a.jpg)

直接在index.html中使用即可

```html
<webview id="foo" src="https://www.github.com/" style="display:inline-flex; width:640px; height:480px"></webview>
```

#### 模块一览

[shell](https://electronjs.org/docs/api/shell)

[window.open](https://electronjs.org/docs/api/window-open)

[webview](https://electronjs.org/docs/api/webview-tag)

### QA

其他应用模块和API请下载

[演示应用程序](https://github.com/electron/electron-api-demos)

[演示应用程序中文版](https://github.com/demopark/electron-api-demos-Zh_CN)

## 模版

### electron-forge
[electron-forge](https://electronforge.io/templates)包含`vue`、`react`、`Angular`等开箱即用的模版。

```bash
npm i -g electron-forge
electron-forge init my-app template=react
cd my-app
npm run start 
```

目录结构如下

![](https://ws4.sinaimg.cn/large/0069RVTdly1fv81y7sakmj31j60zudia.jpg)


`.compilerc`是`electron-compile`的配置文件,和`.babelrc`类似

[electron-compile](https://github.com/electron-userland/electron-compile)是一种转换规则，支持在应用程序中编译js和css


### electron-react-boilerplate

如果你不希望任何工具，而想要简单地从一个模板开始构建,react-electron模版可以了解下。

[electron-react-boilerplate](https://github.com/chentsulin/electron-react-boilerplate) 是双`package.json`配置项目， 目录如下

![](https://ws2.sinaimg.cn/large/0069RVTdly1fv81yr9mwvj30b30njwex.jpg)

### electron-vue

[electron-vue](https://github.com/SimulatedGREG/electron-vue)充分利用 vue-cli 作为脚手架工具，加上拥有 vue-loader 的 webpack、electron-packager 或是 electron-builder，以及一些最常用的插件，如vue-router、vuex 等等。

目录结构如下

![](https://ws3.sinaimg.cn/large/0069RVTdly1fv81ywkzyoj30av0ibt8t.jpg)

[中文教程](https://simulatedgreg.gitbooks.io/electron-vue/content/cn/)

### 更多模版
更多模版[请访问](https://github.com/sindresorhus/awesome-electron#boilerplates)


## 打包

怎么将我们开发好的应用打包成`.app`或`.exe`的执行文件，这就涉及到了重要的打包环节,
这里使用[electron-quick-start](https://github.com/electron/electron-quick-start)项目进行打包

目前，主流的打包工具有两个[electron-packager](https://github.com/electron-userland/electron-packager)和[electron-builder](https://github.com/electron-userland/electron-builder)

Mac打包window安装包需下载`wine`

`brew install wine`

如果有丢失组件，按照报错信息进行下载即可

###  electron-packager

[electron-packager](https://github.com/electron-userland/electron-packager)把你的electron打包成可运行文件(.app, .exe, etc)


执行`npm i electron-packager -D`进行安装

`electron-packager .` 快速打包

#### 打包详解
```js
electron-packager <sourcedir> <appname> --platform=<platform> --arch=<arch> --out=out --icon=assets/app.ico --asar --overwrite --ignore=.git
```
+ sourcedir 项目入口 根据package.json的目录
+ appname 包名
+ platform 构建平台 包含 `darwin, linux, mas, win32 all`
+ arch 构建架构 包含`ia32,x64,armv7l,arm64`
+ out 打包后的地址
+ icon 打包图标
+ asar 是否生成app.asar, 不然就是自己的源码
+ overwrite 覆盖上次打包
+ ignore 不进行打包的文件

第一次打包需要下载二进制的包耗时会久一些，以后走缓存就快的多了。

打包后目录为

![](https://ws1.sinaimg.cn/large/0069RVTdly1fv81zhx31pj30gr06x3yf.jpg)

其中`electron-quick-start`可以直接打开运行

**缓存地址**
+ Mac `~/Library/Caches/electron/`
+ Windows `$LOCALAPPDATA/electron/Cache` or `~/AppData/Local/electron/Cache/`
+ Linux  `$XDG_CACHE_HOME` or `~/.cache/electron/`

### electron-builder

[electron-builder](https://github.com/electron-userland/electron-builder)是一个完整的解决方案，对于macos、windows、linux下的electron app，它可以提供打包及构建的相关功能。同时，它还提供开箱即用的“自动更新”功能支持

`npm i electron-builder -D` 下载

`electron-builder`打包

出现如下

![](https://ws4.sinaimg.cn/large/0069RVTdly1fv81zoowe8j30qj05374k.jpg)

同样是第一次打包时间会比较长，别着急，静候佳音。

会默认打包在`dist`目录，包含如下

![](https://ws3.sinaimg.cn/large/0069RVTdly1fv81zv4ijbj30ji04kjrf.jpg)

`mac`文件里有`运行时文件`，默认使用asar方式进行打包

`electron-builder --dir` 只会生成包文件，对测试有用

![](https://ws2.sinaimg.cn/large/0069RVTdly1fv8200jyb5j309n04sjr7.jpg)


**坑坑坑**

第一次打包的时候会比较慢，如果你和我手欠直接退出了，再次打包的时候，恭喜你，出错了。报错信息如下

```js
  • electron-builder version=20.28.2
  • loaded configuration file=package.json ("build" field)
  • description is missed in the package.json appPackageFile=/Users/shisan/Documents/self/you-app/package.json
  • writing effective config file=dist/builder-effective-config.yaml
  • no native production dependencies
  • packaging       platform=darwin arch=x64 electron=2.0.7 appOutDir=dist/mac
  • cannot unpack electron zip file, will be re-downloaded error=zip: not a valid zip file
  ⨯ zip: not a valid zip file

Error: /Users/shisan/Documents/self/you-app/node_modules/app-builder-bin/mac/app-builder exited with code 1
    at ChildProcess.childProcess.once.code (/Users/shisan/Documents/self/you-app/node_modules/builder-util/src/util.ts:254:14)
    at Object.onceWrapper (events.js:272:13)
    at ChildProcess.emit (events.js:180:13)
    at maybeClose (internal/child_process.js:936:16)
    at Process.ChildProcess._handle.onexit (internal/child_process.js:220:5)
From previous event:
    at unpack (/Users/shisan/Documents/self/you-app/node_modules/app-builder-lib/out/electron/ElectronFramework.js:191:18)
    at Object.prepareApplicationStageDirectory (/Users/shisan/Documents/self/you-app/node_modules/app-builder-lib/src/electron/ElectronFramework.ts:148:50)
    at /Users/shisan/Documents/self/you-app/node_modules/app-builder-lib/src/platformPackager.ts:179:21
    at Generator.next (<anonymous>)
From previous event:
    at MacPackager.doPack (/Users/shisan/Documents/self/you-app/node_modules/app-builder-lib/src/platformPackager.ts:166:165)
    at /Users/shisan/Documents/self/you-app/node_modules/app-builder-lib/src/macPackager.ts:88:63
    at Generator.next (<anonymous>)
From previous event:
    at MacPackager.pack (/Users/shisan/Documents/self/you-app/node_modules/app-builder-lib/src/macPackager.ts:80:95)
    at /Users/shisan/Documents/self/you-app/node_modules/app-builder-lib/src/packager.ts:376:24
    at Generator.next (<anonymous>)
    at xfs.stat (/Users/shisan/Documents/self/you-app/node_modules/fs-extra-p/node_modules/fs-extra/lib/mkdirs/mkdirs.js:56:16)
    at /Users/shisan/Documents/self/you-app/node_modules/graceful-fs/polyfills.js:287:18
    at FSReqWrap.oncomplete (fs.js:171:5)
From previous event:
    at Packager.doBuild (/Users/shisan/Documents/self/you-app/node_modules/app-builder-lib/src/packager.ts:344:39)
    at /Users/shisan/Documents/self/you-app/node_modules/app-builder-lib/src/packager.ts:314:57
    at Generator.next (<anonymous>)
    at /Users/shisan/Documents/self/you-app/node_modules/graceful-fs/graceful-fs.js:99:16
    at /Users/shisan/Documents/self/you-app/node_modules/graceful-fs/graceful-fs.js:43:10
    at FSReqWrap.oncomplete (fs.js:153:20)
From previous event:
    at Packager._build (/Users/shisan/Documents/self/you-app/node_modules/app-builder-lib/src/packager.ts:285:133)
    at /Users/shisan/Documents/self/you-app/node_modules/app-builder-lib/src/packager.ts:281:23
    at Generator.next (<anonymous>)
From previous event:
    at Packager.build (/Users/shisan/Documents/self/you-app/node_modules/app-builder-lib/src/packager.ts:238:14)
    at build (/Users/shisan/Documents/self/you-app/node_modules/app-builder-lib/src/index.ts:58:28)
    at build (/Users/shisan/Documents/self/you-app/node_modules/electron-builder/src/builder.ts:227:10)
    at then (/Users/shisan/Documents/self/you-app/node_modules/electron-builder/src/cli/cli.ts:42:48)
    at runCallback (timers.js:763:18)
    at tryOnImmediate (timers.js:734:5)
    at processImmediate (timers.js:716:5)
From previous event:
    at Object.args [as handler] (/Users/shisan/Documents/self/you-app/node_modules/electron-builder/src/cli/cli.ts:42:48)
    at Object.runCommand (/Users/shisan/Documents/self/you-app/node_modules/yargs/lib/command.js:237:44)
    at Object.parseArgs [as _parseArgs] (/Users/shisan/Documents/self/you-app/node_modules/yargs/yargs.js:1085:24)
    at Object.get [as argv] (/Users/shisan/Documents/self/you-app/node_modules/yargs/yargs.js:1000:21)
    at Object.<anonymous> (/Users/shisan/Documents/self/you-app/node_modules/electron-builder/src/cli/cli.ts:25:28)
    at Module._compile (internal/modules/cjs/loader.js:654:30)
    at Object.Module._extensions..js (internal/modules/cjs/loader.js:665:10)
    at Module.load (internal/modules/cjs/loader.js:566:32)
    at tryModuleLoad (internal/modules/cjs/loader.js:506:12)
    at Function.Module._load (internal/modules/cjs/loader.js:498:3)
    at Function.Module.runMain (internal/modules/cjs/loader.js:695:10)
    at startup (internal/bootstrap/node.js:201:19)
    at bootstrapNodeJSCore (internal/bootstrap/node.js:516:3)
npm ERR! code ELIFECYCLE
npm ERR! errno 1
npm ERR! you-app@1.0.0 dist: `electron-builder --mac --x64`
npm ERR! Exit status 1
npm ERR!
npm ERR! Failed at the you-app@1.0.0 dist script.
npm ERR! This is probably not a problem with npm. There is likely additional logging output above.

npm ERR! A complete log of this run can be found in:
npm ERR!     /Users/shisan/.npm/_logs/2018-08-22T06_28_55_102Z-debug.log
✖  Error building interactive interface
```

问题是在下载.zip包的时候，中断了操作，以后所有执行打包的时候，找不到那个文件（或者是残缺的文件）就报错了，需要手动清除下缓存 缓存路径在`~/Library/Caches/electron/`

#### 常用参数

`electron-builder`配置文件写在`package.json`中的`build`字段中

```json
"build": {
    "appId": "com.example.app", // 应用程序id 
    "productName": "测试", // 应用名称 
    "directories": {
        "buildResources": "build", // 构建资源路径默认为build
        "output": "dist" // 输出目录 默认为dist
    },
    "mac": {
        "category": "public.app-category.developer-tools", // 应用程序类别
        "target": ["dmg", "zip"],  // 目标包类型 
        "icon": "build/icon.icns" // 图标的路径
    },
    "dmg": {
        "background": "build/background.tiff or build/background.png", // 背景图像的路径
        "title": "标题",
        "icon": "build/icon.icns" // 图标路径
    },
    "win": {
        "target": ["nsis","zip"] // 目标包类型 
    }
}
```

[应用程序类别](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/LaunchServicesKeys.html#//apple_ref/doc/uid/TP40009250-SW8)

[更多参数](https://www.electron.build/)


**使用electron-package或者electron-builder进行打包，就不需要asar手动打包了，这两个工具已经集成进去了，以下内容只做了解即可**

### asar

#### 简介
[asar](https://electronjs.org/docs/tutorial/application-packaging#generating-asar-archives)是一种将多个文件合并成一个文件的类 tar 风格的归档格式。 Electron 可以无需解压整个文件，即可从其中读取任意文件内容。

#### 使用

上面我们看到使用`electron-package`进行打包的时候，可以添加`--asar`参数， 它的作用就是将我们的源码打包成asar格式， 不使用的话就是我们的源码。

依旧使用`electron-quick-start`模版， 使用`electron-package`进行打包

打包后目录中选择应用，右键`显示包内容`

![](https://ws4.sinaimg.cn/large/0069RVTdly1fv820a969oj30j907a3yi.jpg)

依次打开路径 `Contents -> Resources -> app`

就可以看到我们的原文件了

![](https://ws4.sinaimg.cn/large/0069RVTdly1fv820foundj30nv09dwej.jpg)

使用`electron-package . --asar`进行打包，再次查看app中的源文件已经打包成`asar`文件了

![](https://ws2.sinaimg.cn/large/0069RVTdly1fv820o0xnpj30lt091jro.jpg)

#### 常用命令

+ 打包

```bash
asar pack|p <dir> <ouput>
```
如 `asar ./ app.asar`

+ 解压

```bash
asar extract|e <archive> <output>
```
如 `asar e app.asar ./`


事实上，就算使用了asar打包方式，我们的源代码依旧是暴露的，使用asar解压仍然能轻易解密，使用webpack将代码压缩混淆才是王道。



## 自动更新

我们使用[electron-builder](https://github.com/electron-userland/electron-builder)和[electron-updater](https://www.npmjs.com/package/electron-updater)来完成应用的自动更新， *并不是electron里面的`autoUpdater`*

### 流程

![](https://ws3.sinaimg.cn/large/0069RVTdly1fv820toua7j30vq16xgq0.jpg)



### 创建证书

无论使用哪种方式进行自动更新，代码签名是必不可少的，我们使用创建本地的证书来进行测试自动更新

在其他中找到钥匙串访问

![](https://ws2.sinaimg.cn/large/0069RVTdly1fv820y0777j31kw0tbn0s.jpg)

依次选择 `钥匙串访问` -> `证书助理` -> `创建证书`，创建一个新的证书

![](https://ws4.sinaimg.cn/large/0069RVTdly1fv8213wneqj30s60ekq49.jpg)

起个响亮的名字，证书类型选择`代码签名`，然后点击创建

![](https://ws3.sinaimg.cn/large/0069RVTdly1fv8219snb3j30yg0o6q42.jpg)

创建完成后在钥匙串中找到它

![](https://ws3.sinaimg.cn/large/0069RVTdly1fv821fwihpj319o0lomyv.jpg)

右键，选择更改信任设置，将信任设置成`始终信任`

![](https://ws2.sinaimg.cn/large/0069RVTdly1fv821lnzyoj30sk0ku0tw.jpg)


### 代码签名

+ 没有代码签名
![](https://ws4.sinaimg.cn/large/0069RVTdly1fv821qew5ej30nn0ef0ui.jpg)

+ 代码签名
![](https://ws4.sinaimg.cn/large/0069RVTdly1fv821upxv8j30nh06gjrz.jpg)


#### 设置环境变量

执行`sudo vi ~/.bash_profile`, 添加变量

```bash
export CSC_NAME="electron_update"
```
执行`source ~/.bash_profile` 重载变量文件

执行`echo $CSC_NAME` 查看变量有没有生效，没有生效的话，执行上面命令重载文件

**多次重载，依旧获取不到变量，直接退出终端，再次登录终端即可**

#### 打包代码

使用`electron-builder`进行打包，项目使用[electron-quick-start](https://github.com/electron/electron-quick-start)

我们先添加打包的`build`配置， 在`package.json`中添加

```json
"build": {
    "appId": "org.electron.com",
    "publish": [
      {
        "provider": "generic",
        "url": "http://172.28.86.36:8080/" 
      }
    ],
    "productName": "我的",
    "directories": {
      "output": "dist"
    },
    "mac": {
      "target": [
        "dmg",
        "zip"
      ]
    },
    "win": {
      "target": [
        "nsis",
        "zip"
      ]
    },
    "dmg": {
      "backgroundColor": "red",
      "title": "made",
      "contents": [
        {
          "x": 400,
          "y": 128,
          "type": "link",
          "path": "/Applications"
        }
      ]
    }
}
```

前面，我们说过了`build`字段中各个参数的含义，这里就不赘述了，这里添加了`publish`字段，它是配置我们自动更新的信息，url是服务器地址。

**服务器我们可以使用[http-server](https://www.npmjs.com/package/http-server)模块, 快速搭建一个静态服务器**

执行打包后，输出

![](https://ws1.sinaimg.cn/large/0069RVTdly1fv8222jbwkj30m5093aa5.jpg)

主要看下`latest-mac.yml`文件 

![](https://ws3.sinaimg.cn/large/0069RVTdly1fv8226oxv3j31g40e4myu.jpg)

可以看到`version`字段，用来表明当前应用的版本，那这个字段是从哪来的呢？

没错就是我们的`package.json`文件中`version`字段



#### 自动更新 

在主进程中写入以下代码
```js
const {app, BrowserWindow, ipcMain} = require('electron')

const { autoUpdater } = require('electron-updater')

// 本地服务器地址
const feedURL = `http://172.28.82.40:8080/`

let mainWindow

function createWindow () { // 创建窗口
  mainWindow = new BrowserWindow({width: 800, height: 600})
  mainWindow.loadFile('index.html')

  mainWindow.on('closed', function () {
    mainWindow = null
  })
}

app.on('ready', createWindow)

app.on('window-all-closed', function () {
  if (process.platform !== 'darwin') {
    app.quit()
  }
})

app.on('activate', function () {
  if (mainWindow === null) {
    createWindow()
  }
})

// 监听自定义update事件
ipcMain.on('update', (e, arg) => {
  checkForUpdate()
})

const checkForUpdate = () => {
  // 设置检查更新的 url，并且初始化自动更新
  autoUpdater.setFeedURL(feedURL)

 // 监听错误 
  autoUpdater.on('error', message => {
    sendUpdateMessage('err', message)
  })
 // 当开始检查更新的时候触发
  autoUpdater.on('checking-for-update', message => {
    sendUpdateMessage('checking-for-update', message);
  })
 // 
  autoUpdater.on('download-progress', function(progressObj) {
    sendUpdateMessage('downloadProgress', progressObj);
  });
  // 更新下载完成事件
  autoUpdater.on('update-downloaded', function(event, releaseNotes, releaseName, releaseDate, updateUrl, quitAndUpdate) {
      
      ipcMain.on('updateNow', (e, arg) => {
          autoUpdater.quitAndInstall();
      });
      sendUpdateMessage('isUpdateNow');
  });
 // 向服务端查询现在是否有可用的更新
  autoUpdater.checkForUpdates();
}

// 发送消息触发message事件
function sendUpdateMessage(message, data) {
  mainWindow.webContents.send('message', { message, data });
}
```

然后更改我们的渲染进程的代码

```js
const {ipcRenderer} = require ('electron');
const button = document.querySelector('#button')


const ul = document.querySelector('ul')
button.onclick = () => {
  ipcRenderer.send('update')
}

ipcRenderer.on('message', (event, {message,data }) => {
  let li = document.createElement('li')
  li.innerHTML = message + " <br>data:" + JSON.stringify(data) +"<hr>";
  ul.appendChild(li)
  if (message === 'isUpdateNow') {
    if (confirm('是否现在更新？')) {
        ipcRenderer.send('updateNow');
    }
  }
})
```

页面代码
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Hello World!</title>
  </head>
  <body>
    <h1>Hello World!</h1>
    <h1>当前版本是1.0.3</h1>

    <ul>
      <li>生命周期过程展示</li>
    </ul>

    <button id="button">点击我</button>
    <script>
      // You can also require other files to run in this process
      require('./renderer.js')
    </script>
  </body>
</html>
```
这里使用按钮点击事件触发更新操作。


直接打包，安装这个`1.0.3`的版本。

然后将`package.json`的文件中的`version`字段更改成`1.0.5`，将index.html的更改成`1.0.5`，进行打包。 

随便找个地方创建个文件夹，如`test`

将打包的文件移动到`test`中，执行`http-server ./`，搭建服务

当我们点击按钮的时候，就可以进行更新应用了，如下图

![](https://ws2.sinaimg.cn/large/0069RVTdly1fv822ez1drj31hc0taq5v.jpg)

点击ok后，静候片刻，更新完后就打开了最新版本的应用

![](https://ws3.sinaimg.cn/large/0069RVTdly1fv822ka2x4j30p60g6q38.jpg)

以上就完成了electron的自动更新。

## 项目地址

本文所有代码请戳[gitlab](http://gitlab.mljr.com/FEApply/electron-ocr)了解更多