# 前端监控平台sentry
<!-- author:ying.lin@mljr.com -->

## 什么是sentry

[sentry官网](https://sentry.io/welcome/)

以下是官网首页关于Sentry的介绍
> Stop hoping your users will report errors
停止期待您的用户反馈错误
Open-source error tracking that helps developers monitor and fix crashes in real time. Iterate continuously. Boost efficiency. Improve user experience
帮助开发人员实时监控和修复错误，快速迭代，提高效率。提升用户体验。

它分为客户端和服务端，客户端(目前客户端有Python, PHP,C#, Ruby等多种语言)就嵌入在你的应用程序中间，程序出现异常就向服务端发送消息，服务端呢,是消息记录到数据库中并提供一个web页方便查看。Sentry由python编写，源码开放，性能卓越，易于扩展

* sentry 有官网,但是要收费,只能试用14天
* 但是可以自己搭建一套sentry服务, 有两种方式一种是用docker搭建,一种是python搭建[我自己用docker 搭建了一个在我私人服务器上](http://140.143.94.153:9000/auth/login/sentry/)
* 使用人的比较多,网上能搜到不少资料,我身边的朋友在公司也在用sentry
* 使用方便,适合我们公司的项目,不管是vue,还是react,还是fepack搭建的项目都能配置上

![](https://ws3.sinaimg.cn/large/005BYqpggy1g2u1cwu2xbj31aw0n4ae8.jpg)


## 怎么使用它 - 连蒙带猜 

### 先创建项目

[react例子](https://codesandbox.io/s/qvlqy352zj)
[vue例子](https://codesandbox.io/s/pmpr2lj1vj)
[koa教程 + 小程序教程](https://andyliwr.github.io/2018/10/28/mbook_use_sentry/)
[sentry官方文档](https://docs.sentry.io/)

前端项目直接创建
```js
vue create --preset gitlab.mljr.com:FEApply/vue-cli3-preset --clone project-name
```
官网: 点击设置>项目>[创建](https://sentry.io/organizations/mljr/projects/)

按照官方指南走(网上有些资料已经落后,看官网最靠谱)

下载 @sentry/integrations
```javascript
npm install @sentry/integrations
# or
yarn add @sentry/integrations
```

在main.js 里添加相对应代码
```javascript
import Vue from 'vue'
import * as Sentry from '@sentry/browser';
import * as Integrations from '@sentry/integrations';

Sentry.init({
  dsn: 'https://0e0e8d7c96e547c9b406d12c08f93eb2@sentry.io/1455012',
  //生成项目对应的dsn
  integrations: [
    new Integrations.Vue({
      Vue,
      attachProps: true,
    }),
  ],
});
```
* dsn 的组成
```js
'{PROTOCOL}://{PUBLIC_KEY}:{SECRET_KEY}@{HOST}/{PATH}{PROJECT_ID}'
```
它由5部分组成：
1. 使用的协议: http或https;
2. 验证sdk的公钥(PUBLIC_KEY)和密钥(SECRET_KEY);
3. 目标sentry服务器;
4. 验证用户绑定的项目id(PROJECT_ID)
ps: 网上说dsn私人搭建也会自动生成,但是我搭建的没有生成,得自己去拼
点击项目配置>>客户端密钥 > 点击配置 找到公钥密钥和项目id 

接下来写一个bug试试~演示下手机模式, (安卓,ios),pc模式

会在官网上显示,并且及时发邮件给我,如果配置了钉钉,还能[钉钉提醒](https://www.cnblogs.com/cjsblog/archive/2019/03/25/10585213.html)

![](https://ws3.sinaimg.cn/large/005BYqpggy1g2u1vkorm4j31jv0u0kex.jpg)

[这里详细介绍如何在页面里安监控代码](https://docs.sentry.io/platforms/javascript/?platform=browser)
### 界面介绍
![](https://ws3.sinaimg.cn/large/005BYqpggy1g2u2g9wp9uj31az0b8ac5.jpg)

#### 解决:   
标记解决后可以下版本进行追踪,这个跟gitlab进行[版本配置](https://docs.sentry.io/workflow/releases/?_ga=2.195241899.608360996.1557125588-1909320914.1557125588%3F_ga&platform=browser)才能使用
#### 忽略:   
忽略配置,可以标记次数和影响人数再次促发报警
#### 删除:   
删除后再次有类似报警会直接忽略
#### 用户反馈:   
当触发bug的时候会弹出一个框,让用户填写当前bug内容
![](https://ws3.sinaimg.cn/large/005BYqpggy1g2u2p5yitrj30l60gsmyl.jpg)
[可以根据文档来弹性触发弹窗](https://docs.sentry.io/enriching-error-data/user-feedback/?_ga=2.161712827.608360996.1557125588-1909320914.1557125588&platform=browser) 
```javascript
Sentry.init({
    dsn: "https://0e0e8d7c96e547c9b406d12c08f93eb2@sentry.io/1455012",
    integrations: [
      new Integrations.Vue({
        Vue,
        attachProps: true
      })
    ],
    beforeSend(event, hint) {
      // 检查它是否是异常，如果是，则显示报告对话框
      if (event.exception) {
        Sentry.showReportDialog({ eventId: event.event_id });
      }
      return event;
    }
  });
```
#### 标签:  
个人理解就是bug相关的资料,并且还能在项目代码里[自定义标签](https://docs.sentry.io/enriching-error-data/context/?_ga=2.225797277.608360996.1557125588-1909320914.1557125588%3F_ga&platform=browser),方便后期在discover,dashboards上统计查询排查
![](https://docs.sentry.io/assets/visibility/query-result-087f8e1405f0960031886f4f6bc338df313d9a637e7d7968c04a52eea43b4957.png)
![](https://docs.sentry.io/assets/visibility/dashboard-80fedef0b04491a276a93557975e34fe14ae24e1f4c875daa3fdb4723c8d24e5.png)
#### 事件:  
详细标记bug出现几次,每次是什么浏览器,设备是什么.
#### Merged:  
跟其他bug进行合并,标记是同一个bug好方便后期处理和跟踪


#### sentry-cli:  
用sentry-cli 管理使用sentry-cli 去管理[Release和SourceMap](https://www.javascriptcn.com/read-31777.html)
* 在配置Release和SourceMap 要先使用sentry进行登录
```js
npm install sentry-cli -g

sentry-cli login  #关联官网
sentry-cli --url http://127.0.0.1:9000 login  #关联私人搭建服务器

```
* 默认组织与项目
-o xxx -p xxx 很麻烦，可以通过修改 ~/.sentryclirc 添加默认组织与项目。

```js
[auth]
token=YOUR API TOKEN
[defaults]
url=服务器
org=组织
project=项目
```
[sentry-cli使用教程](https://docs.sentry.io/cli/)
生产环境是会压缩代码,这样捕捉的异常堆栈也是压缩代码上的,让人无法理解,所以需要根据SourceMap和map来进行对应找出相对的异常堆栈
![](https://img.javascriptcn.com/16fc471992e0737dc72fcf73fbdd5244?imageView2/2/w/800)

## 服务器安装和本地安装
Sentry搭建有两种方式：
* 通过Python安装 [教程地址](https://docs.sentry.io/server/installation/python/)
* 通过Docker容器安装 [教程地址](https://docs.sentry.io/server/installation/docker/)

[参考资料](https://www.chenng.cn/post/sentry-in-practice.html)
###  方法
* 注意事项:
服务器的内存至少 2G，否则在执行 sentry upgrade 命令时会出现问题。

* docker
```javascript
yum install docker -y

// 查看版本信息
docker info
或者
docker -v
```
* 安装pip(pip用于安装docker-compose)
```javascript
# 如果使用 wget下载https开头的网址域名 时报错，你需要加上 --no-check-certificate (不检查证书)选项
wget https://bootstrap.pypa.io/get-pip.py --no-check-certificate  # 下载文件

python get-pip.py #执行安装

pip -V #查看pip版本
```

* 安装docker-compose
```javascript
sudo pip install docker-compose # 安装 docker-compose

docker-compose -v #查看docker-compose 版本
```

* 安装sentry [github地址](https://github.com/getsentry/onpremise)

```javascript
git clone https://github.com/getsentry/onpremise.git

cd onpremise
#cat README.md 里有详细介绍 还有各个配置文件也是有详细介绍方法

# 新建本地数据库和 Sentry 配置的目录
docker volume create --name=sentry-data && docker volume create --name=sentry-postgres

# 创建环境变量配置文件
cp -n .env.example .env

# 构建 Docker Services
docker-compose build

# 生成秘钥
docker-compose run --rm web config generate-secret-key

# 复制秘钥(即最后一行)，编辑 .env 文件，将 SENTRY_SECRET_KEY 对应的 value 修改为刚才复制下来的秘钥
vim .env

# 创建数据库，生成管理员账号(中途会让你设置邮箱 密码,方便后期登录)
docker-compose run --rm web upgrade

# 启动 Sentry 服务
docker-compose up -d

# 查看容器
docker-compose ps
```
打开http://ip:9000 会进去一个登录页,进去登录页后输入`docker-compose run --rm web upgrade`所设置的邮箱和密码,便可以进去了.

* 遇到问题

1. 当执行`docker-compose run --rm web upgrade`的时候报错,目前我在服务器上搭建也有报这个问题,但是不影响使用
![](https://ws3.sinaimg.cn/large/005BYqpggy1g2u8ghyvwrj30ki088jsg.jpg)

2. 在服务器上搭建的时候报错,输入`docker-compose logs web`查看问题
![](https://user-images.githubusercontent.com/883964/41982126-adf9e00a-7a3b-11e8-9927-226d22ecf5b4.PNG)
解决办法:
[参考链接](https://github.com/getsentry/sentry/issues/8862#issuecomment-400807159)
```javascript
docker-compose run --rm web shell

from sentry.models import Project
from sentry.receivers.core import create_default_projects
create_default_projects([Project])
```
3. 搭建后的网站一定要修改时间,把国际时间改成中国时间,不然issues时间统计不对
4. 登录别用qq邮箱登录,官网对qq邮箱认为不规范.
5. 搭建后是需要配置邮箱才能发报警邮箱,[参考链接](https://learnku.com/articles/9405/the-deployment-of-sentry-into-the-production-environment)
6. `docker-compose run --rm web upgrade`在服务器搭建没有让你创建用户 [参考链接](http://ken.weiaai.com/644.html) [参考链接2](https://github.com/getsentry/onpremise/issues/71)
