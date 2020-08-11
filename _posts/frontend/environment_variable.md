# Linux环境变量
<!-- author:f2engineer@outlook.com -->

## Linux与MAC OS

**常用环境变量**

```
> $PATH：决定了shell将到哪些目录中寻找命令或程序  
> $HOME：当前用户主目录  
> $MAIL：是指当前用户的邮件存放目录。  
> $SHELL：是指当前用户用的是哪种Shell。  
> $HISTSIZE：是指保存历史命令记录的条数  
> $LOGNAME：是指当前用户的登录名。  
> $HOSTNAME：是指主机的名称，许多应用程序如果要用到主机名的话，通常是从这个环境变量中来取得的。
``` 

**查看本机shell类型和当前使用的shell**

```shell
cat /etc/shells
echo $SHELL
```

**配置文件介绍**
 
```html
* /etc/profile
	* 全局文件对所有用户生效（永久设置）
* .bash_profile
	* 针对特定用户，用户登陆读取，来配置自己的环境变量
	* 读取.bash_profile .bashrc也会被执行 所有个性化配置也会生效
* .bashrc .zshrc
	* 当前shell，export TEST="Test"未登录和登录都会被读取
```

`系统读取顺序: /etc/profile -> .bash_profile -> .bashrc`
 
**zsh(oh-my-zsh)**
 
```
zsh的配置文件不再是.zsh_profile;而是直接配置.zshrc即可
```
#### Linux提供了修改和查看环境变量的命令

```
> echo 显示某个环境变量值 echo $PATH
> export 设置一个新的环境变量  (可以无引号)
> env 显示所有环境变量
> set 显示本地定义的shell变量
> unset 清除环境变量 unset HELLO
> readonly 设置只读环境变量 readonly HELLO
```

**eg:**

```
export HELLO="hello"
env | grep 'hello'

unset HELLO

export TEST="Test"
readonly TEST

unset TEST

```

#### 基本操作
* export 临时设置 （export 变量名=变量值）

```shell
直接用export命令：
export PATH=$PATH:/usr/local/nginx/sbin
export NVM_DIR="/Users/pom/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"  # This loads nvm

可用命令export查看
PATH里面已经有了我要加的编译器的路径
```

* 修改全局profile文件 对所有用户生效（永久设置）

```shell
vim /etc/profile

export PATH=$PATH:/usr/local/nginx/sbin
source /etc/profile
```

* PATH声明用法

```
## 你可以自己加上指定的路径，:分隔路径
“PATH=$PATH:路径1:路径2:...:路径n”，
可执行文件的路径包括原先设定的路径，也包括从"路径1"到"路径n"的所有路径
```

**eg:**

```
JAVA_HOME=/usr/share/jdk1.5.0_05  
PATH=$JAVA_HOME/bin:$PATH  
CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar  
export JAVA_HOME  
export PATH  
export CLASSPATH  
```




