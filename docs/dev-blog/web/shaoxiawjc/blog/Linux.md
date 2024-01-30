# linux

# 概述

服务器操作系统

包括内核和程序

内核是完全开源的，提供Linux的主要功能，如硬件调度

内核+系统程序的完整封装，称为Linux发行版

因此，只要自己编写系统程序，加上内核，就有一套Linux发行版

市面上的一些Linux发行版

![image-20240115232739324](./markdown-img/Linux.assets/image-20240115232739324.png)

主要的centOS，Ubuntu







# 虚拟机

虚拟的电脑

在电脑里通过软件模拟计算机硬件，安装真实的操作系统

## VMware

虚拟化软件

安装VMware WorkStation

同时确保网络适配器里有虚拟网卡

![image-20240116000051788](./markdown-img/Linux.assets/image-20240116000051788.png)

> 在VMware WorkStation上部署linux

安装操作系统软件

使用centos

[开源镜像站-阿里云 (aliyun.com)](https://mirrors.aliyun.com/centos/7.9.2009/isos/x86_64/)

![image-20240116133451286](./markdown-img/Linux.assets/image-20240116133451286.png)



# 命令行

在linux的开发里，通常是使用命令行进行开发

下载finalShell，远程连接到Linux操作系统里



# Linux的目录结构

由根目录衍生的树目录

没有盘符这概念

Linux的路径描述用 /  Windows则是用 \



# Linux命令

## 基础格式

```bash
command [-options] [parameter]
```

* command 命令本身
* -options 非必填 命令的一些选项，用来控制命令的行为细节
* parameter 非必填 命令的一些参数，表示命令的目标

## ls命令

```bash
ls [-a -l -h] [Linux路径]
```

直接使用ls命令本体时，表示：平铺形式列出当前工作目录下的内容

![image-20240116142916097](./markdown-img/Linux.assets/image-20240116142916097.png)

在Linux里，默认的工作目录是/home目录

[Linux路径] 表示指定的路径

``-a`` 表示all  列出全部的文件（包括隐藏的文件和文件夹）

其中，以 . 开头的文件或文件夹就是隐藏的

``-l``以竖向的列表展示文件，同时展示更多信息

同时，命令的选项是可以组合使用

组合的方式可以是

```bash
ls -l -a
ls -la
ls -al
```

![image-20240116143742858](./markdown-img/Linux.assets/image-20240116143742858.png)

``-h``要和-l组合使用，表示以k，g等单位来显示文件大小



## cd和pwd命令

cd命令，change，用于切换目录

```bash
cd [路径]
```

不写参数代表回到home目录

pwd命令，列出当前的工作目录 Print Work Directory

```bash
pwd
```

无参数，无选项



## 相对路径和绝对路径

绝对路径  cd /home/shaoxiawjc/Desktop

相对路径  cd Desktop



## 特殊路径符

*  .  表示当前目录 比如 cd ./Desktop 代表切换到当前目录下的Desktop 和 cd Desktop 一样的效果
* ..  表示上一级目录，比如：cd .. 即可切换到上一级目录，cd ../.. 表示切换到上俩级目录
* ~   表示home目录，比如: cd ~ 即可切换到home目录，又比如：cd ~/Desktop 切换到home下的Desktop



## mkdir命令

创建文件夹

```bash
mkdir [-p] Linux路径
```

* 参数必填

* -p可选，表示自动创建不存在的父目录

  * 比如

  ```bash
  [shaoxiawjc@bogon ~]$ mkdir -p test01/test011/test0111
  [shaoxiawjc@bogon ~]$ cd  test01/test011/test0111
  [shaoxiawjc@bogon test0111]$ pwd
  /home/shaoxiawjc/test01/test011/test0111
  ```



## touch命令

创建文件

```bash
touch 路径
```

路径必填，表示要创建的文件的路径



## cat

查看文件内容

```bash
cat 路径
```

```bash
[shaoxiawjc@bogon ~]$ cat demo01.txt
123456789iheuigioejiojioiosjiojiaojgiojiojisojdiogjio
```



## more

也是查看文件内容

与cat不同的是

* cat是将内容全部显示出来
* more支持翻页，如果文件内容过多，可以一页一页的看

```shell
more 路径
```

在查看过程里

* 用空格翻页
* 用q退出查看



## cp命令

复制文件或文件夹

```shell
cp [-r] 参数1  参数2
```

* -r 可选 用于复制文件夹使用，表示递归
* 参数1  表路径 表示被复制的文件或文件夹
* 参数2 表示目标位置



## mv 

移动文件或文件夹

```shell
mv 参数1 参数2
```

参数1 表示被移动的文件或文件夹

参数2  表示目标路径

同时，如果移动到同级目录下，并且参数2的名字和原来不一样，即可实现改名效果



## rm

用于删除文件或文件夹

```shell
rm [-r -f] 参数1 参数2 ... 参数N	
```

* -r 用于删除文件夹
* -f 表示force 表示强制删除（不会弹出确认信息）
  * 普通用户删除不会弹出提示，只有root管理员删除时有提示
  * 一般用户用不到-f
* 参数1 参数2 ... 参数N 表示要删除的文件或文件夹路径，用空格隔开



## which

我们之前的一系列命令就是一个个二进制文件，和Windows的.exe相似

通过which命令查找这些文件的位置

```shell
which 要查找的命令
```

```shell
[shaoxiawjc@bogon ~]$ which cd
/usr/bin/cd
[shaoxiawjc@bogon ~]$ which rm
/usr/bin/rm
[shaoxiawjc@bogon ~]$ which ls
alias ls='ls --color=auto'
        /usr/bin/ls
[shaoxiawjc@bogon ~]$ which mv
/usr/bin/mv
```



## find 命令 

按文件名查找文件

``````shell
find 起始位置 -name "被查找的文件名"
``````



```bash
[root@bogon ~]# find / -name "test"
find: ‘/run/user/1000/gvfs’: Permission denied
/usr/bin/test
/usr/lib/modules/3.10.0-1160.el7.x86_64/kernel/drivers/ntb/test
/usr/lib/alsa/init/test
/usr/lib64/python2.7/test
/usr/lib64/python2.7/unittest/test
/usr/share/espeak-data/voices/test
/usr/src/kernels/3.10.0-1160.el7.x86_64/drivers/ntb/test
/usr/src/kernels/3.10.0-1160.el7.x86_64/include/config/test
/usr/src/kernels/3.10.0-1160.el7.x86_64/lib/raid6/test
```

## 通配符

符号* 表示通配符，匹配任意内容，包括空

test* 表示以任何以test开头的内容

*test * test * 类似

 

## find按文件大小查找

```shell
find 起始路径 -size +/- -n[KMG]
+ - 表示大于 小于
n 表示 数字
KMG 表示大小单位 k（小写）kb  M表示MB G表示GB
```



## grep

从文件中通过关键字过滤文件行

```shell
grep [-n] 关键字 文件路径
```

* -n 可选 在结果显示匹配的行的行号
* 参数 关键字 必填 过滤的关键字 如果带有空格或其他符号 建议用  “” 将关键字包裹起来
* 参数 文件路径 要过滤的内容的文件路径 可作为内容输出端口

```shell
Last login: Thu Jan 18 23:40:01 2024 from 192.168.242.1
[shaoxiawjc@192 ~]$ touch test01.txt
[shaoxiawjc@192 ~]$ cat test.txt
cat: test.txt: 没有那个文件或目录
[shaoxiawjc@192 ~]$ cat test01.txt
shaoxia
123456
[shaoxiawjc@192 ~]$ grep "shaoxia" test01.txt
shaoxia
[shaoxiawjc@192 ~]$ grep "s" test01.txt 
shaoxia
suuigiuwe
[shaoxiawjc@192 ~]$ cat test01.txt
shaoxia
123456
12
suuigiuwe
gghhjhj
[shaoxiawjc@192 ~]$ grep -n "1" test01.txt
2:123456
3:12
[shaoxiawjc@192 ~]$ 
```



## wc命令

 做文件内容的统计，包括行数，单词数等

```shell
wc [-c -m -l -w] 文件路径
```

* -c 统计bytes数量
* -m 统计字符数量
* -l 统计行数
* -w 统计单词数量
* 参数 文件路径 可作为内容输入端口

```shell
[shaoxiawjc@192 ~]$ wc test01.txt
 5  5 36 test01.txt
行数 单词数 当前文件字节数
[shaoxiawjc@192 ~]$ wc -c test01.txt
36 test01.txt
[shaoxiawjc@192 ~]$ wc -m test01.txt
36 test01.txt
[shaoxiawjc@192 ~]$ wc -l test01.txt
5 test01.txt
[shaoxiawjc@192 ~]$ wc -w test01.txt
5 test01.txt
```



## 管道符  |

将左边命令的结果作为右边命令的输入

```shell
[shaoxiawjc@192 ~]$ cat test01.txt | grep "s"
shaoxia
suuigiuwe

[shaoxiawjc@192 ~]$ cat test01.txt | wc 
      5       5      36

[shaoxiawjc@192 ~]$ ls -l | grep "test01.txt"
-rw-rw-r--. 1 shaoxiawjc shaoxiawjc 36 1月  19 04:08 test01.txt

[shaoxiawjc@192 ~]$ ls -l | wc -l
10

[shaoxiawjc@192 ~]$ cat test01.txt | grep "s" | grep "w"
suuigiuwe

```



## echo命令

在命令行输出指定内容

```shell
echo 指定内容
[shaoxiawjc@192 ~]$ echo 123
123
[shaoxiawjc@192 ~]$ echo "hello"
hello
```

建议使用双引号包围起来



## 反引号

被包围的内容作为指令执行而不是字符串

```shell'
[shaoxiawjc@192 ~]$ echo `pwd`
/home/shaoxiawjc
```



## 重定向符 > >>

```java
> 将左侧的命令结果，覆盖写入到符号右侧的指定文件里
>> 将左侧命令的结果，追加写入到符号右侧的指定文件里
```

```java
[shaoxiawjc@192 ~]$ cat test01.txt
hello linux
[shaoxiawjc@192 ~]$ echo "\nhhh" >> test01.txt
[shaoxiawjc@192 ~]$ cat test01.txt
hello linux
\nhhh
```



## tail命令

查看文件尾部内容，追踪文件最新修改

```bash
tail [-f -num] Linux路径
```

* -f 表示持续跟踪
* -num 表示查看尾部多少行，不填默认10行

```shell
[shaoxiawjc@192 ~]$ ls > test01.txt
[shaoxiawjc@192 ~]$ ls / >> test01.txt
[shaoxiawjc@192 ~]$ cat test01.txt
Desktop
Documents
Downloads
Music
Pictures
Public
Templates
test01.txt
Videos
bin
boot
dev
etc
home
lib
lib64
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var
[shaoxiawjc@192 ~]$ tail test01.txt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var
[shaoxiawjc@192 ~]$ tail -5 test01.txt
srv
sys
tmp
usr
var
```

-f 会持续追踪更改

使用CTRL+C 停止-f追踪

---

# vi/vim编辑器

visual interface的简称，Linux的最经典的文本编辑器

vim这是vi的加强版本，不仅能编辑文本，还有shell程序编辑的功能，可以用不同颜色的字体来区分辨别语法的正确性

## 三种工作模式

> 命令模式

所敲的按键全部理解为命令，以命令驱动执行不同的功能

此模式下，不能自由进行文本编辑

> 输入模式

编辑模式，自由编辑

> 底线命令模式

用于文件的保存，退出

![image-20240119204646022](./markdown-img/Linux.assets/image-20240119204646022.png)



基础语法

```shell
vi 文件路径
vim 文件路径
```

* 如果文件路径不存在，则命令会用于编辑新文件
* 如果文件路径已存在，则命令会用于编辑已有文件

## 快速体验

输入vim 文件路径进入命令模式，同时附注新文件or旧文件

![image-20240119210926431](./markdown-img/Linux.assets/image-20240119210926431.png)

按 i 键进入输入模式

按 Esc 退出插入模式 

按 ： 进入底线命令模式

​	输入w进行保存

​	输入q进行退出

​	也可以输入wq保存并退出



| 模式     | 命令    | 描述                           |
| -------- | ------- | ------------------------------ |
| 命令模式 | i       | 在当前光标位置进入输入模式     |
| 命令模式 | a       | 在当前光标位置之后进入输入模式 |
| 命令模式 | shift+i | 在当前行的开头进入输入模式     |
| 命令模式 | shift+a | 在当前光标的末尾进入输入模式   |
| 命令模式 | o       | 在当前光标的下一行进入输入模式 |
| 命令模式 | shift+o | 在当前光标的上一行进入输入模式 |
| 输入模式 | Esc     | 退回命令模式                   |

![image-20240120151003309](./markdown-img/Linux.assets/image-20240120151003309.png)

![image-20240120151239931](./markdown-img/Linux.assets/image-20240120151239931.png)























































