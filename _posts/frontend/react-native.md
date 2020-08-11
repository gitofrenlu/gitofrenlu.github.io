# react-native
<!-- author:jiechao.ding01@mljr.com -->

## 前言

1、`React Native`使你只使用`JavaScript`也能编写原生移动应用。 它在设计原理上和`React`一致，通过声明式的组件机制来搭建丰富多彩的用户界面

2、`React Native`产出的并不是“网页应用”， 或者说“HTML5应用”，又或者“混合应用”。 最终产品是一个真正的移动应用，从使用感受上和用Objective-C或Java编写的应用相比几乎是无法区分的。

![](https://ws3.sinaimg.cn/large/006tNbRwly1fwkmwssto6j31fb13jtek.jpg)

3、[部分react-native开发的产品](https://facebook.github.io/react-native/showcase.html)

## 优势
1、垮平台开发：相比原生的ios 和 android app各自维护一套业务逻辑大同小异的代码，React Native 只需要同一套javascript 代码就可以运行于ios 和 android 两个平台，在开发、测试和维护的成本上要低很多。

![](https://ws2.sinaimg.cn/large/006tNbRwly1fwlgaccctfj30v40jswft.jpg)

2、快速编译：相比Xcode中原生代码需要较长时间的编译，React Native 采用热加载的即时编译方式，使得App UI的开发体验得到改善，几乎做到了和网页开发一样随时更改，随时可见的效果。

3、快速发布：React Native 可以通过 JSBundle 即时更新 App。相比原来冗长的审核和上传过程，发布和测试新功能的效率大幅提高。

4、简单易学。 相比于 iOS 和 Android 的一整套复杂的知识体系，React Native 从本质上来讲就是状态机，对于开发者来讲理解不难，且实际操作可谓入门容易、上手轻松。


## 环境搭建
**安装依赖**

**IOS**

必须安装的依赖有：Node、Watchman 和 React Native 命令行工具以及 Xcode。

虽然你可以使用任何编辑器来开发应用（编写 js 代码），但你仍然必须安装 Xcode 来获得编译 iOS 应用所需的工具和环境。

```
 brew install node
 brew install watchman
```
注： node版本v8.3以上

Yarn、React Native 的命令行工具（react-native-cli）

Yarn是 Facebook 提供的替代 npm 的工具，可以加速 node 模块的下载。

React Native 的命令行工具用于执行创建、初始化、更新项目、运行打包服务（packager）等任务。

```
 npm install -g yarn react-native-cli
```

安装完 yarn 后同理也要设置镜像源：

```
yarn config set registry https://registry.npm.taobao.org --global
yarn config set disturl https://npm.taobao.org/dist --global
```

创建新项目

```
react-native init myapp
```

启动项目

```
react-native run-ios
```


**Android**

**安装依赖**

必须安装的依赖有：Node、Watchman 和 React Native 命令行工具以及 JDK 和 Android Studio

安装 Android Studio

[详情请点击](https://reactnative.cn/docs/getting-started/)

项目初始化过程同IOS

启动项目

```
react-native run-android
```

