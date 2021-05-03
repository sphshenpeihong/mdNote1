# 一、容器化部署

## 1、Docker的作用

**Docker的作用**：Docker可以打包应用以及依赖包成镜像，然后我们就可以把镜像发布到任何一个流行的Linux发行版的机器上

**三步骤**：编写Dockerfile文件(镜像的构建文件) -> docker build(构建镜像) -> docker run (运行镜像)







### 1.1、Docker流程

![image-20201222183316953](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210409094110.png)



### 1.2、Dockerfile是什么？

Dockerfile是用来构建Docker镜像的构建文件，是由一系列命令和参数构成的脚本呢（简单来说就是Dockerfile就是用来打镜像的）



### 1.3、Dockerfile基础知识

- 每条保留字指令都必须为大写字母并后面要跟随至少一个参数(比如FROM centos)

- 指令按照从上到下，顺序执行
- #表示注释
- 每条指令都会创建一个新的镜像层，并对镜像进行提交



### 1.4、Dockerfile保留字指令

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





## 2、Docker常用命令

### 2.1、常用的镜像命令

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



### 2.2、常用的容器命令

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





## 3、不同环境下打镜像的方式

### 3.1、Linux

#### 3.1.1、打镜像需先准备的文件

![image-20210409092809181](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210409092810.png)

**Dockerfile**：打镜像的整个命令集合都写在Dockerfile里面了

**jar包**：需要复制到容器里边



#### 1.1.2、打镜像命令

![image-20210409094808471](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210409094809.png)

docker build  -t checker-bus:dev20210409 .



#### 1.1.3、运行镜像

![image-20210409095710537](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210409095711.png)

docker run --restart=always --net=host -d -e "spring.profiles.active=test" -e "JAVA_OPTS=-Xmx2048m -Xms512m -XX:MetaspaceSize=256m -XX:MaxMetaspaceSize=256m" -v /data/config/checker-bus:/data/app/config -v /data/logs:/data/logs --name=checker-bus checker-bus:dev20210409



#### 1.1.4、将镜像推到私有仓库

1、登录：docker login -u docker -p 123456  localhost:5000

2、将镜像打tag：docker tag 镜像ID localhost:5000/repo2/checker-bus:dev20210409

3、将镜像推到远程仓库：docker push localhost:5000/repo2/checker-bus:dev20210409



#### 1.1.5、从私有仓库拉取镜像

1、登录：docker login -u docker -p 123456  localhost:5000

2、拉取：docker pull localhost:5000/repo2/checker-bus



### 3.2、Windows





## 4、愿景

git -> jenkins -> docker -> k8s

**git**：我们开发完毕后，代码提交到git仓库上

**jenkins**：配置关联了git，自动/手动打包，然后打镜像推送到docker私有仓库

**k8s**：从docker仓库拉取镜像，并创建容器



不用再打命令了

git 

jenkins   打完包后   打镜像   push docker镜像私有仓库



k8s绑定某个私有仓库

pull 下来 

创建容器

