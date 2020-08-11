# npm install模块安装
<!-- author:lingke.wang@mljr.com -->

### npm install 安装过程：

**首次安装：**

读取`package.json`中`dependencies`和`devDependencies`依赖的模块，这些模块为首层依赖模块。项目本身是整颗依赖树的根节点，每个首层依赖模块都是根节点下面的一棵子树，npm会开启多进程从每个首层依赖模块中逐步寻找更深层次的节点。

获取模块是一个递归的过程，主要分为以下几步：

1. 获取模块信息。`package.json`中描述的大多数为向上兼容的版本，如`vue: ^2.1.1`这样。此时会从`npm`仓库查询该模块，并找到符合该版本的最新的版本。

2. 下载模块。找到匹配的模块后，会从该模块信息拿到对应的压缩包地址进行下载(`tarball`字段为下载地址)。此时`npm`会用此地址检查本地缓存，如有，则直接从缓存拿，如没有，则从仓库进行下载。

3. 查找模块依赖。如果有依赖，则回到第一步，没有则停止。

上一步获取的是完整的依赖树，中间可能包含大量重复的模块，比如`模块A`依赖`模块B`，`模块C`也依赖`模块B`。`npm`会遍历所有节点，进行扁平化处理，即所有模块都位于`node_modules`根目录。如果发现重复模块，则直接从依赖树中丢弃。如果是固定版本，`npm`会选择兼容版本。

更新工程中的node_modules，并执行模块中的生命周期函数(按照preinstall、install、postinstall 的顺序)。

当前npm工程如果定义了钩子此时会被执行，（按照 preinstall、install、postinstall 的顺序）。

生成`package-lock.json`文件，`npm install`完成。



**再次安装：**

直接读取`package-lock.json`中的模块信息，对比缓存，如果匹配，则直接从缓存拿，如未匹配到，则直接进行下载，并缓存。



#### npm缓存

模块安装成功以后，一份存于`node_modules`目录，另一份位于缓存目录中。缓存目录，默认为`~/.npm`。

当执行`npm install`的时候，如果存在`package-lock.json`文件，读取文件模块信息及依赖，通过`version`
、`resolved`、`integrity`字段，在缓存中检查是否有匹配，如有，则直接取缓存，如没有，则直接进行下载。


如遇`npm install`报错，尝试删除`package-lock.json`文件，然后删除缓存 `npm cache clean --force`

#### 查询模块信息

`npm`模块仓库有自己的查询服务`registry`，执行`npm config get registry`可以看到当前的`registry`地址。

![](https://ws4.sinaimg.cn/large/006tKfTcly1fsbq9v47gdj308j00zt8i.jpg)

`registry`后面跟上模块名，可以拿到一个`json`对象，为此模块的所有版本信息。

如[https://registry.npm.taobao.org/vue](https://registry.npm.taobao.org/vue)。查询具体版本号的信息，只需要加上版本号即可，如[https://registry.npm.taobao.org/vue/2.5.1](https://registry.npm.taobao.org/vue/2.5.1)。

上面的操作等同于下面的命令：
``` bash
# 查看vue的所有版本信息
npm view vue

# 查看vue2.5.1模块信息
npm view vue@2.5.1
```







...

::: warning npm 版本
请确保你的 npm 版本大于等于 5。
:::
