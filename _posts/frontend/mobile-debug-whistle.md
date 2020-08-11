# 移动端调试
<!-- author:dongliang.yang02@mljr.com -->

### 需求场景
在不借助调试工具的情况下，我们在开发移动端的h5项目时会遇到下面几个问题
一般情况下，我们都是在pc上的浏览器开发并调试移动端的h5项目，在这个过程中可能会遇到如下情景：
 1. **h5中的有些模块需要调用APP本地的api,如拍照，定位等**
 2. **部分移动设备对css的渲染存在差别，需要在真机上看效果**
 3. **无法像pc浏览器的开发者工具那样，查看 修改DOM结构等操作(vConsole能实现部分功能)**
 4. **对于一些触摸滑动等手势操作也存在兼容性问题**
 5. **带有用户系统的多角色模块，在pc浏览器上调试的时候需要进行多次抓包来模拟不同角色（如车商需求中的拜访管理模块，分为高管 大区总监 区域经理 城市经理 主管 销售等角色**）
### 调试工具whistle的简单介绍
>whistle(读音`[ˈwɪsəl]`，拼音`[wēisǒu]`)是基于Node实现的跨平台抓包调试代理工具，和Fiddler、Charles比较类似，有以下基本功能：

 1. 查看HTTP、HTTPS请求响应内容
 2. 查看WebSocket、Socket收发的帧数据
 3. 设置请求hosts、上游http/socks代理
 4. 修改请求url、方法、头部、内容
 5. 修改响应状态码、头部、内容，并支持本地替换
 6. 修改WebSocket或Socket收发的帧数据
 7. 内置调试移动端页面的weinre和log
 8. 作为HTTP代理或反向代理
 9. 支持用Node编写插件扩展功能
 ![whistle.png-423.6kB][1]
### whistle的安装及相关命令
```
npm install whistle -g
w2 -h // 查看命令相关的帮助信息
w2 start // 启动 可加端口号
w2 stop // 停止
w2 restart // 重启 可加端口号
```
### 准备工作
在用whistle抓包或移动端调试之前需要安装一个Chrome的插件SwitchyOmega，然后进行代理设置如下图
![switchyomega.png-49kB][2]

其中代理服务器填写127.0.0.1即可，端口号为whistle服务的端口号，
通过whistle start启动whistle服务，默认端口号为8899，在浏览器里打开127.0.0.1:8899，其界面如下图

![whistle-interface.png-105.9kB][3]
切换到network选项，可以看到抓取的请求列表，点击其中一个请求，在右侧一栏中的inspectors选项卡下可以看到该请求 响应的详细信息。
同一局域网下，打卡手机的代理设置，代理服务器为本地的ip地址，端口号为whistle的端口号
### 真机调试
对于单页面应用的前端项目，我们在whistle界面的rules选项卡里配置代理请求及目标请求即可，目标请求也就是我们本地启动的前端服务，如在Simba的h5项目中，测试服务器配置的入口为`http://192.168.49.147/carcrm-h5/`，本地服务的入口为`http://192.168.31.123:8082`那么在rules中的设置应为`http://192.168.49.147/carcrm-h5/ http://192.168.31.123:8082`,最后把webpack中静态资源的公共访问路径修改`http://127.0.0.1:8082/`(这里只是一个示例，端口号及相关ip要根据实际情况进行调整)，注意：在请求中#及其后面的字符串不会发送到服务端，对于单页面的调试，大致的转发流程如下

![Snipaste_2018-11-30_10-50-19.png-26.4kB][4]

通过whistle中的network选项卡也能清晰的看出转发成功后静态资源的请求情况


### 远程调试
远程调试中主要用到whistle中集成的weinre模块（他就像移动端浏览器的开发者工具），同样需要在whistle的rules进行相关的设置，例如远程调试线上的Simba-h5项目，应设置为`simba-h5.mljr.com/ weinre://test`,然后在whistle服务界面中weinre选项卡，此时，在移动端APP里触发相关项目中的一个模块，即可在weinre界面里看到相关的elements network等内容了，如下图
![remote-debug.png-254kB][5]

请求情况如下图

![request-001.png-354.2kB][6]



### Charles的remote map也可以。。。
### 相关资料链接
https://github.com/avwo/whistle
https://wproxy.org/
https://www.switchyomega.com/
https://segmentfault.com/a/1190000016058875
http://imweb.io/topic/5981a34bf8b6c96352a59401
https://juejin.im/post/5ad5f471518825557e78e623
http://people.apache.org/~pmuellr/weinre/docs/latest/


  [1]: http://static.zybuluo.com/xiaojiang/djhpc4ox9tbxpqca4ijw8qbo/whistle.png
  [2]: http://static.zybuluo.com/xiaojiang/g96ovtkn6c78mdsfenjpu58t/switchyomega.png
  [3]: http://static.zybuluo.com/xiaojiang/fzg48xlqnxzc3ixctuigujxj/whistle-interface.png
  [4]: http://static.zybuluo.com/xiaojiang/5udhgu4nqsgaxbctivacpoy1/Snipaste_2018-11-30_10-50-19.png
  [5]: http://static.zybuluo.com/xiaojiang/grdvfogxfujv1o39gnswp4ni/remote-debug.png
  [6]: http://static.zybuluo.com/xiaojiang/i5ric3h0acvfjfmk80ugeqm3/request-001.png