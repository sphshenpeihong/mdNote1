# 一、Linux

## 1、Linux常识

### 1.1、Linux诞生历史

1.ken tompson是用B语言在1971年开发出Unix操作系统 然后和dennis共同创造C语言，后面用C语言重写了Unix(开源内核)，然后很多产商是用Unix内核二开出收费的操作系统 ，人家用不起，所以stallman是用Unix内核开发出Linux操作系统(免费开源) 所以stallman提出GNU计划（人机交互）  最后。 **Linux是linus在Unix操作系统进行二次开发**

//**Linux操作系统诞生历史 (Linux指的是Linux内核，发行版才是可以使用的操作系统，平常说linux操作系统指的是某个发行版)**



### 1.2、人机交互

人与硬件交互（GNU计划）：人->应用软件(浏览器、音乐播放器)->shell层->操作系统(系统软件)->硬件(内核)



### 1.3、Linux与Windows比较

windows收费，相比linux不稳定(三天两头打补丁、安全更新)，linux基本源代码没问题，程序跑半年都没事，半年重启一次即可。windows不能定制化开发(没源码)



### 1.4、基础知识概念

**Linux有很多发行版**：ubuntu、centOS、蝶变、红帽、Suse、红旗Linux 这些都是系统软件，都是基于Linux内核去开发的(硬件)

**常用的操作系统(系统软件)**：linux、windows、IOS、车载系统(嵌入式)、安卓、Mac OS

**挂载：**系统盘与外部硬盘(移动硬盘、U盘)形成映射。使用挂载命令，才能将U盘文件系统挂载到此目录。 //若不挂载，默认外部硬盘插进去时，目录会出现在/dev/目录下（此时访问此目录指挥提供给你此设备的一些基本信息，比如容量）。



### 1.5、安装虚拟机

目的：安装虚拟机，导入centos镜像

步骤：

新建虚拟机时，网络连接一般选择NAT模式(使用新的网络的地址 不会产生IP冲突)

![image-20201222192546216](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222192547.png)

在服务器linux系统下载并安装vmware-tools这个小软件(好处如下)  虚拟机(虚拟机->可以安装)
1.可以直接粘贴命令在windows和centos系统之间
2.可以设置windows和centos的共享文件夹(windows新建一个共享文件夹->虚拟机->设置->选项->共享文件夹enable->添加windows新建的共享文件夹，，最后共享成功，linxu这个共享文件夹存放于 /mnt/hgfs/目录下)



### 1.6、su和sudo的区别

**共同点：**都是root用户权限
**不同点：**su root(只是临时获得root权限，无root的工作环境)，但是sudo root（既获得root权限，又有root的工作环境）



## 2、Linux基础

### 2.1、Linux目录结构

linux世界中，一切皆文件(即是是外部设备，也会在/dev中映射一个文件 对硬件进行管理)

![image-20201222192602865](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222192603.png)

**/bin：这个目录存放着最经常使用的命令**
**/boot：这里存放的是启动Linux时使用的一些核心文件，包括一些连接文件以及镜像文件**
/dev：该目录下存放的是Linux的外部设备
/etc：这个目录用来存放所有的系统管理所需要的配置文件和子目录
**/home：用户的主目录，在Linux中，每个用户都有一个自己的目录，一般该目录名是以用户的账号命名的**
/lib：这个目录里存放着系统最基本的动态连接共享库
/media：linux系统会自动识别一些设备，例如U盘、光驱等等，当识别后，linux会把识别的设备挂在到这个目录下。
**/mnt（共享文件夹）**：系统提供该目录是为了让用户临时挂在别的文件系统的，我们可以将光驱挂在在/mnt/上，然后进入该目录就可以查看光驱里的内容了
**/opt（安装软件存放路径 比如安装Mysql）：这里给主机额外安装软件所摆放的目录。比如安装一个MYSQL数据库就可以安装在这个目录下**
/proc：这个目录是一个虚拟的目录，它是系统内存的映射，我们可以通过直接访问这个目录来获取系统信息
**/root：该目录是为系统管理员，也称作超级权限者的用户主目录**
/sbin：s就是Super User的意思，这里存放的是系统管理员使用的系统管理程序
/selinux：安全机制，类似于防火墙的
/srv：该目录存放一些服务启动之后需要提取的数据
/tmp：这个目录是用来存放一些临时文件的
**/usr：这个是一个非常重要的目录，用户的很多应用程序和文件都放在这个目录下，类似于windows下的program files目录**
/usr/bin：系统用户使用的应用程序
/usr/sbin：超级用户使用的比较高级的管理程序和系统守护程序
/usr/src：内核源代码默认的放置目录
/var：这个目录存放着不断扩充这的东西，习惯修改的文件就放这个目录，比如日志文件



#### 2.1.1、hosts文件与DNS服务器

**1、linux中的hosts文件的位置？**

/etc/hosts



**2、查询本机DNS的配置**

cat /etc/resolv.conf





### 2.2、常用命令

#### 2.2.1、常用基本命令

**ls：**仅罗列出当前文件名或目录名
**ll(别名是：ls -l)：**罗列出当前文件或目录的详细信息
**ls -la：**查看看隐藏文件
**cat .git/config：**查看文件里面有什么内容
**mkdir：**创建文件夹 （mkdir -p /home/dog/dir1  使用-p选项 后面目录可以创建多级目录）
**rmdir：**删除文件夹
**touch：**新建文件
**rm -rf：**删除文件（也支持删除目录 非空目录）
**vim写完后  保存退出 ：Esc  ： wq**
**cd：**切换目录（只能切换到目录 若要看文件信息 需要使用cat）
**ps -au：**查看进程信息(查看后台进程运行使用ps命令   查看详细进程信息使用au  故为ps -au) 
**kill PID码：**杀死进程  kill -9 21321
pwd：打印当前路径
useradd -d /home/dog1 dog ：新建一个用户dog，并且指定该用户家目录名为dog1（若不指定家目录名 默认为dog）
passwd dog：修改用户密码
ll -h:查看当前目录下的文件具体详细大小以及读写权限



#### 2.2.2、文件目录类

2、cp  test1.java  dir1 //复制文件到文件夹cp -r dir1 dir2 //复制文件夹dir1中的内容递归到dir2  （若已有相同名称问题 使用cp -r dir1 dir2是强制覆盖）  //r是递归的意思

3、mv：移动文件和重命名文件

4、> 和 >>：>就是覆盖 >>是追加  例：ls -l > newFile.txt（将ls -l显示在控制台的内容覆盖到newFile.txt中  cat test.java >> newFile.txt）

**5、echo：**显示后面文本内容到控制台 **echo "I'm coming"** >> file1.txt //echo命令加 >> 可以追加文本数据到某个文件当中   **（echo后面的内容可以显示文本到控制台）**

6、head：head file.txt  默认显示文件前10行   (head -n 5 前5行)

**7、tail ：**tail file.txt 默认显示文件最后10行 **(tail -f file.txt 实时监控**该文件是否有更新变化 **一般用于观看日志是否有更新**)

**8、ln -s 某个目录/文件  快捷方式名**  例：ln -s /root newQuick

9、history ：查看执行过的指令

10、df -h 查看当前文件系统/挂载点的使用情况

11、fdisk -l：查询可挂载的文件系统   

12、mount 文件系统目录 目标挂载目录 ： 手动挂载某个文件系统目录（根目录一定得挂载）  （自动挂载 /etc/fstab文件直接写映射文件系统目录 -> 目标挂载目录即可）

![image-20210222214838610](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210222215353.png)







#### 2.2.3、时间日期类

```txt
1.date   ： date "+%Y"
2.date -s "2019-10-10 22:22:22" //给系统设置一个时间
3.cal 2020 //查看2020的日历
```

#### 2.2.4、搜索查找类

```txt
1.find：find  /root -name hello.txt //按文件名查找 查找root目录下文件名为hello的文件
2.find：find /root -user shen //按用户名查找 查找root目录下是属于用户shen的文件
3.find：find /root -size +20m //按文件大小来查找 查找目录下大小大于20M
4.grep(过滤查找)：cat hello.txt | grep -ni yes //查找hello.txt中是yes的内容 并显示行号和忽略大小写（管道"|"：就是把前面的内容交给后面的命令去处理）
```

#### 2.2.5、压缩与解压缩文件

gzip压缩文件： gzip 文件名  //只能压缩成gz为后缀的压缩文件 （得到压缩文件，但是文件就没了）
gzip解压缩文件： gunzip 文件.gz  //解压缩后缀为.gz的压缩文件（得到解压缩文件，但是压缩文件就没了）
**zip压缩文件：zip -r newPackage.zip /home    //打包home目录下的所有文件递归打zip包到当前目录**
unzip解压文件到指定目录：unzip myPackage.zip -d dir1  //解压myPackage.zip包到当前目录下的dir1目录

unzip解压文件到当前目录（覆盖）：unzip -o vp （vp如果后面有括号的话，需要带个反斜杆）

**tar指令(既能压缩、又能解压)**
tar指令是打包指令，压缩文件后缀为：.tar.gz
**-c：产生.tar打包文件**
-x：解压.tar文件 //这是解压 和楼上相反
-v：显示详细信息
-f：指定压缩后的文件名
-z：打包同时压缩
**1.打包** ：tar [选项] 压缩文件名.tar.gz 要压缩的文件.... (压缩使用-zcvf )//例1：**tar -zcvf** a.tar.gz file1.txt file2.txt  对file1和file2进行打包成a.tar.gz 例2：tar -zcvf myHome.tar.gz /home 对home目录下文件打包成myHome.tar.gz 
**2.解压**：**tar -zxvf** a.tar.gz //解压压缩包a.tar.gz到当前目录
 解压到指定目录：tar -zxvf a.tar.gz -C /opt //解压到/home目录   指定的目录一定得存在 不然报错
3.解压tar.xz文件：tar -xvJf 压缩文件名



### 2.3、vi和vim文本编辑器

三种常用模式
正常模式(进入vim默认的模式 可以按上和下来移动光标)

编辑模式(按i进入编辑模式)

命令行模式(按ESC 然后再加： 就可以输入命令了 wq保存退出 q!强制退出)

![image-20201222192616022](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222192617.png)

进入vim **一般模式**下的一些快捷键
dd：删除当前一行    （2dd：删除当前向下2行）
yy：复制当前一行    （2yy：复制当前向下2行）
p：粘贴
u：撤销操作(相当于Ctrl+Z) 需要在一般模式下才有这个快捷键   //如果我们在编辑模式下输入完想撤销， 那么先ESC 再按u  然后再i 进入编辑模式
G：光标定位到最后一行
gg：光标定位到第一行
/：按/然后输入我们查找的文本 然后按Enter开始查找 按n则是下一个
快速定位：先命令模式 :set nu   然后一般模式下 输入行号 按shift+g快速定位



**命令行模式的一些命令**
set nu：给程序设置行号
set nonu：给程序取消设置行号



### 2.4、开启、重启、用户登录注销

shutdown
    shutdown -h now：现在关机
    shutdown -h 1：一分钟后关机
halt：等价shutdown -h now 关机
reboot：重启
sync：将内存数据同步到磁盘(将未保存数据同步到磁盘  //关机和重启前执行)



### 2.5、用户管理与权限管理

#### 2.5.1、Linux用户管理

**1.添加用户**
useradd -d /home/dog1 dog ：新建一个用户dog，并且指定该用户家目录名为dog1（若不指定家目录名 默认为dog）
passwd dog：修改用户密码
useradd -g wudang zhangwuji：添加用户直接加上指定组(一般家目录和用户名一样)  //一般使用这种方式创建用户

**2.删除用户(1.保留家目录  2.不保留家目录)**
userdel xq ：删除用户 但是会保留家目录(一般执行这条 保留家目录)
userdel -r xq ： 删除用户 同时删除对应家目录

**3.查询用户信息**
id root：超级管理员uid=0
id shen：第一用户 uid=1000

![image-20201222192653504](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222192654.png)

4.切换用户
su - root：切换到超管



**5、用户管理（组的管理）**

1.添加组
groupadd wudang
2.删除组
groupdel wudang
3、修改用户到某个组(从wudang到shaolin)
usermod -g shaolin zhangwuji



**用户和组的存放位置目录**
用户配置信息的目录：/etc/passwd
组配置信息的目录：/etc/group
口令配置信息(用户密码和登录信息 //是加密的 可能看不懂哦)：/etc/shadow

Linux的运行级别

```txt
init 3
0：关机
1：单用户模式(不需要输入密码)
3：多用户有网络
5：图形界面
6：重启
```

案例：linux忘记root超管密码了，现在找回root密码
1.开启启动Linux系统的时候，修改配置告诉linux内核  使用1模式启动 然后进入root 再修改密码



#### 2.5.2、组管理和权限管理

ls -ahl：查看文件(包括所在组 和创建者)
chown 用户名 文件名：chown zhang file1.txt //修改文件创建者
chgrp 组名 文件名:chgrp wudang file1.txt //修改文件所在组



#### 2.5.3、文件权限管理

![image-20201222192706860](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222192707.png)

给目录或者文件修改读、写、执行权限 chmod 777 file1.txt



### 2.6、定时任务调度

crontab(定时任务)：指系统在某个时间执行的特定的命令或程序 //比如凌晨2点执行mysql数据备份 比如定时杀毒
基本语法：crontab [选项] // -e：编辑定时任务 -l：查询crontab任务 -r：删除当前用户所有crontab任务

windows下的脚本语言编程：.bat文件 可以执行windows命令
linux下的脚本语言编程：.sh文件 shell脚本 可以在linux系统下执行操作
shell脚本 写完可以使用crond命令 新增定时任务



## 3、服务器日常操作

### 3.1、高频使用命令

登录到对应的服务器上
然后先使用ps -ef | grep tomcat 查询有多少台tomcat正在工作    //ps -ef 表示查看全格式的全部进程  然后grep过滤查找
然后cd切换到工作的tomcat(具体要先看看linux上的tomcat安装到哪个目录下)，tomcat的日志是存放到logs目录下(按日期存储)。
切换到logs目录下，有个catalina.out目录，使用 
0、下载日志 tail -n 10000 catalina.out > /home/shenpeihong/20200703.log

**1、实时日志：**tail -f catalina.out 即可以输出实时日志  。

**2、输出后10000行日志：**tail -n 10000 catalina.out 则是输出后10000行日志

**3、sed查询某时间段的日志 可结合grep**：sed -n '/2020-04-08 10:01:星号/,/2020-04-08 11:05:星号/p' catalina.out > /home/dwyuanxianxian/shenpeihong/20200408a.log （有一些那个分钟没日志的话，就要更改其它的分钟了，不然没日志）

**4、修改文件夹创建者** chown dwyuanxianxian /home/dwyuanxianxian/shenpeihong

**5、修改文件创建者**：chown dwyuanxianxian /home/dwyuanxianxian/shenpeihong/20200408a.log

**6、跟踪网络走向：**traceroute -4 10.244.78.69 -p 80

**7、清除日志：**echo "" > 日期日志文件.log

**8、查看各目录的使用量：**df -h

**9、登陆数据库：**mysql --login-path=21 （测试环境是wxqyh）

**10、查看服务器运行的进程：**ps -ef|grep tomcat (查看运行的tomcat)  、 ps -ef|grep nginx (查看运行的nginx)

**11、启动tomcat：**切换到tomcat的bin目录下，然后使用./startup.sh启动tomcat  、 ./shutdown.sh关闭tomcat

**12、杀死进程来停掉进程：**kill -9 2999 PID号 也即是进程ID (先通过ps -ef|grep tomcat 查看进程) 杀死tomcat进程

**13、awk查询某时间段的日志（nginx） 可结合grep：**

- sudo su rcsdo1
- cd /data/local/nginx/logs/
- grep "18/Apr/2020" access.log | awk '$4 >="[18/Apr/2020:10:20:00" && $4 <="[18/Apr/2020:10:30:00"' | wc -l

**14、curl -v 请求绝对地址  ：** 显示一次http通信的整个过程

1**5、./startup执行没有权限：**输入chmod u+x startup.sh 或 chmod 777 startup.sh  增加权限 

**16、关闭防火墙：**systemctl stop firewalld    

**17、查看防火墙状态：**systemctl status firewalld

**//汇总以下systemctl和service命令的区别(两个命令的作用相同，都可以对所安装的组件进行start/stop/status/build)**
systemctl的使用方式：systemctl status/start/stop/build docker
service的使用方式：service docker start

**18、查看该端口的占用情况：**netstat -tunlp | grep 端口号  |  **通过进程号查占用端口**：netstat -nap|grep 10086

19、登录到指定的机子(以root的身份登录)：ssh root@192.168.1.35   //后面就输入密码即可

**20、查看当前系统centos的版本：**cat /etc/centos-release
21、本地无安装rz命令的话，可以使用安装命令：yum install -y rz

22、linux的任务管理器(查看各个进程的资源使用情况)：top

23、挂载外设硬盘：mount [ -t iso9660 ] /dev/sr0 /mnt/cdrom  （mount -t 外设设备的文件系统 外设设别文件名 挂载点）  //外设设备的系统可以不用写，默认会自己识别，然后外设接入时会默认在/dev目录下，然后最右边是我们想要挂载的目录(挂载后的目录与外设目录形成映射) **//特别注意：挂载前需要先创建出/mnt/cdrom目录出来**

24、已对外设硬盘进行挂载后，想拔出之前需要先卸载挂载目录：umount /mnt/cdrom/

25、linux中查看本机IP地址：ifconfig

26、下载一些tar包：wget http://cn.wordpress.org/wordpress-4.9.4-zh_CN.tar.gz 

27、数据库数据的备份(整个数据库)：mysqldump -h主机名 -p端口 -u用户名 -p密码 --database 数据库名 > 文件名.sql（例：mysqldump -h192.168.1.100 -p3306 -uroot -ppassword --database cmdb > /data/backup/cmdb.sql）

28、备份同个库多个表的数据：mysqldump -h主机名 -u用户名 -p密码 --database 数据库名 表1 表2 ... > 文件名.sql （例：mysqldump -h192.168.1.100 -p3306 -uroot -ppassword cmdb t1 t2 > /data/backup/cmdb_t1_t2.sql）

29、**查看linux服务器是否安装过mysql**： rpm -qa|grep -i mysql

30、查看某个进程的连接数：netstat -nat|grep 8084|wc -l

31、查看某个目录实际占用磁盘：du -sh /wxqyh

32、查询某部分信息，过滤排除某些内容：grep "tags" ice-out.txt | grep -v "000000000"

**33、已知redis进程号，查看其安装目录**：ls -l /proc/10086/cwd

**34、linux防火墙相关操作**

```shell
# 查看防火墙指定的端口是否有开放
firewall-cmd --query-port=3306/tcp

# 开放指定端口
 firewall-cmd --zone=public --add-port=3306/tcp --permanent  （--permanent永久生效，没有此参数重启后失效）
# 开放端口完毕后，需要reload一下 重新载入
firewall-cmd --reload

# 删除
firewall-cmd --zone= public --remove-port=80/tcp --permanent
```







### 3.2、认识tomcat的logs目录下的各种目录的作用

//设置实时日志是在tomcat的webapps目录/classes目录下的.properties里面设置的

**1.catalina.日期.log**        
记录tomcat的控制台日志（包括log4j等日志、系统的错误信息）

**2.localhost_access_log.日期.txt**
这个是存放访问tomcat的请求的所有地址以及请求的路径、时间、请求协议以及返回状态码



### 3.3、常用的各种操作

**常用：手机端 46-51 214-217删除tomcat和log4j的日志**
**删除tomcat日志**
sudo su rcsdo1
echo "" > /data/web/apache-tomcat-8080/logs/catalina.out &&
echo "" > /data/web/apache-tomcat-8081/logs/catalina.out &&
echo "" > /data/web/apache-tomcat-8082/logs/catalina.out &&
echo "" > /data/web/apache-tomcat-8083/logs/catalina.out

**删除log4j的日志**
sudo su rcsdo1
echo "" > /data/log/wxqyh/log4j.log && echo "" > /data/log/wxqyh/log4j.log.2020-05-03 && echo "" > /data/log/wxqyh/log4j.log.2020-05-04 

生产登录从库
1、mysql -uroot -p -h 10.1.58.25    
2、Do1admin_hfx123

**登陆中建八局的从库**
1、mysql -uroot -p -h 192.168.210.132;
2、show databases;
3、use wxqyh;

**删除前端vp、vm包**
1、替换了对应portal、mgr、open、文件服务器的vp、vm包 （记得做好备份呢）
2、然后删除了nginx的缓存目录，切到对应目录然后输入（删除当前目录下的所有文件,一定要切到正确的对应目录下）：rm -rf  *
3、强制停止nginx：pkill nginx  
4、Nginx的启动： /home/do1/nginx/sbin/nginx -c /home/do1/nginx/conf/nginx.conf