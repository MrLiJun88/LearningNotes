# Linux学习

## 1.Shell编程

### 1.条件判断

#### 1.根据文件类型进行判断

| 测试选项  |                           作用                           |
| :-------: | :------------------------------------------------------: |
| -b 文件名 | 判断该文件是否存在，且是否为块设备文件(是块设备文件为真) |
| -c 文件名 |      判断该文件是否存在，且为字符设备文件(是则为真)      |
| -d 文件名 |            判断该文件是否存在，且为目录才为真            |
| -e 文件名 |              判断该文件是否存在 ，存在为真               |
| -f 文件名 |          判断该文件是否存在，且为普通文件则为真          |
| -L 文件名 |       判断该文件是否存在，且为符号链接文件，则为真       |
| -p 文件名 |        判断该文件是否存在，并且为管道文件，则为真        |
| -s 文件名 |               判断该文件是否存在，非空为真               |

#### 2.根据文件权限进行判断

|  -r  |  判断文件是否存在，并有读权限  |
| :--: | :----------------------------: |
|  -w  |  判断文件是否存在，并有写权限  |
|  -x  | 判断文件是否存在，并有执行权限 |

#### 3.两个整数的比较

| -eq  |   相等   |
| :--: | :------: |
| -ne  |  不相等  |
| -gt  |   大于   |
| -lt  |   小于   |
| -ge  | 大于等于 |
| -le  | 小于等于 |

#### 4.字符串的判断

|     -z 字符串      | 字符串为空返回真 |
| :----------------: | :--------------: |
|     -n 字符串      |   不为空返回真   |
| 字符串1 == 字符串2 |    相等返回真    |
| 字符串1 != 字符串2 |   不相等返回真   |

#### 5.多重条件判断

| 判断1 -a 判断2 | 逻辑与 |
| :------------: | :----: |
|       -o       | 逻辑或 |
|       !        | 逻辑非 |

### 2.流程控制

#### 1.if条件判断

````powershell
#单分支if条件
if [ 条件判断式 ] 
      then
          程序
fi
#双分支if条件
if [ 条件判断式 ]
    then
         条件成立时，执行
     else 
         条件不成立时，执行
fi
#多分支if条件
if [ 条件判断式 ]
   then
       语句1
elif [ 条件判断2 ]
   then 
       语句2
  ...
  else 
     上述条件都不成立时，执行最后这条程序
fi
````

#### 2.case条件语句

```powershell
case $变量名 in
    "值1")
         程序 ;;
    "值2")
          程序 ;;
    *)
       如果变量的值都不是以上的值，则执行该程序 ;;
ease
如： 
case $cho in
     "1")
      echo "fly to beijing";;
     "2")
      echo "fly to nanjing";;
     "3")
      echo "fly to shanghai";;
     *)
     echo "other";;
esac

```

#### 3.for循环

```powershell
for 变量 in 值1 值2 值3 ...
    do
        程序
    done
如：
for i in 1 2 3 4
    do
      echo $i
    done
```

````powershell
for (( 初始值；循环控制条件；变量变化))
     do
        程序
     done
如：
s=0
for (( i=1;i<=100;i++ ))
      do
         s=$(( $s+$i ))
      done
echo $s

````

```powershell
#例：批量添用户
#用户输入用户名
read -p "please input user name: " name
#用户输入要添加用户的数量
read -p "please input number of users: " num
#用户的密码
read -p "please input the pass: " pass
#判断三个变量不为空
if [ ! -z "$name" -a ! -z "$num" -a ! -z "$pass" ]
    then 
    #将num中的数字都替换成空，如果在全部替换，则代表num是一个数字
    y=$(echo $num | sed 's/[0-9]//g' )
    #判断y是否为空，为空则说明num是一个数字
    if [ -z "$y" ]
       then
       for (( i=1;i<=$num;i++ ))
           do
           #添加用户并修改密码
             /usr/sbin/useradd $name$i &>/dev/null
             echo $pass | /usr/bin/passwd --stdin $name$i &>/dev/null
             #强制用户登录后修改密码
             chage -d 0 $name$i &>/dev/null
           done
     fi
fi
```

```powershell
#例：批量删除用户
#!/bin/bash
#在/etc/passwd下截取用户名
name=$( cat /etc/passwd | grep "/bin/bash" | grep -v "root" | cut -d ":" -f 1 )
for i in $name
   do
     userdel -r $i &>/dev/null
   done
```

#### 4.while循环

````powershell
while [ 条件判断 ]
   do
   done
   
例：求100的累加
#!/bin/bash
i=1
s=0
while [ $i -le 100 ]
  do
    s=$(( $s+$i ))
    i=$(( $i+1 ))
  done
echo "s: "$s
````

#### 5.until循环

```powershell
until [ 条件判断 ]
   do
   done
   
例：求100的累加
#!/bin/bash
i=1
s=0
until [ $i -gt 100 ]
    do
     s=$(( $s+$i ))
     i=$(( $i+1 ))
    done
echo $s
```

#### 6.exit,break,continue流程控制语句

```powershell
# exit 退出程序并返回一个错误值
read -t 15 -p "please input a num: " num
y=$( echo $num | sed 's/[0-9]//g' )
if [ -n "$y" ]
   then 
     echo "num is error"
     exit 112 
else 
    echo $num 
fi
```

## 2.启动引导与修复

### 1.系统运行级别

| 运行级别 |                         含义                         |
| :------: | :--------------------------------------------------: |
|    0     |                         关机                         |
|    1     | 单用户模式，相当 于Window安全模式，主要用于系统 修复 |
|    2     |                多用户状态没有网络服务                |
|    3     |                 多用户状态有网络服务                 |
|    4     |                 系统未使用保留给用户                 |
|    5     |                       图形界面                       |
|    6     |                       系统重启                       |

> 在Linux系统中可以使用 runlevel 命令查看当前系统运行级别, init 数字 进入对应的运行级别，修改/etc/inittab来修改系统 默认运行级别,如下图：

````powershell
# Default runlevel. The runlevels used are:
#   0 - halt (Do NOT set initdefault to this)
#   1 - Single user mode
#   2 - Multiuser, without NFS (The same as 3, if you do not have networking)
#   3 - Full multiuser mode
#   4 - unused
#   5 - X11
#   6 - reboot (Do NOT set initdefault to this)
id:5:initdefault:
````

> /etc/rc.d/rc.local文件 这个配置文件会在用户登陆之前读取，这个文件中写入什么命令，在每次系统启动时都会执行一次。也就是说，如果有任何需要在系统启动时就要加载的工作，只需要写入/etc/rc.d/rc.local这个配置即可。它有一个软链接，修改哪一个都行。

```powershell
[root@localhost ~]# ll /etc/rc.local
lrwxrwxrwx. 1 root root 13 9月   1 03:06 /etc/rc.local -> rc.d/rc.local
```

### 2.启动引导程序

> 启动引导程序在/boot/grub目录下，作用：1.加载操作系统 的内核；2.拥有一个可以让用户选择的菜单来选择到底启动哪个系统；3.可以调用其他的启动引导程序，来实现多系统引导。
>
> vi /etc/grub.conf  如下：

```powershell
#boot=/dev/sda
#默认启动第一个系统 
default=0
#等待时间5秒钟 0:不会等待，-1：一直等待输入
timeout=5
#背景图像
splashimage=(hd0,0)/grub/splash.xpm.gz
#隐藏界面
hiddenmenu
#标题
title CentOS 6 (2.6.32-642.el6.x86_64)
        #启动目录的位置
        root (hd0,0)
        #内核加载的选项
        kernel /vmlinuz-2.6.32-642.el6.x86_64 ro root=UUID=40605f57-add1-48ed-b118-d59630e38943 rd_NO_LUKS  KEYBOARDTYPE=pc KEYTABLE=us rd_NO_MD LANG=zh_CN.UTF-8 rd_NO_LVM rd_NO_DM rhgb quiet
        #内核系统镜像文件位置
        initrd /initramfs-2.6.32-642.el6.x86_64.img
```

### 3.CentOS6.x启动管理

#### 3.1 initramfs 内存文件系统 

> CentOS6.x中使用initramfs内存文件系统取代了CentOS 5.x中的initrd RAM Disk。他们的作用类似，可以通过启动引导程序加载到内存中，然后加载启动过程中所需要的内核模块，比如USB，SATA,SCSI硬盘的驱动和LVM,RAID文件系统的驱动。

![内存](C:\Users\Administrator\Desktop\linux\images\内存.JPG)

#### 3.2  /etc/init/rcS.conf配置文件

> 主要功能有两个：
>
> * 先调用/etc/rc.d/rc.sysinit，然后由 /etc/rc.d/rc.sysinit 配置文件进行Linux系统初始化。
> * 再调用 /etc/inittab,然后由  /etc/inittab配置文件确定系统的默认运行级别

![启动顺序](C:\Users\Administrator\Desktop\linux\images\启动顺序.JPG)

### 4. 启动引导程序 Grub

### 4.1 grub中分区表示方式

![分区表](C:\Users\Administrator\Desktop\linux\images\分区表.JPG)

#### 4.2 grub配置文件

>**vi /boot/grub/grub.conf**

![grub](C:\Users\Administrator\Desktop\linux\images\grub.JPG)

>  vi /boot/grub/grub.conf
>
> * default=0 : 默认启动第一个系统 
> * timeout=5 ： 等待时间，默认5秒
> *  splashimage : 指定grub启动时背景图像文件的保存位置
> * hiddenmenu : 启动时隐藏菜单

## 3.服务管理

### 1.服务的分类 

![捕获](C:\Users\Administrator\Desktop\linux\images\捕获.JPG)

### 2.RPM包默认安装有服务

- 独立的服务：就是独立启动的意思，这类服务可以自动启动，而不用依赖其他的管理服务。不依赖其他管理服务，那么当客户端请求访问时，独立的服务响应请求更快速。Linux中目前大多数服务都是独立的服务，比如apapche,ftp等。

  > 独立服务的启动(当前系统下生效)：
  >
  >       1./etc/rc.d/init.d/htttp start  (推荐) 以绝对路径的形式进入安装包开启启动命令
  >
  > 2.  service httpd start   以service +服务名+start
  >
  > 独立服务的自启动(下次开机后生效)：
  >
  > 1. chkconfig --level 2345 httpd lon | off 
  > 2. vi /etc/rc.local 文件中追加 启动命令 (推荐)
  > 3. ntsysv

- 基于 xinetd 服务：这种服务就不能独立启动了，而是要依靠管理服务来调用这种服务。这个负责管理的服务就是 xinetd服务，xinetd服务是系统的超级守护进程。xinetd服务的作用就是管理不能独立启动的服务，当有客户端请求时，先请求xinetd服务，由xinetd服务去唤醒相对应的服务。当客户端请求结束后，被唤醒的服务会关闭并释放资源。这样做的好处是只需要持续启动xinetd服务，而其他基于xinetd的服务只有在需要时才启动，不会占用过多的服务器资源。但这种服务由于是有客户端请求时才会被唤醒，所以相应时间相对较慢。

>基于 xinetd 的启动
>
>1. vi /etc/xinetd.d/服务名 ；将disable改为no；service xinetd restart
>
>基于 xinetd的自启动
>
>      1.chkconfig  服务名 on 
>
>      2.ntsysv

### 3.源码包安装的服务管理

> 启动：
>
> ​      源码安装包的绝对路径启动：如开启httpd: /usr/local/apache2/bin/apachect1 start|stop|retstart
>
> 自启动：
>
> ​     vi /etc/rc.local 文件中追加 启动命令 :/usr/local/apache2/bin/apachect1 start

#### 1.让源码包服务被服务管理命令识别(不推荐使用)

> 默认情况下源码包安装的软件，是不被service，chkconfig，ntsysv命令识别的

```powershell
#service命令其实只是在/etc/init.d目录中查找是否有服务的启动脚本，所以我们只需要做个软链接把源码
#包的启动脚本链接到/etc/init.d/目录中，就能被service命令管理了
ln -s /usr/local/apache2/bin/apachect1 /etc/init.d/apache
```

![启动](C:\Users\Administrator\Desktop\linux\images\启动.JPG)

## 4.系统管理

### 1.进程管理

#### 1.进程的查看

````powershell
#查询正在运行的进程
ps aux 或者  ps -le
````

```powershell
#动态查看进程运行状态,按 q 退出
top 
#图下画红圈的位置含义：系统在之前1分钟，5分钟，15分钟的平均负载
#cpu的空闲百分比
#内存和swap的空间大小
```

![top](C:\Users\Administrator\Desktop\linux\images\top.JPG)

````powershell
#查询指定PID的进程状态
top -p PID
#将top命令的结果重定向到文件当中
top -b -n 1 > /root/top.log
````

````powershell
pstree 命令
-p:显示进程的PID
-u:显示进程的所属用户
````

#### 2.杀死进程 

````powershell
kill -1 IPD  重启该进程
kill -9 IPD  立即关闭该进程 
````

````powershell
#杀死一类进程 
killall [选项] [信号 -1/-9] 进程名
-i:交互式，询问是否要杀死某个进程 
-I:忽略进程名的大小写

pkill [选项] [信号] 进程名
#强制杀死从pts/1虚拟终端登录的进程
pkill -9 -t pts/1
````

### 2.工作管理

#### 1.工作管理简介

>* 前台是指当前可以操控和执行命令的这个操作环境，后台是指工作可以自行运行，但是不能直接用Ctrl+C来终止它，只能使用fg/bg来调用工作
>* 当前的登录终端，只能管理当前终端的工作，而不能管理其他登录终端的工作。如tty1登录的终端是不能管理tty2终端中的工作
>* 放入后台的命令必须可以持续运行一段时间，这样我们才能扑捉和操作这个工作。如果把ls命令放入后台执行，它会很快就会执行完成，我们很难操作它
>* 放入后台的命令不能和前台用户有交互或需要前台输入，否则放入后台只能暂停，而不能执行。比如vi,top命令.

#### 2.将命令运行放入后台

```powershell
# 命令 &
tar -zcvf /etc/etc.tar.gz /etc & 

#在命令执行过程中按 Ctrl + Z ，注意这样操作命令在后台是暂停状态
```

![ps](C:\Users\Administrator\Desktop\linux\images\ps.JPG)

![ps2](C:\Users\Administrator\Desktop\linux\images\ps2.JPG)



#### 3.查看后台运行状态

````powershell
jobs 
-l: 显示工作的PID
````

![ps3](C:\Users\Administrator\Desktop\linux\images\ps3.JPG)

#### 4.将后台暂停的工作恢复到前台执行

```powershell
fg 工作号,不是PID号
```

#### 5.将后台暂停的工作恢复到后台执行

````powershell
bg 工作号
````

#### 6.将后台命令脱离当前登录终端运动

> 我们已经知道把命令放入后台，只能在当前登录的终端执行。如果我是远程管理的服务器，在远程终端中执行了后台命令，当我退出登录后，这个后台是不能继续执行的。如果解决呢？

* 把需要执行的后台命令写入  /etc/rc.local文件，让系统在启动时执行这个后台程序。
* 使用系统定时任务
* 使用 nohup 命令

````powershell
nohup [命令] &
如
nohup tar -zcvf /etc/etc.tar.gz /etc &
````

#### 7.系统资源查看

````powershell
#查看监控系统资源
vmstat  刷新延迟  刷新次数
#每一秒钟刷新1次，共刷新3次
vmstat 1 3

#显示开机时内核检测信息
dmesg | grep cpu 查看cpu信息
dmesg | grep eth0 查看第一块网卡的信息

#查看内存使用状态
free -h 

#查看cpu信息
cat /proc/cpufino

#查看系统中已经登录的用户
who

#显示系统的启动时间和平均负载，也就是top命令的第一行内容
uptime

#查看系统与内核相关信息
uname [选项]
-a:查看系统所有相关信息
-r:查看内核版本
-s:查看内核名称

#查询当前Linux系统的发行版本
lsb_releas -a
````

### 3.系统定时任务

#### 3.1 系统一次性执行任务(at)

> at 一次性执行定时任务，at命令要想正确执行，需要atd服务的支持。atd服务是独立的服务，所以启动命令是： 

```powershell
service atd start
```

> at 的访问控制依赖的是 /etc/at.allow文件（白名单）和/etc/deny文件（黑名单）。
>
> /etc/at.allow 只有写入这个文件的用户才可以使用at命令，其他用户不可以使用，这时/etc/at.deny文件会被忽略，也就是/etc/at.allow文件优先级更高。
>
> 如果系统中没有/etc/at.allow文件，只有/etc/at.deny文件，那么写拉/etc/at.deny文件中的用户不能使用at
>
> 命令，其他用户可以使用。不过这个文件对root用户不生效。
>
> 如果系统中这两个文件都不存在，那么只有root用户才可以使用at命令。
>
> 系统中默认只有/etc/at.deny文件，而且这个是个空文件，也就是所有用户都可以使用这个命令。
>
> /etc/at.allow文件的权限更高，如果 /etc/at.allow文件存在，则/etc/at.deny文件失效。

![at](C:\Users\Administrator\Desktop\linux\images\at.JPG)

```powershell
#查询当前服务器上的at工作
atq
#删除指定的at任务
atrm [工作号]
#查询具体的at任务
at -c [工作号]
```

#### 3.2 循环执行定时任务(crontab)

> crontab 命令是需要 crond 服务支持的，crond服务同样是独立的服务，所以启动和自启动方法如下：
>
> crontab  命令和 at 命令类似，也是通过/etc/cron.allow和/etc/cron.deny文件来限制用户是否可以使用crontab命令。 而且原则也类似，/etc/cron.allow文件优先级比/etc/cron.deny高，Linux中默认只有/etc/ cron.deny文件。

```powershell
#启动crond服务 
service crond restart

#设置自启动crond服务
chkconfig crond on
```

> 用户的crontab设置：每个用户都可以实现自己的crontab定时任务，只要是使用这个身份执行 crontab -e 命令即可，当然这个用户不能加入 /etc/cron.deny 文件中。

```powershell
crontab [选项]
-e:编辑crontab定时任务
-l:查询crontab任务
-r:删除当前用户所有的crontab任务，如果只想删除单个任务，编辑 crontab -e 删除任务代码
-u 用户名： 修改或删除其他用户的crontab任务，只有root可用
```

|   项目   |         含义         |        范围        |
| :------: | :------------------: | :----------------: |
| 第一个 * | 一小时当中的第几分钟 |        0-59        |
| 第二个 * | 一天当中的第几个小时 |        0-23        |
| 第三个 * |  一个月当中的第几天  |        1-31        |
| 第四个 * |  一年当中的第几个月  |        1-12        |
| 第五个 * |   一周当中的星期几   | 0-7(0,7都是星期天) |

##### 3.2.1 一些特殊符号

| 特殊符号 |                             含义                             |
| :------: | :----------------------------------------------------------: |
|    *     | 代表任何时间。比如第一个 " * "就代表一小时中每分钟都执行一次的意思。 |
|    ,     | 代表不连续的时间。比如 "0 8, 12,16 * * * 命令"，就代表在每天的8点0分，12点0分，16点0分都执行一次命令 |
|    -     | 代表不连续的时间。比如 " 0 5 * * 1-6 "命令，代表在周一到周六的凌晨5点0分执行命令 |
|   */n    | 代表每个多久执行一次，比如 "*/10 * * * * 命令"，代表每隔10分钟就执行一次。 |

##### 3.2.2 举例

|     时间     |                             含义                             |
| :----------: | :----------------------------------------------------------: |
| 45 22 * * *  |                      在22点45分执行命令                      |
|  0 17 * * 1  |                     每周一的17点0分执行                      |
| 0 5 1,15 * * |               每个月的1号和15号凌晨5点0分执行                |
| 40 4 * * 1-5 |               周一到周五的每天凌晨4点40分执行                |
| */10 4 * * * |                 每天凌晨4点，每隔10分钟执行                  |
| 0 0 1,15 * 1 | 每月1号，15号，每周一的0点0分执行，注意：星期几和几号最好不要同时出现。 |

> crontab的注意事项
>
> 在编写 crontab定时任务进，需要注意：
>
> ​    1.五个选项都不能为空，必须填写。如果不确定使用 * 代表任意时间
>
> ​    2.crontab定时任务，最小有效时间是分钟，最大时间范围是月。像2018年某时执行，3分30秒这样的时间都不能识别。
>
>    ​    3.在定义时间时，日期和星期最好不要在一条定时任务中出现，因为它们都是以天作为单位
>
>    ​    4.在定时任务中，写命令最好使用绝对路径 

##### 3.2.3 编写定时任务配置文件

> crontab -e 是每个用户执行的命令，也就是说不同的用户身份可以执行自己的定时任务。可以是有些定时任务需要系统执行，这时我们就需要编辑 /etc/crontab这个配置文件了，当然，并不是写入 /etc/crontab配置文件中的定时任务执行时，不需要用户身份，而是 crontab -e  命令定义定时任务时，默认用户就是当前登陆的用户。而修改 /etc/crontab 配置文件是，定时任务的执行身份是需要手工指定的。这样定时任务的执行会更加灵活。 vi/etc/crontab  如下图：

![任务](C:\Users\Administrator\Desktop\linux\images\任务.JPG)

>在以前的 CentOS 5.x版本中，/etc/crontab文件会使用 run-parts 脚本执行 /etc/cron.{daily,weekly,monthly}目录中的所有文件。这个run-parts其实是一个shell脚本，保存在/usr/bin/run-parts,它的作用就是把其后面跟随的目录中的所有可执行文件依次执行。也就是说，如果我们想让某个脚本在每一天执行一次，可以不用手工去写定时 任务，而只需要给这个脚本赋予执行权限，并把它放入/etc/cron.daily/目录中。这样这个脚本就会在每天的凌晨4点02分自动执行了。
>
>在CentOS 6.x版本中，/etc/crontab文件中不再有相关的段落，但在旧版本的crontab配置文件中，是通过run-parts脚本来调用cron.{daily,weeklk,monthly}目录 ，定时执行这些目录中的脚本。在当前系统中，为了避免cron和anacron之间的冲突。只要anacron已经安装，就使用anacron来执行这些目录中的脚本。
>
>对于用户来说，不需要知道这个定时任务到底是由哪个程序调用的。我们需要知道的事情是如何使用系统的crontab设置。这个新老版本的CentOS没有区别，配置方法都有两种：
>
> 1:把需要定时执行的工作写成脚本程序，并赋予执行权限，然后直接把这个脚本复制到/etc/cron.{daily,weekly,monthly}目录中的任意一个。比如我需要让某个脚本每周执行一次，那么就把这个脚本复制到/etc/cron.weekly目录中，这样这个脚本就会每周自动执行一次，具体的执行时间参考anacron配置。
>
>  2:修改/etc/crontab这个配置文件，自己加入需要执行的定时任务，不过需要注意指定脚本的执行者身份
>
>如下图：

![任务2](C:\Users\Administrator\Desktop\linux\images\任务2.JPG)

##### 3.3.3 anacron

>如果刚好在关机的时间段之内有系统定时任务(cron)要执行，那么这些定时任务是不会执行的。也就是说，假设我们需要在凌晨5点05分执行系统的日志备份，但我们的Linux服务器不是24小时开机的，在晚上需要关机，白天上班才会开机，这个定时任务的执行时间我们服务器刚好没有开机，那么这个任务就不会执行了。anacron就是用来解决这个问题的。
>
>anacron会使用一天，一周，一个月作为检测周期，用来判断是否有定时任务在关机之后没有执行，如果有这样的任务，anacron就会在特定的时间重新执行这些定时任务。
>
>那么anacron是如何判断这些定时任务已经超过了执行时间呢？在系统的/var/spool/anacron目录音中存在cron.{daily,weekly,monthly}文件，这些文件中都保存着anacron上次执行时间。anacron会去读取这些文件中的时间，然后和当前时间做比较，如果两个时间的差值超过了anacron的指定时间差值(一般是1天，一周和一个月)，就说明有定时任务漏掉了没有被执行，这时anacron会介入而执行这个漏掉执行，从而保证在关机时没有被执行的定时任务不会被漏掉。
>
>在现在的CentOS 6.x中，/etc/cron.{daily,weekly,monthly}目录中的定时任务程序只会被anacron调用，从而保证这些定时任务只会在每天，每周或每月被定时执行一次，而不会重复执行。

![任务3](C:\Users\Administrator\Desktop\linux\images\任务3.JPG)

> 在CentOS 6.x中 anacron不再是单独的服务，而变成了系统命令。也就是说我们不再可以用 "service anacron restart"命令来管理anacron服务了。而是需要使用anacron 命令来管理anacron工作。

```powershell
anacron [选项] [工作名]
-s :开始anacron工作，依据/etc/anacrontab文件中的设定的延迟时间执行
-n :立即执行/etc/anacrontab 中所有的工作，忽略所有的延迟时间
-u :更新/var/spool/anacron.{daily,weekly,monthly}文件中的时间戳，但不执行任务工作

工作名 : 是依据/etc/anacrontab文件中定义的工作名
```

> 在我们当前的Linux中，其实不需要执行任何anacron命令，只需要配置好/etc/anacrontab 文件，系统 就会依赖这个文件中的设定来通过anacron执行定时任务，/etc/anacrontab如下图：

![任务4](C:\Users\Administrator\Desktop\linux\images\任务4.JPG)

> RANDOM_DELAY=45 : 最大随机延迟时间
>
> START_HOURS_RANGE=3-22 : anacron的执行时间范围是 3点到22点
>
> 我们用cron.daily工作来说明下/etc/anacrontab的执行过程：
>
>    1:首先读取/var/spool/anacron.daily中的上一次anacron执行时间
>
>    2:和当前时间比较，如果两个时间的差值超过1天，就执行 cron.daily工作
>
>    3:执行这个工作只能在03：00 - 22：00之间
>
>    4:执行工作时强制延迟时间为5分钟，再随机延迟0 - 45 分钟时间
>
>    5:使用 nice 命令指定默认优先级，再使用 run-parts 脚本执行 /etc/cron.daily目录中的所有可执行文件。
>
> /etc/cron.{daily,weekly,monthly}中的脚本，在当前的Linux中是被anacron调用的，不再依靠cron服务。不过anacron不用设置多余的配置，我们只需要把需要定时执行的脚本放入/etc/cron.{daily,weekly,monthly}目录中，就会在每天，每周或每月执行，而且也不再需要启动anacron服务了。我们如果做修改的话，只用修改/etc/anacrontab配置文件即可。

## 5.日志管理

### 5.1 系统中常见日志

|     日志文件     |                             说明                             |
| :--------------: | :----------------------------------------------------------: |
|  /var/log/cron   |                  记录了系统定时任务相关日志                  |
|  /var/log/cups/  |                       记录打印信息日志                       |
|  /var/log/dmesg  |               记录了系统在开机时内核自检的信息               |
|  /var/log/btmp   | 记录错误登陆的日志，是二进制文件，不能用vi，而要使用lastb命令查看 |
| /var/log/lastlog | 记录系统中所有用户最后一次登陆时间的日志，是二进制文件，用lastlog命令查看 |
| /var/log/mailog  |                         记录邮件信息                         |
| /var/log/message |   记录系统重要信息的日志。记录Linux系统的绝大多数重要信息    |
| /var/log/secure  | 记录验证和授权方面的信息，只要涉及到帐户和密码的程序都会记录 |
|  /var/log/wtmp   | 永久记录所有用户的登陆，注销信息，同时记录系统的启动，重启，关机，二进制文件，用last命令查看 |
|  /var/run/utmp   | 记录当前已经登陆的用户信息。这个文件会随着用户的登录和注销而不断变化，只记录当前登陆用户的信息，使用w,who,users等命令查询 |

> 除了系统默认的日志之外，采用RPM方式安装的系统服务也会默认把日志记录放在/var/log/目录中(源码安装的服务日志是在源码包指定目录中)。不过这些日志不是由rsyslogd服务来管理的。而是各个服务使用自己的日志管理文档来记录自身日志。

|    日志文件     |                说明                 |
| :-------------: | :---------------------------------: |
| /var/log/httpd/ | RPM包安装的apache服务的默认日志目录 |
| /var/log/mail/  |  RPM包安装的邮件服务的额外日志目录  |
| /var/log/samba/ |    RPM包安装的samba服务日志目录     |
| /var/log/sssd/  |        守护进程安全服务目录         |

### 5.2 日志服务 rsyslogd

> 日志文件格式：
>
>      * 事件产生的时间
>      * 发生事件的服务器主机名
>      * 产生事件的服务名或程序名
>      * 事件的具体信息

### 5.3 rsyslogd服务的配置文件

> vi /etc/rsyslog.conf 配置文件 如下图：

![日志](C:\Users\Administrator\Desktop\linux\images\日志.JPG)

> 服务名称，连接符号，日志登记，日志记录位置

### 5.4 连接符号

> 日志服务连接日志等级的格式是：

````powershell
日志服务 [连接符号] 日志登记                 日志记录位置
````

> * " . "代表只要比后面的等级高的（包含该等级）日志都记录下来。比如："cron.info" 代表cron服务产生的日志，只要日志等级大于等于info级别，就记录.
> * “  .= ”代表只记录所需等级日志，其他等级的都不记录。
> *  " .! "代表不等于，也就是除了该等级的日志外，其他等级日志都记录。

### 5.5 日志等级

![日志2](C:\Users\Administrator\Desktop\linux\images\日志2.JPG)

### 5.6 日志轮替

#### 5.6.1 日志文件的命名规则

> 日志轮替的最主要作用就是把旧的日志文件移动并改名，同时建立新的空日志文件，当旧日志文件超出保存的范围后，就会进行删除。那么旧的日志文件改名之后。如何命令名？主要依靠 /etc/logrotate.conf 配置文件中的 "dateext" 参数。
>
> 如果配置文件中拥有“dateext”参数，那么日志会用日期来作为日志文件的后缀，例如"secure-20180605".这样的话日志文件名不会重叠，所以也就不需要日志文件的改名，只需要保存指定的日志个数，删除多余的日志文件即可。
>
> 如果配置文件中没有"dateext"参数，那么日志文件就需要进行改名了。当第一次进行日志轮替时，当前的secure日志会自动改名为secure.1，然后新建secure日志，用来保存新的日志，当第二次进行日志轮替进，secure.1日志会自动 改名为secure.2，当前的secure日志会自动改名为secure.1，然后也会新建secure日志，用来保存新的日志，以此类推。

#### 5.6.2 日志轮替配置文件

> vi /etc/logrotate.conf

```powershell
# see "man logrotate" for details
# rotate log files weekly
#每周对日志文件进行一次轮替
weekly

# keep 4 weeks worth of backlogs
#保存4个日志文件，也就是说如果进行了5次日志轮替，就会删除第一个备份日志
rotate 4

# create new (empty) log files after rotating old ones
#在日志轮替时，自动修建新的日志文件
create

# use date as a suffix of the rotated file
#使用日期作为日志轮替文件的后缀
dateext

# uncomment this if you want your log files compressed
#日志文件是否压缩。如果取消注解，则日志会在转储的同时进行压缩
#compress

#以上是日志配置的默认配置，如果需要轮替的日志没有设定独立的参数，那么都会遵守以上参数
#如果轮替日志配置了独立参数，那么独立参数优先级更高

# RPM packages drop log rotation information into this directory
#包含/ext/logrotate.d/目录中所有子配置文件。也就是说会把这个目录中的所有子配置
#文件读进来，进行日志轮替
include /etc/logrotate.d

# no packages own wtmp and btmp -- we'll rotate them here
#以下两个轮替日志有自己的独立参数，如果和默认的参数冲突，则独立参数生效
/var/log/wtmp {
    monthly
    create 0664 root utmp
        minsize 1M
    rotate 1
    #每月对日志进行一次轮替
    #创建的新日志文件，权限是0664，所有者是root，所属者是utmp
    #日志文件最小轮替大小是1MB，也就是日志一定要超过1MB才会轮替，否则就算时间到了一朋，也不轮替
    #仅保留一个日志备份，也就是只有wtmp和wtmp.1日志保留而已
}

/var/log/btmp {
    missingok#如果日志不存在，则忽略该日志的警告信息
    monthly
    create 0600 root utmp
    rotate 1
}
}
```

#### 5.6.3 logrotate配置文件的主要参数

|          参数           |                           参数说明                           |
| :---------------------: | :----------------------------------------------------------: |
|          daily          |                     日志的轮替周期是每天                     |
|         weekly          |                           每周轮替                           |
|         monthly         |                           每月轮替                           |
|       rotate 数字       |              保留的日志文件的个数，0指没有备份               |
|        compress         |                 日志轮替时，旧的日志进行压缩                 |
| create mode owner group | 建立新日志，同时指定新的日志的权限，所有者，所属组。如 create 0600 root root |
|      mail address       |      当日志轮替时，输出内容通过邮件发送到指定的邮件地址      |
|        missingok        |            如果日志不存在，则忽略该日志的警告信息            |
|       notifempty        |              如果日志为空文件，则不进行日志轮替              |
|         minsize         | 日志轮替的最小值，只有日志一定达到这个最小值才会轮替，否则 就算时间达到也不轮替 |
|          size           |   日志只有大于指定大小才会进行日志轮替，而不是按照时间轮替   |
|         dateext         |                使用日期作为日志轮替文件的后缀                |
|      sharedscripts      |                在此关键字之后的脚本只执行一次                |
|   prerotate/endscript   |                   在日志轮替执行前执行脚本                   |
|  postrotate/endscript   |                   在日志轮替执行后执行脚本                   |

#### 5.6.4 将自己的日志加入日志轮替

>* 在/etc/logrotate.conf配置文件中写入该日志的轮替策略，从而把日志加入轮替
>* 在/etc/logrotate.d/目录中创建该日志的轮替文件，在该轮替文件中写入正确的轮替策略，因为该目录中的文件都会被"include"到主配置中，所以也可以把日志加入轮替。推荐使用第二种。

```powershell
logrotate [选项] /etc/logrotate.conf
-v: 显示日志轮替过程
-f: 强制进行日志轮替。不管日志轮替的条件是否已经符合，强制配置文件中所有的日志进行轮替
```

```powershell
# RPM packages drop log rotation information into this directory
include /etc/logrotate.d
```

> 系统会自动将RPM包安装的软件中的日志进行轮替，但源码包安装的软件默认是不会进行日志轮替，这时就需要手工将源码包中的日志加入到 /etc/logrotate.conf中轮替,如下将源码包安装apache日志加入到轮替中。

```powershell
vi /etc/logrotate.conf
#将源码包的apache日志加入轮替
/usr/local/apache/logs/access_log{
  daily
  create
  rotate 30
}
```

## 6. 备份与恢复

### 6.1 备份概念

> 完全备份：将数据完全备份
>
> 增量备份：每次备份都备份上次新增的数据
>
> 差异备份：

### 6.2 dump 和 resotre 命令

#### 6.2.1 dump备份命令

```powershell
dump [选项] 备份后的文件名  原文件或目录
选项：
-level:0-9个备份级别
-f:指定备份后的文件名
-u:备份成功后把备份时间记录在/etc/dumpdates文件中
-v:显示备份过程中更多的输出信息
-j:将文件压缩成.bz2格式
-W:允许被dump的分区的备份等级及备份时间
```

```powershell
#查询整个分区的备份时间及备份级别
dump -W
```

```powershell
#查看备份时间文件
cat /etc/dumpdates
```

#### 6.2.2 resotre 恢复命令

```powershell
restore [模式选项] [选项]
模式选项：不能混用
-C :比较备份数据和实际数据的变化
-i :进入交互模式，手工选择需要恢复的文件
-t :查看模式，用于查看备份文件中拥有哪些数据
-r :还原模式，用于数据还原

选项：
-f :指定备份的文件名
```

