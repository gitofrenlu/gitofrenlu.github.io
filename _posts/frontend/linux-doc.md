# Linux目录结构及文件基本操作(1)
## 区别
```
win以存储介质为主，主要以盘符及分区实现文件管理，再下面才是目录。
linux以树形目录结构的形式来构造整个系统，每一个目录不仅能使用本地磁盘分区的文件系统，也能使用网络的文件系统。
```
## 结构
![avatar](https://femans.com/images/linux_dir.png)
**[查看](https://femans.com/images/linux_dir.png)**

## 内建命令&&系统命令      
1.内建命令要比系统论命令有比较高的执行效率。
2.外部命令执行时往往需要fork出（产生出）一个子进程，而内建命令一般不用。

- 查看一个命令是系统命令还是内建命令：type
```
➜  ~ type pwd
pwd is a shell builtin
➜  ~ type -a pwd
pwd is a shell builtin
pwd is /bin/pwd

➜  ~ type -a echo
echo is a shell builtin
echo is /bin/echo

➜  ~ type -a cd
cd is a shell builtin
cd is /usr/bin/cd
```
可以看出，有些命令，echo,pwd,cd同时是内建命令和系统命令。

- 命令类型
```
alias：别名。
keyword：关键字，Shell保留字。
function：函数，Shell函数。
builtin：内建命令，Shell内建命令。
file：文件，磁盘文件，外部命令。
unfound：没有找到。
```
选项：
```
-p：如果给出的指令为外部指令，则显示其绝对路径；
-a：打印指令的所有位置。
```



# 常用命令解析
## ls 

文件与目录查看命令,含有多个参数如下：
```
-l ：列出长数据串，包含文件的属性与权限数据等  
-a ：列出全部的文件，连同隐藏文件（开头为.的文件）一起列出来（常用）  
-d ：仅列出目录本身，而不是列出目录的文件数据  
-h ：将文件容量以较易读的方式（GB，kB等）列出来  --(ls)
-R ：连同子目录的内容一起列出（递归列出），等于该目录下的所有文件都会显示出来  
```
- Linux下可以用`ll(ls -l)` 命令来判断文件类型，主要是根据每行的首个字符来判断。
```
-rw-r--r--   1      baijiyun    staff       910     2   27  14:05   b.html
drwxr-xr-x   7      baijiyun    staff       238     4   20  17:26   demo
    ①        ②       ③           ④           ⑤       ⑥   ⑦      ⑧      ⑨

# ①.文件属性字段  
# ②.文件硬链接数或目录子目录数
# ③.文件拥有者  
# ④.文件拥有者所在的组
# ⑤.文件文件大小(以字节为单位)  
# ⑥.文件创建月份
# ⑦.文件创建日期 
# ⑧.文件创建时间
# ⑨.文件名

```
-  文件属性字段解析：
```
  

    文件属性字段总共有10个字母组成,
    第一个字母表示文件类型:
    -   普通文件
    d   目录文件
    b   块设备文件
    c   字符设备文件
    l   符号链
    p   管道特殊文件

    第一字段的后面9个字母表示文件的权限.
    其中前三个表示文件属主的权限,中间三个表示组用户权限,最后三个表示其他用户权限.

    `r表是读 (Read)->4` `w表示写 (Write)->2` `x表示执行 (eXecute)->1`
    通过4、2、1的组合，得到以下几种权限：
    0（没有权限）；4（读取权限）；5（4+1 | 读取+执行）；6（4+2 | 读取+写入）；7（4+2+1 | 读取+写入+执行）

    以754为例：
    1-3位7等于4+2+1，rwx，所有者具有读取、写入、执行权限； 
    4-6位5等于4+1+0，r-x，同组用户具有读取、执行权限但没有写入权限； 
    7-9位4，其他用户具有读取但没有写入权限和执行权限
```


##  cd 
切换当前目录，它的参数是要切换到的目录的路径，可以是绝对路径，也可以是相对路径。如：
```
cd /data/femans    # 切换到目录/data/femans 
cd ./path          # 切换到当前目录下的path目录中，“.”表示当前目录    
cd ../path         # 切换到上层目录中的path目录中，“..”表示上一层目录  
```

## pwd     
显示当前层级目录结构
```
pwd  
```

##  find         
基于查找的功能非常强大的命令，使用起来相对复杂，参数也比较多，整理出常见用法，它的基本语法如下：
```
find [PATH] [option] [action]

# 与时间有关的参数：
-mtime n : n为数字，意思为在n天之前的“一天内”被更改过的文件；
-mtime +n : 列出在n天之前（不含n天本身）被更改过的文件名；
-mtime -n : 列出在n天之内（含n天本身）被更改过的文件名；
-newer file : 列出比file还要新的文件名
# 例如：
find /Users/baijiyun/mljr/fe-docs -mtime 0 # 在当前目录下查找今天之内有改动的文件

# 与用户或用户组名有关的参数：
-user name : 列出文件所有者为name的文件
-group name : 列出文件所属用户组为name的文件
-uid n : 列出文件所有者为用户ID为n的文件
-gid n : 列出文件所属用户组为用户组ID为n的文件
# 例如：
find /Users/baijiyun/mljr/fe-docs -user baijiyun # 在目录/Users/baijiyun中找出所有者为baijiyun的文件

# 与文件权限及名称有关的参数：
-name filename ：找出文件名为filename的文件
-size [+-]SIZE ：找出比SIZE还要大（+）或小（-）的文件
-tpye TYPE ：查找文件的类型为TYPE的文件，TYPE的值主要有：一般文件（f)、设备文件（b、c）、
             目录（d）、连接文件（l）、socket（s）、FIFO管道文件（p）；
-perm mode ：查找文件权限刚好等于mode的文件，mode用数字表示，如0755；
-perm -mode ：查找文件权限必须要全部包括mode权限的文件，mode用数字表示
-perm +mode ：查找文件权限包含任一mode的权限的文件，mode用数字表示
# 例如：
find / -name passwd # 查找文件名为passwd的文件
find . -perm 0755 # 查找当前目录中文件权限的0755的文件
find . -size +12k # 查找当前目录中大于12KB的文件，注意c表示byte
```

## sudo     
Linux sudo命令以系统管理者的身份执行指令，也就是说，经由 sudo 所执行的指令就好像是 root 亲自执行。
使用权限：在 /etc/sudoers 中有出现的使用者。
```
-b：在后台执行指令；
-h：显示帮助；
-H：将HOME环境变量设为新身份的HOME环境变量；
-k：结束密码的有效期限，也就是下次再执行sudo时便需要输入密码；。
-l：列出目前用户可执行与无法执行的指令；
-p：改变询问密码的提示符号；
-s<shell>：执行指定的shell；
-u<用户>：以指定的用户作为新的身份。若不加上此参数，则预设以root作为新的身份；
-v：延长密码有效期限5分钟；
-V ：显示版本信息。
```

##  cp
用于复制文件，copy之意，它还可以把多个文件一次性地复制到一个目录下，它的常用参数如下：
```
-a ：将文件的特性一起复制  
-p ：连同文件的属性一起复制，而非使用默认方式，与-a相似，常用于备份  
-i ：若目标文件已经存在时，在覆盖时会先询问操作的进行  
-r ：递归持续复制，用于目录的复制行为  
-u ：目标文件与源文件有差异时才会复制  
```
- 例：
```
cp -a file1 file2 #连同文件的所有特性把文件file1复制成文件file2  
cp file1 file2 file3 dir #把文件file1、file2、file3复制到目录dir中  
```

## scp    
- Linux scp命令用于Linux之间复制文件和目录。
- scp是 secure copy的缩写, scp是linux系统下基于ssh登陆进行安全的远程文件拷贝命令。

```
scp [可选参数] file_source file_target 
```
- 从本地复制到远程
```
scp local_file remote_username@remote_ip:remote_folder 
或者 
scp local_file remote_username@remote_ip:remote_file 
#第1,2个指定了用户名，命令执行后需要再输入密码，第1个仅指定了远程的目录，文件名字不变，第2个指定了文件名；

或者 
scp local_file remote_ip:remote_folder 
或者 
scp local_file remote_ip:remote_file 
# 第3,4个没有指定用户名，命令执行后需要输入用户名和密码，第3个仅指定了远程的目录，文件名字不变，第4个指定了文件名；
```

- 应用实例
```
scp /home/space/music/1.mp3 root@111.111.111.111:/home/root/others/music 
scp /home/space/music/1.mp3 root@111.111.111.111:/home/root/others/music/001.mp3 
scp /home/space/music/1.mp3 111.111.111.111:/home/root/others/music 
scp /home/space/music/1.mp3 111.111.111.111:/home/root/others/music/001.mp3 
```

- 复制目录命令格式
```
scp -r local_folder remote_username@remote_ip:remote_folder 
# 第1个指定了用户名，命令执行后需要再输入密码；


或者 
scp -r local_folder remote_ip:remote_folder 
# 第2个没有指定用户名，命令执行后需要输入用户名和密码；
```
- 应用实例:
```
scp -r /home/space/music/ root@111.111.111.111:/home/root/others/ 
scp -r /home/space/music/ 111.111.111.111:/home/root/others/ 

#上面命令将本地 music 目录复制到远程 others 目录下。
```

- 从远程复制到本地  
从远程复制到本地，只要将从本地复制到远程的命令的后2个参数调换顺序即可，如下实例
```
scp root@www.runoob.com:/home/root/others/music /home/space/music/1.mp3 
scp -r www.runoob.com:/home/root/others/ /home/space/music/
```

- 说明
1.如果远程服务器防火墙有为scp命令设置了指定的端口，我们需要使用 -P 参数来设置命令的端口号，命令格式如下：
```
#scp 命令使用端口号 4588
scp -P 4588 remote@www.runoob.com:/usr/local/sin.sh /home/administrator
```
2.使用scp命令要确保使用的用户具有可读取远程服务器相应文件的权限，否则scp命令是无法起作用的。


   
##  mv          
该命令用于移动文件、目录或更名，move之意，它的常用参数如下：
```
-f ：force强制的意思，如果目标文件已经存在，不会询问而直接覆盖  
-i ：若目标文件已经存在，就会询问是否覆盖  
-u ：若目标文件已经存在，且比目标文件新，才会更新  
```
- 注意：该命令可以把一个文件或多个文件一次移动一个文件夹中，但是最后一个目标文件一定要是`“目录”`。
- 例：
```
mv file1 file2 file3 dir # 把文件file1、file2、file3移动到目录dir中  
mv file1 file2 # 把文件file1重命名为file2  
```

## rm           
该命令用于删除文件或目录，remove之间，它的常用参数如下：
```
-f ：就是force的意思，忽略不存在的文件，不会出现警告消息  
-i ：互动模式，在删除前会询问用户是否操作  
-r ：递归删除，最常用于目录删除，它是一个非常危险的参数  
```
- 例:
```
rm -i file # 删除文件file，在删除之前会询问是否进行该操作  
rm -rf dir # 强制删除目录dir中的所有文件  
```

## file   
该命令用于判断接在file命令后的文件的基本数据，因为在Linux下文件的类型并不是以后缀为分的，所以这个命令对我们来说就很有用了，它的用法非常简单，基本语法如下：
```
file filename  
#例如：  
file ./test  
```

## cat
该命令用于查看文本文件的内容，后接要查看的文件名，通常可用管道与more和less一起使用，从而可以一页页地查看数据。例如：
```
cat text | less # 查看text文件中的内容  
# 注：这条命令也可以使用less text来代替  
```

## chmod        
该命令用于改变文件的权限，一般的用法如下：   
```
chmod [-R] xyz 文件或目录  
-R：进行递归的持续更改，即连同子目录下的所有文件都会更改 
```

    同时，chmod还可以使用u（user）、g（group）、o（other）、a（all）和+（加入）、-（删除）、=（设置）跟rwx搭配来对文件的权限进行更改。

```
# 例如：  
chmod 0755 file # 把file的文件权限改变为-rxwr-xr-x  
chmod g+w file # 向file的文件权限中加入用户组可写权限 
```

## tar   
该命令用于对文件进行打包，默认情况并不会压缩，如果指定了相应的参数，它还会调用相应的压缩程序（如gzip和bzip等）进行压缩和解压。它的常用参数如下：
```
-c ：新建打包文件  
-t ：查看打包文件的内容含有哪些文件名  
-x ：解打包或解压缩的功能，可以搭配-C（大写）指定解压的目录，注意-c,-t,-x不能同时出现在同一条命令中  
-j ：通过bzip2的支持进行压缩/解压缩  
-z ：通过gzip的支持进行压缩/解压缩  
-v ：在压缩/解压缩过程中，将正在处理的文件名显示出来  
-f filename ：filename为要处理的文件  
-C dir ：指定压缩/解压缩的目录dir  
```
- 上面的解说可以已经让你晕过去了，但是通常我们只需要记住下面三条命令即可：
```
压缩：tar -jcv -f filename.tar.bz2 要被处理的文件或目录名称  
查询：tar -jtv -f filename.tar.bz2  
解压：tar -jxv -f filename.tar.bz2 -C 欲解压缩的目录 
# 注：文件名并不定要以后缀tar.bz2结尾，这里主要是为了说明使用的压缩程序为bzip2
```



## ps
该命令用于将某个时间点的进程运行情况选取下来并输出，process之意，它的常用参数如下：
```
-A ：所有的进程均显示出来  
-a ：不与terminal有关的所有进程  
-u ：有效用户的相关进程  
-x ：一般与a参数一起使用，可列出较完整的信息  
-l ：较长，较详细地将PID的信息列出  
```
- 其实我们只要记住ps一般使用的命令参数搭配即可，它们并不多，如下：
```
ps aux # 查看系统所有的进程数据  
ps ax # 查看不与terminal有关的所有进程  
ps -lA # 查看系统所有的进程数据  
ps axjf # 查看连同一部分进程树状态  
```

## grep
该命令常用于分析一行的信息，若当中有我们所需要的信息，就将该行显示出来，该命令通常与管道命令一起使用，用于对一些命令的输出进行筛选加工等等，它的简单语法为
```
grep [-acinv] [--color=auto] '查找字符串' filename  
```
- 参数：
```
-a ：将binary文件以text文件的方式查找数据  
-c ：计算找到‘查找字符串’的次数  
-i ：忽略大小写的区别，即把大小写视为相同  
-v ：反向选择，即显示出没有‘查找字符串’内容的那一行  
# 例如：  
# 取出文件/etc/man.config中包含MANPATH的行，并把找到的关键字加上颜色  
grep --color=auto 'MANPATH' /etc/man.config  
# 把ls -l的输出中包含字母file（不区分大小写）的内容输出  
ls -l | grep -i file  
```




