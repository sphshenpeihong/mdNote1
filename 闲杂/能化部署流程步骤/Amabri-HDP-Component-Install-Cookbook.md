# Amabri 平台 HDP 组件安装文档

> Auth	余林颖
>
> Dept	技术委员会
>
> Date	2020-04-03



- [1. HDP 集群规划](#1-hdp-集群规划)
    - [1.1安装前提](#11安装前提)
    - [1.2 安装组件列表](#12-安装组件列表)
    - [1.3 集群规划](#13-集群规划)
- [2. HDP 组件安装步骤](#2-hdp-组件安装步骤)
    - [2.1 修改 Ambari  Admin 账号密码（十分重要）](#21-修改-ambari--admin-账号密码十分重要)
    - [2.2 集群初始化](#22-集群初始化)
    - [2.3 安装 Zookeeper 、Ambari Metrics 、 SmartSense 服务](#23-安装-zookeeper-ambari-metrics--smartsense-服务)
    - [2.4 安装 HDFS 服务](#24-安装-hdfs-服务)
    - [2.5 安装 Yarn + MapReduce2 服务](#25-安装-yarn--mapreduce2-服务)
    - [2.6 安装 HBase ](#26-安装-hbase-)
- [3. FAQ](#3-faq)



## 1. HDP 集群规划

#### 1.1安装前提

进行本文档安装 HDP 组件的前提，是已经按照《Ambari 平台部署文档》在集群上完成 Ambari 组件的全部部署过程。

以下安装步骤全部在 Ambari 平台 UI 界面上进行。

#### 1.2 安装组件列表

目前道一 Ambari 平台主要作用是平稳支撑 HBase 运行，规划使用的组件列表如下：

| HDP 组件                                                     | 组件作用                                                     | 是否必须                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------ |
| Zookeeper                                                    | 分布式协调中心                                               | 是                                         |
| HDFS                                                         | Hadoop 分布文件系统                                          | 是                                         |
| Yarn + MapReduce2                                            | Hadoop 资源调度、计算框架                                    | 是                                         |
| HBase                                                        | Hadoop 列式数据库                                            | 是                                         |
| Ambari Metrics                                               | Ambari 指标收集系统                                          | 可选，建议安装                             |
| Log Search                                                               . | Ambari 托管 HDP 服务日志聚合、分析服务，依赖 Atlas、Kafka、Infra Solr 服务。其中 Solr 对安装节点的内存、储存、CPU 有较大的负载，建议独立节点安装 | 可选                                     . |

#### 1.3 集群规划

以下集群规划按照 `Zookeeper`、`HDFS`、`Yarn + MapReduce2`、`HBase`、`SmartSense`、`Ambari Metrics` 服务、以3 节点（**单节点至少 8C-16GB**）的最小规模进行规划。

更大规模的集群请根据节点资源的实际情况进行规划。

| 组件角色       | do1cloud01（ambari-server）  | do1cloud02（ambari-agent）                        | do1cloud03（ambari-agent）                |
| -------------- | ---------------------------- | ------------------------------------------------- | ----------------------------------------- |
| Zookeeper      | Zookeeper Server             | Zookeeper Server                                  | Zookeeper Server                          |
| HDFS           | NameNode、DataNode           | NameNode（sc）、DataNode                          | DataNode                                  |
| Yarn           | ResourceManager、NodeManager | HistoryManger、NodeManager、Timeline Service V1.5 | TIMELINE SERVICE V2.0 READER、NodeManager |
| HBase          | HMaster                      | HMaster、RegionServer、Phoenix Query Server       | HRegionServer、Phoenix Query Server       |
| Ambari Metrics |                              |                                                   | Metrics Collector、Grafana                |

采用逐层安装的方式，根据每层安装后节点的负载调整节点角色。

如果是 4 节点及以上，则将这些 HDP 服务全部部署在 ambari-agent 节点上，ambari-server 独立一个节点。





## 2. HDP 组件安装步骤

以下进行逐层服务安装，每安装一层服务，验证该层服务工作正常，不建议一次性安装全部服务。



#### 2.1 修改 Ambari  Admin 账号密码（十分重要）

![image-20200404012338669](https://cdn.jsdelivr.net/gh/Al-assad/md-img@master/bucket-2/202004040330.png)

![image-20200404013019585](https://cdn.jsdelivr.net/gh/Al-assad/md-img@master/bucket-2/202004040341.png) 

更改为新的复杂密码，并妥善保管。



#### 2.2 集群初始化

1）进入集群安装向导

![image-20200404013227914](https://cdn.jsdelivr.net/gh/Al-assad/md-img@master/bucket-2/202004040348.png)

2）集群命名

![image-20200404013555949](https://cdn.jsdelivr.net/gh/Al-assad/md-img@master/bucket-2/202004040310.png)

3）选择 HDP 版本，并填写 yum 仓库

Repository 选择 `Use Local Repository`

在 redhat7 项中填写一下 repo 信息：

| Name               | BaseUrl                                                      |
| ------------------ | ------------------------------------------------------------ |
| HDP-3.1            | http://do1cloud01:8123/repository/HDP/centos7/3.1.4.0-315/   |
| HDP-3.1-GPL        | http://do1cloud01:8123/repository/HDP-GPL/centos7/3.1.4.0-315/ |
| HDP-UTILS-1.1.0.22 | http://do1cloud01:8123/repository/HDP-UTILS/centos7/1.1.0.22/ |

![image-20200404014824013](https://cdn.jsdelivr.net/gh/Al-assad/md-img@master/bucket-2/202004040319.png)

4）选择安装节点信息

填写所有 ambari-agent 节点 hostname；

ssh 登录认证私钥文本，可以在 amabri-server 节点（do1cloud01）执行 `cat ~/.ssh/id_rsa` 获取。

![image-20200404020853634](https://cdn.jsdelivr.net/gh/Al-assad/md-img@master/bucket-2/202004040357.png)

5）等待所有 agent 节点安装结束

![image-20200404021143835](https://cdn.jsdelivr.net/gh/Al-assad/md-img@master/bucket-2/202004040349.png)

**请耐心等待，切记不要按浏览器刷新按钮**，会重置整个安装部署的。

如果节点安装注册过程失败，请根据 UI 页面上该节点的错误日志进行具体的排查，解决问题后按 `Retry Fail` 按钮重新进行失败节点注册。



#### 2.3 安装 Zookeeper 、Ambari Metrics 、 SmartSense 服务

1）接着上一步，只勾选 Zookeeper 、Ambari Metrics、SmartSense 服务，其他组件都不勾选。

![image-20200404023823370](https://cdn.jsdelivr.net/gh/Al-assad/md-img@master/bucket-2/202004040339.png)

2）进行 Master 节点的分配，按照 1.3 的角色规划表进行分配（或者按照实际给出的 ambari 角色规划表）

![image-20200404024131032](https://raw.githubusercontent.com/Al-assad/md-img/master/bucket-1/20200404024131.png)

3）进行 Slaves、Clients 角色分配

![image-20200404024212936](https://cdn.jsdelivr.net/gh/Al-assad/md-img@master/bucket-2/202004040325.png)

4）设置 Grafana、Activity Explorer Admin 账号密码的设置

这里是分别设置 Ambari Metrics-Grafana、SmartSense-Activity Explorer 的管理账号密码，请设置复杂密码后妥善保管。

![image-20200404024443244](https://raw.githubusercontent.com/Al-assad/md-img/master/bucket-1/20200404024443.png)

5）指定 zookeepr 、Ambari Metrics 组件的安装、日志路径，没有特殊需求的话，用默认就行，直接 next

![image-20200404024641129](https://cdn.jsdelivr.net/gh/Al-assad/md-img@master/bucket-2/202004040332.png)

6）回顾一下刚才设置的账号，确认没有问题就下一步

<img src="https://cdn.jsdelivr.net/gh/Al-assad/md-img@master/bucket-2/202004040342.png" alt="image-20200404024738552"  />

7）调整 Zookeeper、Ambari Metrics、SmartSense 的所有配置项，没有特殊需求，直接用默认的下一步就行。

![image-20200404024852469](https://cdn.jsdelivr.net/gh/Al-assad/md-img@master/bucket-2/202004040350.png)

8）等待相关服务安装启动，喝杯茶先

![image-20200404024958427](https://cdn.jsdelivr.net/gh/Al-assad/md-img@master/bucket-2/202004040333.png)

9）相关服务安装结束

![image-20200404025436088](https://cdn.jsdelivr.net/gh/Al-assad/md-img@master/bucket-2/202004040345.png)

检查服务是否运行正常

![image-20200404025515940](https://cdn.jsdelivr.net/gh/Al-assad/md-img@master/bucket-2/202004040307.png)



#### 2.4 安装 HDFS 服务

1）新增 HDFS 服务

![image-20200404032546776](https://cdn.jsdelivr.net/gh/Al-assad/md-img@master/bucket-2/202004040346.png)

2）分配 Master 角色

![image-20200404032727209](https://cdn.jsdelivr.net/gh/Al-assad/md-img@master/bucket-2/202004040327.png)

3）分配 Slave、Client 角色

![image-20200404032922993](https://cdn.jsdelivr.net/gh/Al-assad/md-img@master/bucket-2/202004040323.png)

4）HDFS 参数调节，根据机器的实际情况进行调整，可以先默认，安装结束后可以再根据机器情况重新调整

![image-20200404033043432](https://cdn.jsdelivr.net/gh/Al-assad/md-img@master/bucket-2/202004040343.png)

5）等待安装结束，再喝杯茶

![image-20200404033229460](https://cdn.jsdelivr.net/gh/Al-assad/md-img@master/bucket-2/202004040329.png)

6）启动 HDFS NameNode HA

点击 HDFS Service 面板，开启 HA。

![image-20200404034046471](https://cdn.jsdelivr.net/gh/Al-assad/md-img@master/bucket-2/202004040356.png)



#### 2.5 安装 Yarn + MapReduce2 服务

1）新增 Yarn + MapReduce2 服务

![image-20200404034350577](../Library/Application Support/typora-user-images/image-20200404034350577.png)

2）分配 Master 角色

![image-20200404034618330](https://cdn.jsdelivr.net/gh/Al-assad/md-img@master/bucket-2/202004040318.png)

3）分配 Slave、Client 角色

![image-20200404034640754](https://cdn.jsdelivr.net/gh/Al-assad/md-img@master/bucket-2/202004040340.png)

4）Yarn、MR 参数调节，根据机器的实际情况进行调整，可以先默认，安装结束后可以再根据机器情况重新调整

![image-20200404034714593](https://cdn.jsdelivr.net/gh/Al-assad/md-img@master/bucket-2/202004040314.png)

5）等待安装结束

![image-20200404034738731](../Library/Application Support/typora-user-images/image-20200404034738731.png)



#### 2.6 安装 HBase 

1）新增 HBase 服务

![image-20200404034919633](https://cdn.jsdelivr.net/gh/Al-assad/md-img@master/bucket-2/202004040320.png)

2）分配 Master 角色

![image-20200404034958965](https://cdn.jsdelivr.net/gh/Al-assad/md-img@master/bucket-2/202004040359.png)

3）分配 Slave、Client 角色

![image-20200404035443829](https://cdn.jsdelivr.net/gh/Al-assad/md-img@master/bucket-2/202004040344.png)

4）调节 HBase 参数，注意 HBase 默认的配置参数都很保守，可以在机器允许的情况下，尽可能调高相关参数

其中 Server 的 HMaster Memory、RegionServer Memory 应该适当调高，Client Retries 应该适当调低。

![image-20200404035654805](https://cdn.jsdelivr.net/gh/Al-assad/md-img@master/bucket-2/202004040354.png)

5）等待安装结束，注意 HBase 安装的自检过程会比较久

![image-20200404035909408](https://cdn.jsdelivr.net/gh/Al-assad/md-img@master/bucket-2/202004040309.png)



## 3. FAQ

Ambari 安装 HDP 组件过程中发现问题或疑问，欢迎留言。

