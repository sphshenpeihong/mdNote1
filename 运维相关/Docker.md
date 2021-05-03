# 一、Docker

## 0、linux安装docker

> 0.1、安装yum-utils

```shell
yum install -y yum-utils
```



> 0.2、配置国内源

```shell
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```



> 0.3、解决problem with installed package podman-1.6.4-10.的报错

```shell
yum erase podman buildah
```



> 0.4、安装Docker

```shell
yum install -y docker-ce docker-ce-cli  containerd.io --nobest
```



> 0.5、查看Docker版本

```shell
docker -v
```



## 1、docker的作用

//可以把容器看作是一个简易版的Linux环境

Docker的作用：Docker可以打包应用以及依赖包成镜像，然后我们就可以把镜像发布到任何一个流行的Linux发行版的机器上

镜像和容器的关系：镜像相当于类，容器相当于实例化对象，可以一个镜像可以跑很多个容器之上

镜像：镜像是静态的，需要借助容器才能运行

Q：镜像是什么？

A：镜像是一种轻量级、可执行的独立软件包，可以用来打包软件运行环境和基于运行环境开发的软件，它包含运行某个软件所需的所有内容，包括代码、运行时、库、环境变量和配置文件。



Q：镜像为何要有分层（千层饼）的概念？

A：最大的一个好处就是共享资源，比如有很多镜像都从相同的base镜像构建而来，那么宿主机只需在磁盘上保存一份base镜像，同时内存也只需加载一份base镜像，就可以为所有容器服务了。而且镜像的每一层都可以被共享。



容器：容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等。

**公用镜像：**mysql、redis、mq、memcached、centos（Docker Hub可以免费下载，嫌慢的可以直接去阿里云仓库下载）

**私用镜像**：我们写的微服务项目(Springboot项目)，写完后可以通过Dockerfile制作成镜像

**docker三要素：**仓库、镜像、容器

**docker容器的作用**：下载镜像、打包镜像、删除镜像、更新镜像等等

语法：**docker 【options】 command 【arg...】**

//注：使用docker之前，需要先启动docker服务：systemctl start docker / service docker start



## 2、常用的镜像命令

1、查看当前服务器内的镜像：docker images  【options】

- -a：列出本地所有的镜像（含中间映像层）
- -q：只显示镜像ID
- --digests：显示镜像的摘要信息
- --no-trunc：显示完整的镜像信息

2、查看docker远程仓库某个镜像的详细信息：docker search tomcat -s 30

3、拉取docker远程仓库的镜像：docker pull tomcat（因为tomcat有很多个版本，前面的相当于是docker pull tomcat:latest）

4、删除本地某个镜像：docker rmi -f practice（后面不跟版本号的话，默认删除最新的，相当于practice:latest）

**5、启动镜像**：docker run -d -p 8085:8085 --name=practiceOne practice:1.1

- --name：为容器实例命名，不使用此命令的话，则docker会给默认名字
- -d：后台运行容器，并返回容器ID，也即启动守护式容器
- -i：以交互模式运行容器，通常与-t同时使用
- -t：为容器重新分配一个伪终端，通常与-i同时使用
- -P：随机端口映射(容器的端口会采用Dockerfile的EXPOSE的声明暴露端口),到时想访问服务时，直接访问宿主机端口号即可成功映射
- -p：指定端口映射，hostPort:containerPort（前面是对外宿主机端口，后面是容器内部运行的端口，一般写相同的端口即可）
- --privileged=true：获取宿主机的root权限
- -e：设置环境变量(也可以在Dockerfile使用ENV设置，但是这个优先级更高)，例：-e "server.port=8085"
- -v：设置容器卷，宿主机绝对路径:容器绝对路径，可以挂载持久化某个目录或文件（实现双向传输）
- -h：指定容器的主机名，不指定docker也会设置随机主机名，可以进入容器后直接打命令hostname
- --net：run的时候，不添加命令的话，那么容器的网卡里面的ip地址会和docker0的地址在同一网段，添加的话后面可以自己指定容器的ip v4的地址。例： --net=host

**6、制作镜像**：docker build -f Dockerfile -t practice:1.1 . (最后面有一个. ) //如果此时当前目录下的Dockerfile文件的名字就叫Dockerfile的话，那么就是最标准的命名了，此时可以不要-f Dockerfile了，直接docker build -t practice:1.1 . 即可

- -f：指定要使用的Dockerfile路径

7、镜像打成tar包：docker save -o practice.tar practice:1.1 //需要声明tar包名 和 镜像:版本

8、tar包加载成镜像：docker load < practice.tar 或 docker load -i practice.tar



## 3、常用的容器命令

docker inspect 容器id  Mounts 有写那两个文件进行映射 共享 一般VolumesRW = true 也即是容器ID是对文件可读可写的

1、查看容器(默认仅包含启动容器)：docker ps【option】

- -a：查看所有容器（也包含未启动的） //示例：docker ps -a

- -l：查看上一个容器（之前关闭过的） //示例：docker ps -l

2、开启容器：docker start 容器id/容器名

3、停止容器：docker stop 容器id //强制停止 docker kill 容器id 一般使用stop即可

4、移除一个容器：docker rm 容器id

5、重启一个容器：docker restart 容器id

**6、查看docker实时日志：docker logs -f 容器id**

7、查看docker尾行实时xxx行日志：docker logs --tail xxx -f 容器Id

​	//例：查看docker后5000行日志：docker logs --tail 5000 -f 容器id

- -f：实时更新日志
- --tail：显示最后几条

- --since：开始的时间
- --until：结束时间

8、查看某个时间段区间的范围的日志：docker logs --since='2020-10-19T14:00:00' --untit='2020-10-19T14:39:00' practice > /home/shenpeihong/a.log

9、复制宿主机的文件到Docker容器（两种方式）

- 1、docker run的时候，使用-v 去指定共享目录，实现挂载

- 2、已经启动的话，可以使用docker cp

   宿主机传输到容器：docker cp 宿主机文件 容器ID:容器绝对路径目录 (例：docker cp Dockerfile practice:/home)

   容器传输到宿主机：docker cp 容器名:容器绝对路径目录 宿主机文件

10、docker运行Mysql并设置密码：docker run -it -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root mysql

- --name：给容器取名字
- -v：挂载，1：挂载文件的话，宿主机要求一定需要有该文件，目录的话允许宿主机可以没有此目录，会自动创建的。（挂载过程以宿主机为准，会copy宿主机的文件直接覆盖掉容器里面的文件，可以理解成直接复制宿主机的目录或文件到容器中(若容器也有此，宿主机也直接覆盖)）

- -e：



11、查看容器里面的详细信息：docker inspect 容器id （json格式），可以根据以及关键字查看详细信息

- Mounts：可以看宿主机和容器的共享目录映射关系 //并且"VolumesRW"=true（也即是容器ID是对文件夹可读可写的）

12、**进入容器内部：docker exec -it 容器id /bin/sh** 或 docker attach 容器id //使用这种方法就可以进入容器内部了(/bin/sh是因为启动容器只是启动后台进程，如果前台进程没有执行的话，就会自动被docker给kill掉了，所以后面加上/bin/sh是相当于运行一个进程，我们运行了进程的话，那么docker就不会去自动关闭我们的容器了)

//从容器内部退出到宿主机，使用Ctrl+Q+P切出去

13、已启动的容器，查看容器正在运行的进程：docker top 容器名/容器ID









Q：进入到容器里面，可以使用什么命令来获取容器的相关信息呢？

- hostname：获取容器的主机名（run的时候，可以使用 -h 命令去指定容器的主机名称，若不指定，则会随机分配主机名）

- ip addr：获取容器的ip地址(内网)

- env：环境变量，一般是Dockerfile使用ENV 或 run的时候使用-e去指定容器的环境变量



Q：如果宿主机不想容器有些的权限，只允许有读的权限，那么如何操作？

A：docker run -it -v /myDataVolume:/dataVolumeContainer:ro --name=myCentos centos

//:ro也即是readOnly //这个时候再次docker inspect 容器id的时候，"VolumesRW"=false（也即是容器对数据卷只读不可写）



Q：若运行的镜像是一个系统(centos)，启动后，发现docker ps居然没有？

A：是因为docker会观察你这个启动着的容器如果没有运行东西的话，会自动给你kill掉，想让centos这个镜像一致启动着的话，可以加一些shell脚本呢的定时打印功能，一直干或者，docker就不会去kill掉



Q：想把容器内文件拷贝出来应当如何操作？

A：docker cp 容器ID:容器内路径 目的主机路径



## 4、数据卷

作用：为了使得容器内的数据同步到宿主机中，防止容器内有用的数据丢失，简单点说就是挂载

卷的设计目的：就是数据的持久化

特点：

- 数据卷可在容器之间共享或重用数据
- 卷中更改可以直接生效
- 数据卷中的更改不会包含在镜像的更新中
- 数据卷的生命周期样子hi持续到没有容器使用它为止

使用：

docker run -it -v /myDataVolume:/dataVolumeContainer --name=myCentos centos (实现容器的文件夹持久化到宿主机的文件夹 前面是宿主机的文件夹，后面是容器的文件夹。绝对路径) //数据共享后，即使容器暂时stop，那么宿主机对文件夹进行修改后，到时候容器启动后，共享目录同样会修改。



## 5、Dockerfile

Q：Dockerfile是什么？

A：Dockerfile是用来构建Docker镜像的构建文件，是由一系列命令和参数构成的脚本呢（简单来说就是Dockerfile就是用来打镜像的）

//构建三步骤：编写Dockerfile文件(镜像的构建文件) -> docker build(构建镜像) -> docker run (运行镜像)



**1、Dockerfile内容基础知识**

- 每条保留字指令都必须为大写字母并后面要跟随至少一个参数(比如FROM centos)

- 指令按照从上到下，顺序执行
- #表示注释
- 每条指令都会创建一个新的镜像层，并对镜像进行提交



**2、Dockerfile保留字指令**

- FROM：基础镜像，当前新镜像是基于哪个镜像的(祖先级别镜像scratch)

- MAINTAINER：镜像维护者的姓名和邮件地址

- RUN：容器构建时需要运行的命令（比方：安装许多软件包/修改时区） //RUN echo "Asia/Shanghai" > /etc/timezone

- EXPOSE：当前容器对外暴露出的端口（比方些redis镜像脚本呢，容器暴露端口6379）

- WORKDIR：指定在创建容器后，终端默认登录进来的工作目录，一个落脚点（默认进来时/根目录）

- ENV：用来在构建镜像过程中设置环境变量

- ADD：将宿主机目录下的文件拷贝进镜像且ADD命令会自动处理URL和解压tar压缩包（比方我们到时候想要构建一个tomcat镜像，那这里需要引入相关的tar包或其它包）书写格式:ADD jdk-8u171-linux-x64-tar.gz /usr/local/

- COPY：类似ADD，拷贝文件和目录到镜像中。将宿主机目录/文件复制到镜像的制定路径中(功能和ADD差不多，只不过ADD还帮忙把你的压缩包给解压了)

- VOLUME：容器数据卷，用于数据保存和持久化工作（建立容器目录和宿主机目录的映射关系）

- CMD：制定一个容器启动后要运行的命令，Dockerfile中可以有多个CMD命令，但只有最后一个生效，CMD会被docker run 之后的参数替换 (>覆盖)

- ENTRYPOINT：制定一个容器启动时要运行的命令，ENTRYPOINT的目的和CMD一样，都是在制定容器启动程序及参数

- ONBUILD：当构建一个被集成的Dockerfile时运行命令，父镜像在被子集成后，父镜像的onbuild被触发（相当于我们书写的镜像里面写了这个命令，后面增加了操作，如果以后有镜像去FROM 我们的镜像的话，那么build Dockerfile时，会执行ONBUILD后面的操作）

**下面是保留字指令执行的时机**

| BUILD         | Both    | RUN        |
| ------------- | ------- | ---------- |
| FROM          | WORKDIR | ENV        |
| MAINTAINER    | USER    | EXPOSE     |
| RUN           |         | VOLUME     |
| ADD           |         | CMD        |
| COPY          |         | ENTRYPOINT |
| ONBUILD       |         |            |
| .dockerignore |         |            |



Q：RUN命令和CMD命令的区别

共同点：后面都是带相关的linux命令

不同点：执行命令的时机不同，RUN命令是构建镜像的构成中执行，而CMD是运行镜像过后才去执行的。

//RUN命令常用来执行安装软件包，也可以执行其它的linux命令的操作。(示例：RUN echo "Asia/Shanghai" > /etc/timezone)



Q：CMD和ENTRYPOINT的区别（共同点都是容器启动之后才会去执行的命令）

A：CMD相当于覆盖，如果我们run镜像的时候，自己在后面加了命令的话，比方加了ls，那么会只执行ls，而不会去执行Dockerfile里面的CMD了。（如果Dockerfile里面有多个CMD的话，那么最终只会去执行最后一行，ENTRYPOINT也是一样的。）

//关键的区别就是CMD和ENTRYPOINT同样是在Dockerfile里面些了一条命令，但是我们docker run的时候，如果后面又加了命令的话，那么CMD的会被覆盖掉，而ENTRYPOINT不会被覆盖，而且ENTRYPOINT还可以额外给自己些的命令添加option参数呢



**RUN、CMD、ENTRYPOINT有两种命令书写格式(shell和exec) //也就是说我们些Dockerfile的时候，这三个保留字指令后面可以有两种书写方式**

例：

shell方式：RUN echo "123" > /home/a.log

exec方式：RUN ["mkdir", "/home/shenpeihong"]  //json键值对，key是linux命令，value是值 //有些时候也可以省略了key，直接括号里面写值即可

//exec方式有一个问题，就是我们$name 去引入ENV的环境变量的话，那么如果单单上面那个键值对的话，那么是无法解析的，那么需要设置解析变量，如下：

RUN ["echo", "-c", "hello $name"]  // -c表示解析变量



小案例(tomcat)：

1、mkdir -p /zzyyuse/mydockerfile/tomcat9

2、在上述目录下新建文件 touch c.txt

3、将jdk和tomcat安装的压缩包拷贝进上一步目录(apache-tomcat-9.0.8.tar.gz、jdk-8u171-linux-x64.tar.gz)

4、在zzyyuse/mydockerfile/tomcat9 目录下新建Dockerfile文件

5、构建Dockerfile文件，生成"增强"镜像

6、run镜像

//Dockerfile文件如下

![image-20201222183245677](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222183246.png)



Q：为什么run tomcat的镜像时，会去将tomcat的服务也起来了呢？

A：因为tomcat的Dockerfile，最后有这么一行命令：CMD ["catalina.sh", "run"] //启动 作用和startup.sh一样的 都是启动tomcat

![image-20201222183301047](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222183302.png)

![image-20201222183316953](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222183317.png)



本地镜像发布到阿里云上（push）

1、登录：docker login --username = registry.cn-hangzhou.aliyuncs.com 

2、将镜像打tag：docker tag 镜像ID registry.cn-hangzhou.aliyuncs.com/zzyybuy/mycentos:1.4.1

3、将镜像推到远程仓库：docker push registry.cn-hangzhou.aliyuncs.com/zzyybuy/mycentos:1.4.1



## 6、Docker网络通信

### 6.1、关于服务器的各种地址

![image-20201222183353713](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222183354.png)

### 6.2、Docker网络模式

前言：安装Docker时，它会自动创建三个网络，bridge（创建容器默认连接到此网络）、none、host

作用：创建一个容器时，为了使它与宿主机或其它容器，或其它设备通信的话，需要选择一种网络模式，选择网络模式将会决定容器的网卡里各种地址。

| 网络模式   | 简介                                                         |
| ---------- | ------------------------------------------------------------ |
| Host       | 容器将不会虚拟出自己的网卡，配置自己的IP等，而是使用宿主机的IP和端口 |
| Bridge     | 此模式会为每一个容器分配、设置IP等，并将容器连接到一个docker0虚拟网桥，通过docker0网桥以及iptables nat表配置与宿主机通信 |
| None       | 该模式关闭了容器的网络功能                                   |
| Container  | 创建的容器不会创建自己的网卡、配置自己的IP，而是和一个指定的容器共享IP、端口范围。 |
| 自定义网络 | 可以自己自定义一个网络(网络段、网关地址、   选择网络模式，上面四种) |



### 6.3、宿主机与容器的网络通信

![image-20201222183410686](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222183411.png)

原理：我们每启动一个docker容器，docker就会给docker容器分配一个ip，我们只要安装了docker，就会有一个网卡docker0桥接模式，使用的技术是evth-pair技术！

//evth-pair技术：就是一对的虚拟设备接口，它们都是成对出现的，一端连着协议，一端彼此相连(evth-pair充当一个桥梁，连接各种虚拟网络设备的。)

结论：容器和容器之间是可以互相ping通的！



### 6.4、容器访问容器的方式

1、ping 地址

2、ping 容器名字  (这个默认不支持，可以run镜像的时候，使用--link去指定某个容器，底层原理是增加Hosts文件夹的映射关系而已)

3、使用自定义网络去实现容器访问容器可以通过 容器的IP地址 + **容器名字** + 容器ID



### 6.5、自定义网络(网络互联)

**使用自定义网络的目的：**

因为我们之前容器启动，没有指定网络模式的话，默认是使用docker0的桥接模式，和docker0是在同一个网段下的，我们可以自定义网络段，自己自定义网络段的ip地址范围、子网掩码、网关地址、使用的网络模式(默认桥接、以及各个参数，设置成自定义网络，到时候我们启动容器的时候，就不要再去使用默认的网络模式了（默认是 --net=bridge，到时候使用这种网络模式的话，会按照这种模式的规则去得到网卡地址）

1、网络模式(容器启动时指定网络模式)

bridge：桥接docker（默认，自己创建的也是使用的bridge模式）

none：不配置网络

host：和宿主机共享网络

container：实现网络连通（用的少）

命令查看docker网络模式：docker network ls

![image-20201222183425134](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222183426.png)

**2、docker网络相关命令**

docker network ls：查看当前docker的网络

docker network create：创建docker网络

![image-20201222183439039](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222183439.png)



### 6.6、网络连通

目的：两个字段域之间的连同，比方tomcat集群与redis集群之间的连通  （redis集群与tomcat集群之间的网段不通）

操作步骤：新建两个网络，然后运行两个tomcat时，采用的是不同的网络，这样一来，这两台tomcat无法互相ping通

解决：tomcat1想实现ping tomcat2，那么需要连接容器到一个网络上

命令：docker network connect net66 tomcat1

![image-20201222183459865](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222183500.png)

![image-20201222183511437](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222183512.png)



## 7、骄子大学项目

**发布云大学微服务步骤：**

**1、先打云大学微服务的jar包** //如果只改动wxqyh-learnonline的模块内容的话，需要先打wxqyh-learnonline，再打云大学微服务

**2、打镜像所需文件如下**：

![image-20201222183527731](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222183528.png)

![image-20201222183541043](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222183542.png)

![image-20201222183555577](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222183556.png)

**3、三个文件准备完毕，直接传输上传到我们的虚拟机上** (都放到同级目录)

![image-20201222183630741](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222183631.png)

**4、开始打镜像**

**直接输入命令**：docker build -f Dockerfile -t dqsf-apps-learnonline:uat_20201105 . 

![image-20201222183641235](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222183642.png)

**查看镜像：**docker images

![image-20201222183652213](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222183653.png)

//镜像已打成功，此时我们还无法直接把镜像传回我们的windows的磁盘，因为镜像存在于docker里面，所以下一步我们需要将镜像打包

**5、将镜像打tar包**

命令：docker save -o dqsf-apps-learnonline_uat_20201105.tar dqsf-apps-learnonline:uat_20201105

![image-20201222183706953](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222183707.png)

**6、tar包加权限**

命令：chmod 777 dqsf-apps-learnonline_uat_20201105.tar

![image-20201222183715937](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222183717.png)

//此时该tar包就可以发给运维让他去发布了

**7、发布镜像**

首先把tar包传到微服务的机器，然后需要解压 （tar包解压成镜像）

**解压tar包**

命令：docker load -i dqsf-apps-learnonline_uat_20201105.tar 

​	     或docker load < dqsf-apps-learnonline_uat_20201105.tar

![image-20201222183726478](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222183727.png)

**运行镜像**

命令：

docker run --restart=always --net=host  -d -e  "spring.profiles.active=develop" -e "server.port=8194" -e "JAVA_OPTS=-Xmx2048m -Xms512m -XX:MetaspaceSize=256m -XX:MaxMetaspaceSize=256m" -v /data/application.properties:/home/project/app/config/application.properties --name=learnonline0107 dqsf-apps-learnonline:1.0

![image-20201222183738301](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222183739.png)

腾讯文档：

https://docs.qq.com/doc/DUWVienhUd1hoWXNv?createTS=1604567034953&templateId=31227



## 8、BUS项目容器化部署





## 9、Docker私有仓库

概述：Docker Hub是远程仓库，搭建私有仓库的话一般用registry harbor两种

### 9.1、registry

**1、linux搭建docker私有仓库环境步骤**

```shel
0.拉取镜像
docker pull registry:2.7.0
1.创建文件夹
/data/docker/registry/auth
2.在上面的文件夹新建账号/密码文件
echo "user:docker passwd:123456" >htpasswd
3.对账号/密码进行加密
docker run --entrypoint htpasswd registry:2.7.0 -Bbn docker 123456 > /data/docker/registry/auth/htpasswd
4.将私有仓库的镜像run起来
docker run -itd --net=host -p 5000:5000 --restart=always --name docker-registry -v /data/docker/registry/auth:/auth -v /data/docker/registry:/var/lib/registry -e "REGISTRY_AUTH=htpasswd" -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" -e "REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd" registry:2.7.0
```



**2、运行完后，校验服务是否跑起来**

curl -v http://localhost:5000/v2

账号/密码：docker/123456



**3、本地打完镜像后，推镜像到私有仓库**

1、登录：docker login -u docker -p 123456  localhost:5000

2、将镜像打tag：docker tag 镜像ID localhost:5000/repo/checker-bus:dev20210408

3、将镜像推到远程仓库：docker push localhost:5000/repo/checker-bus:dev20210408



**4、推完镜像后，校验是否成功**

访问 http://localhost:5000/v2/_catalog (私有仓库目录)







