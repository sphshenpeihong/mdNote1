# 一、Git

## 1、Git常识

### 1.1、Git与SVN的区别

- SVN：集中式
- Git：分布式



### 1.2、Git和代码托管中心

局域网环境下：GitLab服务器
外网环境下：GitHub、码云



### 1.3、本地库和远程库

通过一张图了解IDEA本机、本地库、远程库

![image-20201222193014732](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222193015.png)





团队内部协作：

![image-20201222193027463](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222193028.png)

跨团队协作：

![image-20201222193037681](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222193038.png)



## 2、Git基础

### 2.1、本地库初始化

命令：git init

效果如下：

![image-20201222193050817](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222193051.png)

注意：.git目录中存放的是本地库相关的子目录和文件，不要删除，也不要胡乱修改



### 2.2、设置签名

**形式**：

```txt
用户名：tom
Email地址：goodMorning@atguigu.com
作用：区分不同开发人员的身份
辨析：这里设置的签名和登陆远程库（代码托管中心）的账号、密码没有任何关系。
```

**命令：**

​	项目级别/仓库级别：仅在当前本地库范围内有效

​		**git config** user.name tom_pro

​		**git config** user.email [goodMorning_pro@atguigu.com](mailto:goodMorning_pro@atguigu.com)

​		设置签名后的信息保存在./.git/config下

![image-20201222193101950](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222193102.png)

​	系统用户级别：登陆当前操作系统的用户范围

​		**git config** **--global** user.name tom_glb

​		**git config** **--global** user.email [goodMorning_glb@atguigu.com](mailto:goodMorning_glb@atguigu.com)

​		**信息保存位置：~/.gitconfig (当前用户家目录下的隐藏文件.gitconfig)**

![image-20201222193114248](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222193115.png)

​	级别优先级：

​		就近原则：项目级别优先于系统用户级别，二者都有时采用项目级别的签名

​		如果只有系统用户级别的签名，就以系统用户级别的签名为准

​		二者都没有是不允许的（无法辨别身份）



### 2.3、本地库常用操作命令

**工作区->暂存区：git add good.txt**
**暂存区->工作区：git rm --cached good.txt**

**暂存区->本地库：git commit good.txt（将暂存区提交到本地库需要进入编辑模式写一些注释）**
**暂存区->本地库：git commit -m "I'm coming" good.txt（我们不想进入编辑模式写注释，可以直接加-m "..."直接写注释）**



### 2.4、常用基本命令

1、状态(工作区、暂存区)查看操作

```bash
git status
```

2、添加操作（将工作区的"新建/修改"添加到暂存区）

```bash
git add good.txt
```

3、提交操作

```bash
git commit -m "commit message" good.txt
```

4、push操作

```bash
git push origin develop(分支名)
```

5、pull操作

```bash
git pull origin develop  (直接在对应的mysql文件夹(因为到时候拉去会合并到当前分支)，包括分支也要对应 切换到develop分支， 然后直接打这句代码就可以拉去develop分支的代码)
```

6、清除缓存的账号和密码

```bash
git config --system --unset credential.helper
```



### 2.5、常用操作场景

1、有时候在本地分支开发到一半时，需要临时切到其它分支的话，同时又不想把当前修改过的代码带过去，那么可以将本地分支的代码先暂时放到暂存区中，等到后面想切回来的时候，再切回来，从缓存区中拿回

![image-20201222193126879](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222193127.png)

2、本地分支需要和远程分支相互映射，方法有两种

IDEA：在IDEA中push的时候 修改一下远程分支

命令行：git branch --set-upstream-to=origin/branch develop



3、从tag中切分支

git checkout -b zgjtxxzx-master privatization_3.0.8  //切tags为分支  前面是重命名分支名 后面是tags标签名



**4、如果很不幸，自己的分支已经合到了pro分支，但是pro分支又没有备份分支（master）或者和备份分支不同步的话，那只能赶紧将pro远程分支切一个临时分支，并且回滚到上一个版本或指定的版本，操作：直接在IDEA 对某个log，选择回滚到当前分支**

![image-20201222193139618](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222193140.png)

然后选择hard(本地/远程都删)，此时回滚完只是回滚了本地的pro，还需要将修改使用命令：**git push -f origin master 推到远程**
**操作：回滚到指定的版本后，然后使用命令：git push -f origin master  推到远程。**

推完后的效果：

![image-20201222193158770](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222193159.png)





5、本地的代码已提交到本地库或远程库的话，需要使用reset进行回滚操作了

**IDEA的git reset（回滚操作）**
Reset Type有三种：
**Mixed**默认方法：只保留源码，回退commit和index信息  （直接IDEA的git reset选择Mixed模式，然后To commit输入HEAD~  //HEAD是当前版本，HEAD~/HEAD^是上一个版本），即可将**本地库**的提交**回滚到本地**

**Hard**彻底回退，本地源码也回变成上一个版本内容，不保留之前commit的代码 (直接把提交的代码从远程库和本地库都删除了)  //使用方式也是HEAD~

//注：然后选择hard(本地/远程都删)，此时回滚完只是回滚了本地的pro，还需要将修改使用命令：**git push -f origin master 推到远程**



### 2.6、常见问题

**使用git在Github上到项目出现error：**
error: RPC failed; curl 56 OpenSSL SSL_read: SSL_ERROR_SYSCALL, errno 10054
排查顺序
1.首先是出现该问题可能是上传大小限制，执行如下命令
git config http.postBuffer 524288000

2.此时如果再次clone依然可能报错，这时可以尝试
git config http.sslVerify "false"
//如果显示 fatal:not in a git directory
那么选择一个文件夹作为git仓库  git init



## 3、Github托管

![image-20210219214345956](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210219214347.png)





## 4、Gitlab仓库

### 4.1、新建本地项目推至Gitlab仓库（不使用IDEA）

概述：可能该项目IDEA里面已经配了SVN，那么这个时候，如果在IDEA里面改来改去，难免可能出现啥问题，所以我们试一下，直接纯敲命令，试试将一个项目推至Gitlab仓库。

```shell
# git初始化
git init
# 设置remote地址
git remote add origin http://192.168.122.128:9999/cloud/cloud2021.git
# 将全部文件加入git版本管理 .的意思是将当前文件夹下的全部文件放到版本管理中
git add .
# 提交文件 使用-m 编写注释
git commit -m "注释"
# 推送到远程分支
git push -u origin master
```



git remote add origin https://github.com/sphshenpeihong/mdNote1.git









