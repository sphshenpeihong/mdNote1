# Ambari 自动部署脚本-使用文档



### 1. 使用前准备

#### 1.1 规划节点 Ambari 角色

假设有以下 3 个节点：

| 节点       | ambari 角色   |
| ---------- | ------------- |
| do1cloud01 | ambari-server |
| do1cloud02 | ambari-agent  |
| do1cloud03 | ambari-agent  |

#### 1.2 准备脚本、物料资源

* Ansible 自动部署脚本下载地址：https://git.qiweioa.cn/assad/do1-ansible-ambari

* HDP 物料下载地址（广州总部内网）：http://192.168.21.74:5212/#/s/elUl

  物料目录

```shell
/hdp_asserts
├── HDP-3.1.4.0-centos7-rpm.tar.gz
├── HDP-GPL-3.1.4.0-centos7-gpl.tar.gz
├── HDP-UTILS-1.1.0.22-centos7.tar.gz
└── ambari-2.7.4.0-centos7.tar.gz
```

#### 1.3 系统环境准备

* 系统版本要求： centOS 7.5；

* 所有节点网络互通，并配置静态 IP、hostname；

* 所有节点安装 ssh，并配置节点间免密登录；

* 所有及节点已经安装 `python 2.7` 以及以及 pip；

* 主节点安装  `ansible`；

  ```shell
  yum install ansible
  ```

* 分别上传 ansible 脚本（`/do1-ansible-ambari`）、hdp 物料（`/hdp_asserts`）到 `ambari-server` 节点目录，下面的操作都在 `amabri-server` 节点上进行。

<br>

<br>

### 2. 运行自动部署

#### 2.1 必要脚本配置修改项

修改 `/do1-ansible-ambari/hosts`  内容为实际节点的 hostname，类似如下：

```
[ambari-server]
do1cloud01

[ambari-agent]
do1cloud02
do1cloud03

[all]
do1cloud01
do1cloud02
do1cloud03
```

* `[ambari-server]` ：server 节点的 hostname
* `[ambari-agent]` ：agent 节点的 hostname 列表（排除掉 server 节点）
* `[all]` ：所有节点的 hostname 列表

#### 2.2 其他可选脚本修改项

其余的脚本配置项在 `/do1-ansible-ambari/group_vars` , 默认情况下可以不做修改，有特殊需求可以进行，有几个修改项比较常用：

`/do1-ansible-ambari/group_vars/all.yml`

```yml
# 指定 ntp 服务器地址
ntpl_server: ntp1.aliyun.com
```

`/do1-ansible-ambari/group_vars/ambari-server.yml`

```yaml
# hdp yum 私有仓库 http 端口
yum_repo_port: 8123
# mariadb root 登录密码
db_root_pwd: doc1ccroot
# mariadb ambari 账户密码
db_amabri_pwd: ambari-dba
# mariadb hive 账号密码
db_hive_pwd: hive-dba
# mariadb ozzie 账户密码
db_oozie_pwd: oozie-dba
# mariadb ranger 账户密码
db_ranger_admin_pwd: ranger-dba
```

#### 2.3 运行自动安装脚本

进入到 `amabri-server` 节点的 do1-ansible-ambari 目录，假设该目录位于 `/`  根目录下：

```shell
cd /do1-ansible-ambari && ansible-playbook site.yml
```

等待自动安装结束，大约需要 15-20 分钟自动安装结束。

如果中间因为不可抗因素安装中断，可以直接运行以上指令重新安装，安装的步骤是幂等的。

#### 2.4 进行 amabri 命令行交互式配置

执行以下命令进入交互式配置，默认一路回车即可（jdk 选项选1自动安装 OracleJDK）:

```shell
ambari-server setup
```

交互应答可以参考 **附：amabri-server setup 交互参考**

#### 2.5 启动 ambari-server

```shell
ambari-server start
```

#### 2.6 验证 ambari-server 启动成功

浏览器访问 ambari-server 所在主机 `8080` 端口，如：`http://172.20.0.4:8080`，进入 ambari 管理页面，默认账号密码：

* 账号：admin

* 密码：admin

![image-20200403173753402](https://cdn.jsdelivr.net/gh/Al-assad/md-img@master/bucket-2/202004131703.png)

随后参考 《Ambari HDP 组件安装过程文档》(https://git.qiweioa.cn/assad/do1-ansible-ambari/blob/master/Amabri-HDP-Component-Install-Cookbook.md) 直接在 Amabri UI 上安装 HDP 组件。



<br>

<br>


### 附：amabri-server setup 交互参考

```shell
[root@do1cloud01 java]# ambari-server setup
Using python  /usr/bin/python
Setup ambari-server
Checking SELinux...
WARNING: Could not run /usr/sbin/sestatus: OK
# 使用自定义账户执行 ambari-server
Customize user account for ambari-server daemon [y/n] (n)? y
# 使用 root 账户
Enter user account for ambari-server daemon (root):root
Adjusting ambari-server permissions and ownership...
Checking firewall status...
Checking JDK...
[1] Oracle JDK 1.8 + Java Cryptography Extension (JCE) Policy Files 8
[2] Custom JDK
==============================================================================
# 选择安装 oracle jdk
Enter choice (1): 1
To download the Oracle JDK and the Java Cryptography Extension (JCE) Policy Files you must accept the license terms found at http://www.
oracle.com/technetwork/java/javase/terms/license/index.html and not accepting will cancel the Ambari Server setup and you must install t
he JDK and JCE files manually.
# 接受 oracle jdk license
Do you accept the Oracle Binary Code License Agreement [y/n] (y)? y
Downloading JDK from http://public-repo-1.hortonworks.com/ARTIFACTS/jdk-8u112-linux-x64.tar.gz to /var/lib/ambari-server/resources/jdk-8
u112-linux-x64.tar.gz
Validating JDK on Ambari Server...done.
Check JDK version for Ambari Server...
JDK version found: 8
Minimum JDK version is 8 for Ambari. Skipping to setup different JDK for Ambari Server.
Checking GPL software agreement...
GPL License for LZO: https://www.gnu.org/licenses/old-licenses/gpl-2.0.en.html
# 启用 LZO 相关包
Enable Ambari Server to download and install GPL Licensed LZO packages [y/n] (n)? y
Completing setup...
Configuring database...
# 配置数据库信息
Enter advanced database configuration [y/n] (n)? y
Configuring database...
==============================================================================
Choose one of the following options:
[1] - PostgreSQL (Embedded)
[2] - Oracle
[3] - MySQL / MariaDB
[4] - PostgreSQL
[5] - Microsoft SQL Server (Tech Preview)
[6] - SQL Anywhere
[7] - BDB
==============================================================================
# 选择 mariadb 数据库
Enter choice (1): 3
Hostname (localhost): localhost
Port (3306): 						# 直接回车
Database name (ambari): # 直接回车
Username (ambari):      # 直接回车
Enter Database Password (bigdata):  # 输入 /do1-ansible-ambari/group_vars/ambari-server.yml 文件中的 db_amabri_pwd 项
Re-enter password: 			# 确认密码
Configuring ambari database...
# 确认使用已经安装的 jdbc 连接库
Should ambari use existing default jdbc /usr/share/java/mysql-connector-java.jar [y/n] (y)? y
Configuring remote database connection properties...
WARNING: Before starting Ambari Server, you must run the following DDL directly from the database shell to create the schema: /var/lib/ambari-server/resources/Ambari-DDL-MySQL-CREATE.sql
Proceed with configuring remote database connection properties [y/n] (y)? y
Extracting system views...
ambari-admin-2.7.4.0.118.jar
....
Ambari repo file contains latest json url http://public-repo-1.hortonworks.com/HDP/hdp_urlinfo.json, updating stacks repoinfos with it...
Adjusting ambari-server permissions and ownership...
Ambari Server 'setup' completed successfully.
```



