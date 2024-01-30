# Linux

# 服务器环境搭建

服务器就是一个远程Linux

使用阿里云的云服务器

1、在阿里云购买的，需要开启安全组实现端口映射

3306 8080 ...

![image-20240123133340544](./markdown-img/Linux狂神.assets/image-20240123133340544.png)

2、获取服务器公网ip地址，修改实例名称和密码





# Linux

一切皆文件

文件就读，写（权限）

1、认识Linux

2、基本命令

* 文件操作
* 目录管理
* 文件属性
* vim编辑器
* 账号管理
* 磁盘管理

...

3、软件安装和部署（Java，tomcat，Docker）



# 入门概述

> 简介

Linux是一套免费使用和自由传播的类Unix操作系统，是一个基于POSIX（可移植操作系统接口）和UNIX的多用户，多任务，支持多线程和多cpu的操作系统

能运行的主要是unix工具软件，网络协议和应用程序，继承了UNIX以网络为核心的设计思想，是一个性能稳定的多用户操作系统



> Linux发行版

Linux的发行版就是将Linux的内核和应用软件做一个打包

![image-20240121210316032](./markdown-img/Linux狂神.assets/image-20240121210316032.png)

补充：
kali Linux：安全渗透测试使用！和网络安全有关



> Linux应用领域

通常服务器使用LAMP（Linux+apache+MySQL+PHP）或LNMP（Linux+Nginx+Mysql+PhP）



# 安装Linux

略



# 走近Linux

> 开机登录

开机的时候会启动许多程序，它们在Windows里被称为“服务”（service），而在Linux里叫“守护进程”（daemon）

一般登录有3种：

* 命令行登录
* ssh登录
* 图形化界面登录

最高权限账号为root，可以操作一切



> 关机

一般很少做

指令：shutdown

```markdown
sync # 防止数据丢失，将数据同步到硬盘里
shutdown 
reboot # 重启，等于shutdown -r now
```



> 系统目录结构

1. 一切皆文件
2. 根目录/ ，所有的文件都在这目录下



* /bin： Binary的缩写，存放最经常使用的命令
* /boot： 存放启动Linux时使用的一些核心文件，包括一些连接文件和镜像文件
* /dev： device，存放外部设备，在Linux中访问设备和文件的方式是相同的
* /mnt：为了用户临时挂载别的文件系统，我们可以把光驱挂在/mnt，这样就可以看光驱里的内容了
* /etc：  存放所有系统管理所需要的配置文件和子目录
* /home：用户的主目录，在Linux里每一个用户都有一个自己的目录，一般目录名是以该用户的账号名命名的
* /lib： 存放着最基本的动态连接库
* /lost+found：一般情况下是空的，当系统非法关机了，这里就存放了一些文件
* /media：Linux会自动识别一些设备如U盘，光驱等，识别后，Linux会把识别的设备挂载到这个目录下
* /opt：给主机安装软件的位置，比如MySQL，Redis之类
* /proc：虚拟目录，内存映射
* /root：系统管理员，超级权限者的用户目录
* /sbin：super user，存放系统管理员的系统管理程序
* /tmp：存放临时文件，用完即丢的文件
* /usr：用户的很多应用程序和文件都放在这个目录下，类似于Windows下的program files目录
* /usr/src：内核源代码默认的放置位置
* /var：经常被修改的文件放这里
* /www：存放服务器网站相关的资源，比如环境和项目

# 常用的基本命令

## 目录管理

> 绝对路径和相对路径

绝对路径：全称

相对路径：相对于当前目录下的位置

```shelll
cd  切换目录
./ 当前目录
cd .. 切换上一级目录
cd ~ 回到当前的用户目录
```

绝对路径都是以/ 开头

> ls 列出目录

最常使用

一些参数

-a 参数，查看全部文件，包括隐藏文件

-l 参数，列出所有文件（不包括隐藏文件），同时包含更多信息

==注意==Linux的参数都可以组合使用

>pwd

显示当前用户所在的用户

> mkdir

创建一个目录

-p 创建层级多级目录

```shell
mkdir -p  test01/test02/test03
```

> rmdir

移除文件夹

rmdir 仅仅能删除空的目录，如果下面存在文件，需要先删除文件

递归删除多个目录需要加-p 参数

```shell
rmdir -p test1/test2/test3
```

> cp

复制文件或者目录

cp 原来的路径 新的路径

如果文件重复，就选择覆盖y或者放弃n

> rm

移除文件或者目录

-f 忽略不存在的文件，不会出现警告，强制删除

-r 递归删除目录

-i 互动，删除询问是否删除

```shell
rm -rf / # 删除所有文件，删库跑路
```

> mv

移动文件，重命名

-f 强制移动

-u 只替换已经更新过的文件



## 基本属性

Linux是一个多用户的系统，每个用户对文件有不同的权限

![image-20240122105226354](./markdown-img/Linux狂神.assets/image-20240122105226354.png)

再Linux中第一个字符代表这个文件是目录，文件或链接文件等等;

==【d】代表目录==

==【-】代表文件==

==【l】代表链接文档==

【b】装置文件里面的可供存储的接口设备

【c】装置文件里面的串行端口设备，如鼠标，键盘



接下来的字符，均以三个为一组，且均为【rwx】三个参数的组合

【r】代表可读

【w】代表可写

【x】代表可执行

如果没有权限，就会出现【-】

![image-20240122105845313](./markdown-img/Linux狂神.assets/image-20240122105845313.png)

对于文件来说，它都有一个特定的所有者，也就是对该文件有所有权的用户

同时再Linux系统里，用户是按组分类的，一个用户属于一个或多个组

> 修改文件属性

1、chgrp:更改文件属性

```shell
chgrp [-R] 属性名 文件名
```

-R 递归更改文件属性 

2、chown 更改文件属主

```shell
chown [-R] 属主名 文件名
chown [-R] 属主名：属组名 文件名
```

==3、chmod更改文件9个属性==

```shell
chmod [-R] XYZ 文件目录
```

Linux文件属性有俩种设置方法，数字和符号

r=>4

w=>2

x=>1

```shell
chmod 777 文件路径 = chmod rwxrwxrwx 文件路径
```

一般使用数字



## 文件内容查看

cat 由第一行开始显示文件内容

![image-20240122132719994](./markdown-img/Linux狂神.assets/image-20240122132719994.png)

tac 从最后一行开始显示

![image-20240122132734998](./markdown-img/Linux狂神.assets/image-20240122132734998.png)

nl 显示的时候把行号一起显示

![image-20240122132650531](./markdown-img/Linux狂神.assets/image-20240122132650531.png)

more 一页一页的显示,用空格跳到下一页，enter代表向下看一行，:f看行号

![image-20240122133052798](./markdown-img/Linux狂神.assets/image-20240122133052798.png)

less 和more类似，但是他可以往前翻页(空格翻页，pageup,pagedown键代表翻页，按q退出)

/字符串 在当前页查找字符串 n代表查找下一个，N代表查找上一个

![image-20240122133916913](./markdown-img/Linux狂神.assets/image-20240122133916913.png)

？字符串  向上查找 n代表查找上一个，N代表查找下 一个

![image-20240122133948097](./markdown-img/Linux狂神.assets/image-20240122133948097.png)

head 只看头几行（-n 数字）

![image-20240122133433258](./markdown-img/Linux狂神.assets/image-20240122133433258.png)

tail 只看尾巴几行（-n 数字）

可以使用man命令来查看各个命令的使用文档，如：man cp

网络配置目录：==cd /etc/sysconfig/network-scripts==

ifconfig 查看网络配置



> linux链 接的概念

分为俩种：硬链接，软连接

硬链接：A---B 假设B是A的硬链接，那么他们来指向同一个文件！允许一个文件拥有多个路径，可以用来保护文件，防止误删



软连接：类似Windows下的快捷方式，删除了原文件，快捷方式就访问不了了



创建链接 ln 命令，默认硬链接，-s 软链接

touch命令创建文件

![image-20240122142144195](./markdown-img/Linux狂神.assets/image-20240122142144195.png)

echo 输入字符串

![image-20240122142306281](./markdown-img/Linux狂神.assets/image-20240122142306281.png)

![image-20240122142725264](./markdown-img/Linux狂神.assets/image-20240122142725264.png)

## Vim编辑器

vim是vi的升级版，vim通过一些插件可以实现和ide一样的功能

vim由代码补全，编译错误跳转等等

只要内容（查看内容，编辑内容，保存内容）

三种模式

* 命令模式
* 输入模式
* 底线命令模式

进入vim

```shell
vim 文件名 # 存在就编辑文件，不存在就创建新文件
```

* i 从命令模式切换到输入命令
* x 删除当前光标处的字符
* : 从命令模式切换到底线命令模式
* esc 退出输入模式
* w 保存文件
* q 退出文件，一般和w联合使用即wq



## 账号管理

> 简介

每个用户账号都拥有一个唯一的用户名和各自的口令

> useradd 命令 添加用户

useradd 选项 用户名 

-c comment 指定一段注释

-d 目录 指定用户主目录，如果此目录不存在就用-m自动创建

-m 自动创建者用户的主目录 /home/shaoxia

-g 用户组 指定用户所属的用户组

-G 用户组，用户组  指定用户所属的附加用户组

-s Shell文件 指定用户登录的Shell

-u 用户号 指定用户的用户号

```bash
[root@192 ~]# useradd -m shaoxia 创建一个用户
[root@192 /]# cd /home
[root@192 home]# ls
shaoxia  shaoxiawjc
```

本质：Linux中一切皆文件，这里的添加文件说白了就是在某一个文件里写入用户的信息了 /etc/passwd

```bash
[root@192 home]# cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
nobody:x:99:99:Nobody:/:/sbin/nologin
systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
polkitd:x:999:998:User for polkitd:/:/sbin/nologin
libstoragemgmt:x:998:995:daemon account for libstoragemgmt:/var/run/lsm:/sbin/nologin
colord:x:997:994:User for colord:/var/lib/colord:/sbin/nologin
rpc:x:32:32:Rpcbind Daemon:/var/lib/rpcbind:/sbin/nologin
saned:x:996:993:SANE scanner daemon user:/usr/share/sane:/sbin/nologin
gluster:x:995:992:GlusterFS daemons:/run/gluster:/sbin/nologin
saslauth:x:994:76:Saslauthd user:/run/saslauthd:/sbin/nologin
abrt:x:173:173::/etc/abrt:/sbin/nologin
setroubleshoot:x:993:990::/var/lib/setroubleshoot:/sbin/nologin
rtkit:x:172:172:RealtimeKit:/proc:/sbin/nologin
pulse:x:171:171:PulseAudio System Daemon:/var/run/pulse:/sbin/nologin
radvd:x:75:75:radvd user:/:/sbin/nologin
chrony:x:992:987::/var/lib/chrony:/sbin/nologin
unbound:x:991:986:Unbound DNS resolver:/etc/unbound:/sbin/nologin
qemu:x:107:107:qemu user:/:/sbin/nologin
tss:x:59:59:Account used by the trousers package to sandbox the tcsd daemon:/dev/null:/sbin/nologin
sssd:x:990:984:User for sssd:/:/sbin/nologin
usbmuxd:x:113:113:usbmuxd user:/:/sbin/nologin
geoclue:x:989:983:User for geoclue:/var/lib/geoclue:/sbin/nologin
ntp:x:38:38::/etc/ntp:/sbin/nologin
gdm:x:42:42::/var/lib/gdm:/sbin/nologin
rpcuser:x:29:29:RPC Service User:/var/lib/nfs:/sbin/nologin
nfsnobody:x:65534:65534:Anonymous NFS User:/var/lib/nfs:/sbin/nologin
gnome-initial-setup:x:988:982::/run/gnome-initial-setup/:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
avahi:x:70:70:Avahi mDNS/DNS-SD Stack:/var/run/avahi-daemon:/sbin/nologin
postfix:x:89:89::/var/spool/postfix:/sbin/nologin
tcpdump:x:72:72::/:/sbin/nologin
shaoxiawjc:x:1000:1000:shaoxiawjc:/home/shaoxiawjc:/bin/bash
shaoxia:x:1001:1001::/home/shaoxia:/bin/bash
```

> 删除用户 userdel

userdel -r shaoxia

-r 删除用户的时候将他的目录一并删除

> 修改用户 usermod

usermod 对应修改的内容（选项） 用户名

常用的选项和添加用户的差不多 

修改完后看一下配置文件

> 切换用户

su username (不加username 默认root用户)

 ![image-20240122151741902](./markdown-img/Linux狂神.assets/image-20240122151741902.png)

【#】代表超级权限

【$】代表一般权限

> exit 退出用户

> hostname 修改名字

hostname + 名字 修改后重连电脑

> 用户的密码设置

通过root创建用户的时候，配置密码

```bash
password 用户名 # 回车后输入密码 
```

如果是普通用户

```bash
password # 先输入当前密码，然后会提示输入新密码，新密码不能太简单 
```

> 锁定用户

root用户可以冻结用户，让用户无法登录

```shell
passwd -l 用户名 # lock一个用户
passwd -d 用户名 # 把用户的密码取消掉，没有密码也不能登录
```

## 用户组管理

每一个用户都有一个用户组

用户组的管理本质就是对/etc/group的更新

> 创建一个用户组 groupadd 组名

```shell
[root@192 home]# groupadd xiagp
```

```shell
shaoxiawjc:x:1000:
xiagp:x:1001:
```

创建完用户组后可以得到一个id，这个id是可以指定的！-g 666,如果不指定，就id自增

> 删除用户组 groupdel

> 修改用户的权限和名字 groupmod

-g id   改id

-n 名字   改名字

> 用户切换组

```shell
# 登录当前用户
$ newgroup root
```

> 文件的查看

/etc/passwd

用户名:口令(登录密码，我们不可见):用户标识号:组标识号：注释性描述:主目录：登录shell

```shell
root:x:0:0:root:/root:/bin/bash
```

真正加密后端登录口令（密码）是在/etc/shadow文件下的，保证我们密码的安全性



## 磁盘管理

> df (列出文件系统整体的磁盘使用量) 

![image-20240122192505692](./markdown-img/Linux狂神.assets/image-20240122192505692.png)

-h 使用M或G作为大小单位

![image-20240122192622165](./markdown-img/Linux狂神.assets/image-20240122192622165.png)

>  du （检查文件空间使用量）

-a 查看隐藏文件包括子文件夹

```shell
du -sm /* # 检查根目录下的容量
```

```bash
[root@192 shaoxiawjc]# du -sm /*
0       /bin
137     /boot
0       /dev
43      /etc
7       /home
0       /lib
0       /lib64
0       /media
0       /mnt
0       /opt
du: 无法访问"/proc/21780/task/21780/fd/3": 没有那个文件或目录
du: 无法访问"/proc/21780/task/21780/fdinfo/3": 没有那个文件或目录
du: 无法访问"/proc/21780/fd/3": 没有那个文件或目录
du: 无法访问"/proc/21780/fdinfo/3": 没有那个文件或目录
0       /proc
7       /root
15      /run
0       /sbin
0       /srv
0       /sys
2       /tmp
3932    /usr
1175    /var
```

将外部设备挂在到mnt目录下可以正常访问

```shell
mount /dev/shaoxia /mnt/shaoxia # 挂载
```

卸载 umount -f 挂载的位置 强制卸载



## 进程管理

> 什么是进程

* 在Linux或Windows上，每一个程序都有一个自己的进程，每一个进程都有一个进程号

* 每一个进程都有一个父进程

* 俩种存在方式，前台和后台

* 一般的服务都是后台运行的，基本的程序是前台运行的

> 命令

``ps``查看当前系统中正在进行的各种进程的信息

ps -xx:

* -a 显示当前终端运行的所有进程信息(当前的进程)
* -u 以用户信息显示进程
* -x 显示后台运行进程的参数

grep 过滤

```bash
ps -aux # 查看所有进程
ps -aux|grep mysql # 查看MySQL的进程
```

ps -ef 可以看到父进程的信息

但是看父进程一般可以通过看目录数来查看

pstree 

* -p 显示父id
* -u 显示用户组

结束进程

```shell
kill -9 id
```

# 环境安装

3种安装软件的方式

* rpm安装(jdk)
* 解压缩安装(tomcat)
* yum在线(docker)

## JDK安装

1、下载jdk rpm

2、安装Java环境

```bash
# 检测当前环境是否有Java环境java -version，如果有就卸载
# 卸载 rpm -qal|grep jdk    rpm -e --nodeps jdk_

# 卸载完后就可以安装了
rpm -ivh 安装包

# 配置环境变量
cd /etc/profile

JAVA_HOME=/usr/java/jdk1.8.0_202-i586
CLASSPATH=%JAVA_HOME%/lib:%JAVA_HOME%/jre/lib
PATH=$PATH:$JAVA_HOME/bin:$JAVA_HOME/jre/bin
export PATH CLASSPATH JAVA_HOME
```







## tomcat安装

1、下载tomcat的tar.gz压缩包

2、解压

```shell
tar -zxvf 压缩包
```

3 、启动tomcat测试! ./xxx.sh脚本

```shell
# 执行 ./startup.sh
# 停止 ./shutdown.sh
```

4、确保防火墙的端口是开着的，如果是阿里云，需要保证阿里云的安全组策略是开放的

```bash
# 查看firewall服务状态
systemctl status firewalld

# 开启、重启、关闭、firewalld.service服务
# 开启
service firewalld start
# 重启
service firewalld restart
# 关闭
service firewalld stop

# 查看防火墙规则
firewall-cmd --list-all    # 查看全部信息
firewall-cmd --list-ports  # 只看端口信息

# 开启端口
开端口命令：firewall-cmd --zone=public --add-port=80/tcp --permanent
重启防火墙：systemctl restart firewalld.service

命令含义：
--zone #作用域
--add-port=80/tcp  #添加端口，格式为：端口/通讯协议
--permanent   #永久生效，没有此参数重启后失效
```

上传完毕的项目直接购买自己的域名，备案解析过去即可

域名解析后，如果端口是80或443可以直接访问，如果是其他的9000或8080，就需要用apache或nginx做一下反向代理



## yum安装



















































































































































































































































































































































































































































































