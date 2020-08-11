# 百度云+宝塔面板构建静态网站
## 目标
+ 熟悉服务器的工作流程，域名解析
+ 利用云服务+可视化面板搭建简单的静态网站
+ 使用git或SVN等实现上传代码与服务器同步
+ 不借助面板工具在Linux服务上部署代码
+ 一机多站的实现

## 云服务介绍
+ 概念：是百度提供的一种基础云计算服务，它能帮助您快速的构建更加稳定、安全的应用，提高运维效率，降低IT成本，是您更专注于核心业务
+ 云服务的组成

![17JPEG](https://ss1.baidu.com/6ONXsjip0QIZ8tyhnq/it/u=297791977,1096763709&fm=173&app=25&f=JPEG?w=502&h=512&s=81B0CC3001C244EA504DB1DA020080B2)

+ 国内市场主流的云服务器：[阿里云](https://www.aliyun.com/?spm=5176.8097504.fszjobuve.2.2ec76fb5ZFFQMM)（ECS）、[腾讯云](https://cloud.tencent.com/act/seckill?from=11125)（CVM）、[百度云](https://cloud.baidu.com/campaign/Promotion2019mid/index.html?track=cp:npinzhuan%7Cpf:pc%7Cpp:npinzhuan-biaoti%7Cpu:wenzineirong%7Cci:2019nzdc%7Ckw:2128312)（BCC）、金山、华为、京东、网易

+ 优点：稳定、弹性、安全、成本、可拓展性
不用在采购硬件设备，也不用人力去搬了（真心非常沉），可以随时扩容、增加带宽
+  价格对比：阿里500元起 腾讯600元起 百度优惠活动100元起
+  性能对比，腾讯快 阿里稳定 百度SEO可能会好点

## 百度云
[百度云登录地址](https://login.bce.baidu.com/?account=&redirect=http%3A%2F%2Fbeian.bce.baidu.com%2F)
[BCC文档](https://cloud.baidu.com/doc/BCC/s/wjx1qe6am)
+ 登录远程服务器：
```js
 
 ssh root@106.13.169.161
 ```
+  BCC 实例：就是一台服务器
+  绑定秘钥对 否则在远程登录时会报错
```js
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!

Someone could be eavesdropping on you right now (man-in-the-middle attack)!

It is also possible that a host key has just been changed.

The fingerprint for the ECDSA key sent by the remote host is

5b:82:47:82:ef:cf:76:96:63:a6:7c:ce:86:7f:eb:9b.

Please contact your system administrator.

Add correct host key in /root/.ssh/known_hosts to get rid of this message.

Offending ECDSA key in /root/.ssh/known_hosts:2

ECDSA host key for [106.13.169.161]:2222 has changed and you have requested strict checking.

Host key verification failed.
```
解决方案：删除对应ip的在known_hosts相关信息
```js

vi ~/.ssh/known_hosts

```
+  域名解析：A记录是IPV4地址，多域名解析到同一IP AAAA记录是IPV6 CNAME记录是别名解析，一个域名设置多个域名，NS记录是指定DNS服务器解析 MX记录是邮件服务器 TXT记录为主机名设置说明 TTL值是指域名在服务器上的缓存时间

 
## 安装宝塔面板
安装命令
```js 
yum install -y wget && wget -O install.sh http://download.bt.cn/install/install_6.0.sh && sh install.sh
```
+ 查看命令行 bt、 bt default
+ [宝塔Linux命令行查看](https://www.bt.cn/btcode.html)
+ [宝塔登录]( http://106.13.169.161:8888/ca0ac166)

## 项目的部署
+ 一个IP地址对应一个站点？
![](https://www.landui.com/file/upload/20171113/1510562864132335.png)

## 扩展CentOS安装Node与nvm
+ 1.安装wget
```js
yum install -y wget
```
+ 2.下载node.js的bin包
```js
wget https://nodejs.org/dist/v9.3.0/node-v9.3.0-linux-x64.tar.xz
```
+ 3.解压 依次执行
```js
xz -d node-v9.3.0-linux-x64.tar.xz
tar -xf node-v9.3.0-linux-x64.tar
```
+ 4.部署bin文件 依次执行
```js
ln -s ~/node-v9.3.0-linux-x64/bin/node /usr/bin/node
ln -s ~/node-v9.3.0-linux-x64/bin/npm /usr/bin/npm
```
+ 5.测试是否安装成功
```js
node -v
npm
```
+ 安装nvm
```js
下载git
yum install git -y
下载nvm
git clone git://github.com/creationix/nvm.git ~/nvm
这一步很重要 设置nvm 自动运行
echo "source ~/nvm/nvm.sh" >> ~/.bashrc
source ~/.bashrc
```
## 总结
+ 购买云服务器
+ 购买域名
+ 上传代码到服务器
+ 域名解析到指定文件
+ 遗留问题 代码如何同步到服务器（webhook、FTP） 如何书写数据库、接口外部调用