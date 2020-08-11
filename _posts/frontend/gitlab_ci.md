# gitlab ci搭建持续集成环境
<!-- author:lingke.wang@mljr.com -->

## 配置`gitlab-runner`
gitlab ci 中，runner 是一个隔离的虚拟机器，用来配合 gitlab ci 进行构建。

104机器上已安装`runner`，目前所有编译均在`104`上完成，编译完成后将代码同步至对应的测试环境。

整体流程如下：

![](https://ws2.sinaimg.cn/large/006tKfTcly1fs0gp6zx14j30zu0gk0t5.jpg)

### 以[fe-docs](http://gitlab.mljr.com/FELab/fe-docs)项目为例：

#### 获取项目的`runner配置`

![](https://ws1.sinaimg.cn/large/006tKfTcly1fryyal0phxj310g0atgm0.jpg)

- 点击右上角`Settings -> Runners`，进入`Runners`配置页面：

![](https://ws2.sinaimg.cn/large/006tKfTcly1fryydseaw6j30hc06fglq.jpg)

- 可以看到`runner`需要的配置：1. `ci`地址 2. 该项目的`token`

#### 服务端注册`runner`

- 登入`104`机器，执行`gitlab-runner register`进行注册

![](https://ws3.sinaimg.cn/large/006tKfTcly1frz406tdvzj30pp08474d.jpg)

1. 输入刚才`Runners`配置页面中的`ci`地址
2. `token`地址
3. 当前`runner`的描述，统一使用项目名，此处即为`fe-docs`
4. 当前`runner`的`tag`，统一使用项目名，此处即为`fe-docs`
5. 是否在无`tag`的`build`中运行，`true`即可
6. 执行器选择，此处输入`shell`即可

- 配置完成，执行`gitlab-runner list`，即可在列表中看到刚注册的`runner`:

![](https://ws2.sinaimg.cn/large/006tKfTcly1frz0fyxj4nj30q101hmx0.jpg)


#### 关联`runner`

- 默认注册完成以后会与当前项目进行关联

- 由于单个`runner`一次只能运行一个任务，但可以同时运行多个`runner`，因此需配置此`runner`仅供当前项目使用。

- 进行当前项目的`Runners`配置页面

![](https://ws3.sinaimg.cn/large/006tKfTcly1frz0i4tkejj30i10avjsx.jpg)

如上图所示，点击`token`后的`编辑`，进行如下配置，然后保存即可。

![](https://ws3.sinaimg.cn/large/006tKfTcly1frz0pkyga6j30lg0bvglr.jpg)



## 配置.gitlab-ci.yml

### vue-cli项目配置
在当前项目的根目录创建`.gitlab-ci.yml`文件，进行配置，详细配置请参考[.gitlab-ci配置详解](https://segmentfault.com/a/1190000011890710)

**静态资源通用配置如下：**

``` yml
stages:
    - dev
    - test
    - master

# 定义变量
variables:
    # 测试服务上的静态资源路径
    STATIC_PATH: /opt/soft/tengine/html/mljr/
    # 上线时所拉取的git地址
    # 目前上线的代码放在俩个group中
    # web-release-group：http://gitlab.mljr.com/web-release-group/
    # FEWeb-release-group：http://gitlab.mljr.com/FEWeb-release-group
    # 请确认项目位于哪个group中
    # 默认web-release-group：git@gitlab.mljr.com:FEWeb-release-group/ + 项目名称.git
    # 如不是，请手动配置
    # web-release-group
    RELEASE_GIT: git@gitlab.mljr.com:web-release-group/${CI_PROJECT_NAME}.git
    # FEWeb-release-group
    # RELEASE_GIT: git@gitlab.mljr.com:FEWeb-release-group/${CI_PROJECT_NAME}.git
    # 同步至104
    SYNC_104: rsync -auvzP --delete ./dist/$CI_PROJECT_NAME $STATIC_PATH
    # 同步至133
    SYNC_133: rsync -auvzP --delete ./dist/$CI_PROJECT_NAME root@192.168.49.133:$STATIC_PATH
    # 同步至147
    SYNC_147: rsync -auvzP --delete ./dist/$CI_PROJECT_NAME root@192.168.49.147:$STATIC_PATH
# 缓存
cache:
    untracked: true
    paths:
        - node_modules/


# 运行脚本前的操作
before_script:
    - whoami
    - pwd
    # 获取当前fetch的文件列表，并检查是否有^package 相关文件
    - PACKAGE=$(git diff --name-only HEAD@{0} HEAD@{1} | grep '^\<package' || echo '')
    # 如果没有node_modules目录，则无缓存，一般为当前分支首次编译，需先执行 npm install
    # 如果存在node_modules目录，且package相关文件有更新，则需先执行 npm install
    - test ! -d node_modules || test -n "$PACKAGE" && npm install


# 手动部署
dev:
    stage: dev
    script:
        - echo 'dev'
        - node build/build.js qa
        # 同步至104
        - $SYNC_104
        # 同步至133
        # - $SYNC_133
        # 同步至147
        # - $SYNC_147
    except:
        - test
        - master
    # 需要手动执行
    when: manual


# 自动部署
test:
    stage: test
    script:
        - echo 'test'
        - node build/build.js qa
        # 同步至104
        - $SYNC_104
        # 同步至133
        # - $SYNC_133
        # 同步至147
        # - $SYNC_147
    only:
        - test


# 上线
master:
    stage: master
    script:
        - echo 'master'
        # 编译
        - node build/build.js prod
        - cd ..
        # 发布的目录为当前目录名+ -www 后缀
        # 判断-www目录是否存在，如已存在，则先进行删除操作
        - test -d ${CI_PROJECT_NAME}-www && rm -rf ${CI_PROJECT_NAME}-www
        # 克隆
        - git clone $RELEASE_GIT ${CI_PROJECT_NAME}-www
        # 清空文件夹，.git信息会保留
        - test -d ${CI_PROJECT_NAME}-www && rm -rf ${CI_PROJECT_NAME}-www/*
        # 进行复制操作
        - cp -R ${CI_PROJECT_NAME}/dist__www/${CI_PROJECT_NAME}/* ${CI_PROJECT_NAME}-www/
        # 在当前目录设置git提交的用户名
        - cd ${CI_PROJECT_NAME}-www
        - git config user.name ${GITLAB_USER_EMAIL}
        - git config user.email ${GITLAB_USER_EMAIL}
        # 提交
        - git add . && git commit -m 'Auto-commit' && git push
        # tag
        - git tag "www/${CI_PROJECT_NAME}/`date +\"%Y-%m-%d\"`.`date +\"%H%M\"`" && git push --tags
    only:
        - master
    # 需要手动执行
    when: manual
    # environment:
        # name: production

```

目前已配置`192.168.49.104`，`192.168.49.133`，`192.168.49.147`三台测试服务器的同步，如需同步至其它机器，请自行添加。


::: tip
`104机器`默认使用[淘宝npm仓库](https://registry.npm.taobao.org/)。
:::

### fepack项目配置
`fepack`项目的`gitlab-runner`配置在`192.168.49.139`上。

```yml
stages:
    - master

# 定义变量
variables:
    # 测试服务上的静态资源路径
    STATIC_PATH: /opt/soft/tengine/html/mljr/
    # web-release-group
    RELEASE_GIT: git@gitlab.mljr.com:web-release-group/${CI_PROJECT_NAME}.git
    # FEWeb-release-group
    # RELEASE_GIT: git@gitlab.mljr.com:FEWeb-release-group/${CI_PROJECT_NAME}.git

# 上线
master:
    stage: master
    script:
        - node -v
        - fepack -V
        - compass -v
        - tsc -v
        - whoami
        - pwd
        # 存储用户信息
        - MAIL=${GITLAB_USER_EMAIL}
        # 去除master验证
        - sed -i "/if getGitBranch()/,/return$/s/.*//g" run.py
        # 设置git信息
        - sed -i "s/exeCmd('git add .')/\n    exeCmd('git config user.name \$MAIL')\n    exeCmd('git config user.email \$MAIL')\n    exeCmd('git add .')/g" run.py
        - python run.py www
    only:
        - master
    # 需要手动执行
    when: manual
    # environment:
        # name: production
```
工作原理：去掉`run.py`中的`master`验证，并设置`git`用户名，然后在服务端执行`python run.py www`，服务端相关依赖版本固定，能确保每次编译环境都一样。

服务器上对应的`node`及`fepack`版本，请参考服务器上配置。


## 清除缓存

如出现以下这种`Module not found:`的错误，需清除缓存，再进行`build`。

![](https://ws4.sinaimg.cn/large/006tNbRwly1fw33xpboghj30lg0540sp.jpg)

登陆`104`机器，执行`node remove-cache`，选择需要清除缓存的项目即可。

``` bash
# 密码：mljr123
ssh root@192.168.49.104
```
![](https://ws1.sinaimg.cn/large/006tNbRwly1fw343g7h6uj306m09gaa0.jpg)


## 安装runner

**安装**

``` bash
curl -s https://packages.gitlab.com/install/repositories/runner/gitlab-ci-multi-runner/script.rpm.sh | sudo bash
sudo yum install gitlab-ci-multi-runner-1.11.1-1.x86_64
```
**更新git**

此`gitlab-runner`自带的`git`版本较低，需手动进行更新。

```bash
yum install http://opensource.wandisco.com/centos/6/git/x86_64/wandisco-git-release-6-1.noarch.rpm

yum install git
```
**更改用户**

`gitlab-runner`是以用户`gitlab-runner`来执行脚本，需改成`root`。

```bash
# 确认是否以gitlab-runner在运行，cat /etc/systemd/system/gitlab-runner.service
ps aux | grep gitlab-runner

gitlab-runner uninstall

gitlab-runner install --working-directory /home/gitlab-runner --user root

gitlab-runner restart
```

`runner`常用命令：

``` bash
# 查看运行状态
gitlab-runner status

# 启动
gitlab-runner start

# 停止
gitlab-runner stop

# 注册runner
gitlab-runner register

# 列出所有runner
gitlab-runner list

# 重启服务
gitlab-runner restart
```

<!-- 
**添加其它测试服务器：**

如需添加同步其它机器，需要先进行`104 ssh`免密登录设置。

登录需同步的机器执行下面的命令即可。

``` bash
echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDSsVK9wP8yZSOh/idyaOYH7rTojA4TZ4g7nmJexo6yIUZ1R9Qtz9NN+okbiTcmglGnctstgMZWsQeDO5WA55ovDc1SrWgDLBAjrjreRG7A6IGIKxivVRAVyTDDnyNLZHm7SdiRyHi8jTTermXujE3mWCPYyJI8GjN+yDEdAF0+7pvvjGnU/Nyzdp9GUguSB7l9YAeQmwxdFnL7S4hM+um8r3OahU9De9VGaEvGfMyLvcjdJK48ejbYytBROjfhbDeV+MBUqlcZDCFWZ276pnZ0xnOkJIccrPvG+2O2h88C8bRqWLvACeXpqvW4nDkTCKKJ3aRV8K4xDC7de5vPS+69 test@mljr.com" >> .ssh/authorized_keys
```


`gitlab-runner`如遇以下安装错误：
``` bash
ERROR: Registering runner... failed                 runner=HyZtdyxx status=405 Method Not Allowed
PANIC: Failed to register this runner. Perhaps you are having network problems
```

需要将runner更新
``` bash
curl -s https://packages.gitlab.com/install/repositories/runner/gitlab-ci-multi-runner/script.rpm.sh | sudo bash
sudo yum install gitlab-ci-multi-runner-1.11.1-1.x86_64
``` -->


## 其它

[runner官方安装](https://docs.gitlab.com.cn/runner/install/linux-repository.html)

[runner详细配置](https://gitlab.com/gitlab-org/gitlab-runner/blob/master/docs/configuration/advanced-configuration.md)