# vue-cli3-preset
<!-- author:lingke.wang@mljr.com -->

## 使用方法

需先安装[vue-cli3](https://cli.vuejs.org/zh/guide/installation.html)

``` bash
# 安装vue-cli3
npm install -g @vue/cli
# OR
yarn global add @vue/cli

# 创建项目，project-name为项目名称
vue create --preset gitlab.mljr.com:FEApply/vue-cli3-preset --clone project-name
```

执行完`vue create`之后，根据提示选择网站类型：

![](https://ws1.sinaimg.cn/large/006tNbRwly1fy5atovx7vj308402rq2s.jpg)


选择之后即开始构建，构建完成之后，按照提示即可运行项目。

项目内已有`.gitlab.yml`文件，直接进服务器配置即可。

::: tip
项目构建完成以后，默认`releaseGit`地址为：

`git@gitlab.mljr.com:FEWeb-release-group/project-name.git`

如需更新请修改`.gitlab-ci.yml`和`deploy.sh`文件中的`releaseGit`地址
:::


## 部署至测试环境

**优先使用[gitlab ci](/docs/share/gitlab_ci.html)**

如需频繁部署至测试环境，执行`npm run qa`即可，脚本如下：

``` json
"qa": "npm run test && scp -r ./dist/* root@192.168.49.104:/opt/soft/tengine/html/mljr"
```

默认部署至`104`机器，如需部署至其它服务器，修改脚本中的服务器地址即可。


## 上线

**上线须谨慎，请务必使用[gitlab ci](/docs/share/gitlab_ci.html)**

**上线须谨慎，请务必使用[gitlab ci](/docs/share/gitlab_ci.html)**

**上线须谨慎，请务必使用[gitlab ci](/docs/share/gitlab_ci.html)**

使用`gitlab ci`上线能查看`diff`及保留上线记录。

如遇`gitlab ci`无法使用，可执行`npm run www`进行上线。



## 项目目录

本地开发、部署测试环境及上线相关的环境配置已全部配置好，如有需求可自行修改，构建生成的项目目录如下：

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

## 升级至vue-cli4

目前`vue-cli4`已发布，`vue-cli3`生成的项目，可以进行升级。

```bash
# 安装最新vue-cli
npm install -g @vue/cli
# OR
yarn global add @vue/cli
```

在需升级的项目中执行：

```bash
vue upgrade
```

效果如下：

![](https://tva1.sinaimg.cn/large/006y8mN6ly1g86sq4vkv0j30ll03cdfr.jpg)

详细升级指南及更新参考[https://cli.vuejs.org/migrating-from-v3/](https://cli.vuejs.org/migrating-from-v3/)