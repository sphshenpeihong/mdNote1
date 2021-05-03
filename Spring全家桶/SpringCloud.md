# 一、SpringCloud

## 1、零基础

### 1.1、父maven项目

1、创建好一个文件夹后新建maven项目

![image-20210219201643538](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210219203505.png)



2、修改文件编码

![image-20210219211211792](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210219211213.png)



3、开启注解

![image-20210219211251274](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210219211252.png)



4、Java编译版本

![image-20210219211436852](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210219211437.png)



### 1.2、微服务项目五步走

```txt
1、建module(聚合关系、父子聚合)
2、改POM
3、写YML
4、主启动类
5、写业务类
```



### 1.3、父pom.xm

```txt
0、由最外层做父pom，则<packaging>标签必须使用pom
1、由于聚合工程采用的是父子聚合，则新建子module的时候，不需要写GV，只需要写A即可，GV直接继承父pom的了
2、父pom里面使用<dependencyManagement>标签只声明版本号，不引入jar包，子module引入相关依赖时，可以不书写version版本号
```

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.atguigu.springcloud</groupId>
    <artifactId>cloud2021</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>pom</packaging>

    <!-- 子module -->
    <modules>
        <module>cloud-provider-payment8001</module>
    </modules>

    <!-- 统一管理jar包版本 -->
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <junit.version>4.12</junit.version>
        <log4j.version>1.2.17</log4j.version>
        <lombok.version>1.16.18</lombok.version>
        <mysql.version>8.0.18</mysql.version>
        <druid.version>1.1.18</druid.version>
        <mybatis.spirng.boot.version>1.3.0</mybatis.spirng.boot.version>
        <druid.spring.boot.starter.version>1.1.10</druid.spring.boot.starter.version>
    </properties>

    <!-- 子模块继承之后，提供作用：锁定版本+子module不用写GV     -->
    <dependencyManagement>
        <dependencies>
            <!-- Springboot 2.2.2 -->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>2.2.2.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>

            <!-- springcloud Hoxton.SR1 -->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR1</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>

            <!-- springcloud alibaba 2.1.0.RELEASE -->
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>2.1.0.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>

            <!-- mysql -->
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>${mysql.version}</version>
            </dependency>

            <!-- druid -->
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>druid</artifactId>
                <version>${druid.version}</version>
            </dependency>

            <!-- springboot整合druid -->
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>druid-spring-boot-starter</artifactId>
                <version>${druid.spring.boot.starter.version}</version>
            </dependency>

            <!-- springboot整合mybatis -->
            <dependency>
                <groupId>org.mybatis.spring.boot</groupId>
                <artifactId>mybatis-spring-boot-starter</artifactId>
                <version>${mybatis.spirng.boot.version}</version>
            </dependency>

            <!-- junit -->
            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <version>${junit.version}</version>
            </dependency>

            <!-- log4j -->
            <dependency>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
                <version>${log4j.version}</version>
            </dependency>

            <!-- lombok -->
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <version>${lombok.version}</version>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <fork>true</fork>
                    <addResources>true</addResources>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>

```



### 1.4、payment（子module）

//省略，自己去看代码



### 1.5、consumer模块（子module）

RestTemplate：RestTemplate提供了多种便捷访问远程Http服务的方法，是一种简单便捷的访问restFul服务模板类，是Spring提供的用于访问Rest服务的客户端模板工具集



> 使用

（url，requestMap，ResponseBean.class）这三个参数分别代表

REST请求地址、请求参数、HTTP响应转换被转换成的对象类型。



### 1.6、Run Dashboard

作用：IDEA中可视化，管理多个微服务的

如果IDEA中没显示的话，则去.idea文件中的workspace.xml文件中添加一行即可

![image-20210220165057903](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210220165059.png)

![image-20210220165121299](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210220165122.png)

```xml
	<option name="configurationTypes">
		<set>
			<option value="SpringBootApplicationConfigurationType" />
		</set>
	</option>
```

//保存完后，重启IDEA即可。



## 2、学前班

### 2.1、基础概念

> Q：什么是服务治理？

​		SpringCloud封装了Netflix公司开发的Eureka模块来实现服务治理。

​		在传统的rpc远程调用框架中，管理每个服务与服务之间依赖关系比较复杂，管理比较复杂，所以需要使用服务治理，管理服务与服务之间依赖关系，可以实现服务调用、负载均衡、容错等，实现服务发现与注册。



> Q：什么是服务注册与发现？

​		Eureka采用了CS的设计架构，Eureka Server作为服务注册功能的服务器，它是服务注册中心。而系统中的其他微服务，使用Eureka的客户端连接到Eureka Server并维持心跳连接。这样系统的维护人员就可以通过Eureka Server来监控系统中各个微服务是否正常运行。

​		在服务注册与发现中，有一个注册中心。当服务器启动的时候，会把当前自己服务器的信息，比如：服务地址通讯地址等以别名方式注册到注册中心上。另一方（消费者/服务提供者），以该别名的方式去注册中心上获取到实际的服务通讯地址，然后再实现本地RPC调用RPC远程调用框架核心设计思想：在于注册中心，因为使用注册中心管理每个服务与服务之间的一个依赖关系（服务治理概念）。在任何RPC远程框架中，都会有一个注册中心（存放服务地址相关信息(接口地址)）



> Eureka包含两个组件：Eureka Server和Eureka Client

**Eureka Server**提供服务注册服务

各个微服务节点通过配置启动后，会在EurekaServer进行注册，这样EurekaServer中的服务注册表中将会存储所有可用服务节点的信息，服务节点的信息可以在界面中直观看到。



**EurekaClient**通过注册中心进行访问

是一个Java客户端，用于简化Eureka Server的交互，客户端同时也具备一个内置的、使用轮询（round-robin）负载算法的负载均衡器。在应用启动后，将会向Eureka Server发送心跳（默认周期为30秒）。如果Eureka Server在多个心跳周期内没有接收到某个节点的心跳，EurekaServer将会从服务注册表中把这个服务节点移除（默认90秒）



### 2.2、RestTemplate

作用：一款HTTP远程调用接口的工具类

#### 2.2.1、交给Spring管理

并且要加上注解@LoadBalanced，赋予RestTemplate负载均衡的能力，**不然无法通过微服务名称映射出服务的IP:端口号**。

```java
@Configuration
public class ApplicationContextConfig {

    // 默认是轮询
    @Bean
    @LoadBalanced //使用@LoadBalanced注解赋予RestTemplate负载均衡的能力
    public RestTemplate getRestTemplate(){
        return new RestTemplate();
    }

}
```



#### 2.2.2、API

getForObject(); //发起HTTP请求，返回结果是一个json串

postForObject(); //发起HTTP请求，返回结果是ResponseEntity对象，包含的不但有json串，还有响应头、响应状态码、响应体等

getForEntity();

postForEntity();



### 2.3、actuator

**问题：**

![image-20210413002039927](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210413002041.png)



**想法：**

左边：主机名称隐藏

右边：自定义名称

左下角：鼠标挪动时显示IP：端口号



**解决步骤**：

```yml
# 加上这两个配置即可
eureka:
  instance:
    # 注册中心隐藏主机名 + 右边自定义名称
    # 微服务集群的话，下面这个实例id不能一样，否则在注册中心只显示一个
    instance-id: payment8001
    # 左下角不显示主机名称，而是显示IP+端口号
    prefer-ip-address: true
```







## 3、初级

### 3.1、单机注册中心(eureka)

#### 3.1.1、pom.xml引入相关依赖

```xml
        <!-- eureka-server -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
```



#### 3.1.2、启动类和yml配置文件

![image-20210221230756262](https://i.loli.net/2021/02/21/P596iaMIkCX478K.png)

// 服务端需要加**@EnableEurekaServer**注解

// 客户端需要加**@EnableEurekaClient**注解



#### 3.1.3、启动项目后的效果

![image-20210221231434676](https://i.loli.net/2021/02/21/CKsNRt6Xfb4wuDS.png)



#### 3.1.4、注册中心底层原理

**1、将服务注册到注册中心**：作为服务端，启动类上方需要加上@EnableEurekaServer声明自己是服务端，该注解本质也是利用@Import注解 去将某个类交给Spring容器管理，而这个类恰好就执行了许多代码的逻辑，比方还提供了addInstance(InstanceInfo .....)方法（利用的是观察者模式），每当有服务需要注册到注册中心服务端的话，那么就会调用这个方法，进行注册。

**2、服务续约**：客户端默认的定时任务30秒调用服务端提供的renewLease()方法，进行服务续约

**3、将服务实例信息同步到其它集群**：addInstance()方法底层不单会缓存服务实例信息在本地缓存registry中，还会去将服务实例的信息同步到其它注册中心服务端集群（当中还会判断实例的lastDirtyTimestramp最后修改时间字段，以时间戳大的时间为准，有点利用了乐观锁的思想）。

**4、服务下线**：当有服务需要从注册中心服务端下线时，也是利用观察者模式调用cancelLease()方法，该方法就会去执行服务下线，并且同步到其它集群环境，并且计数器减1（计数器的作用就是为了计算最低续约的阈值，实现自我保护机制的功能）

**5、服务剔除**：服务端默认的定时任务60秒，会去registry缓存中调用isExpired()方法，筛选出过期没有进行续约的所有服务实例（LeaseInfo里面有续约的时间戳字段、过期时间duration字段），如果判断没有进行续约的服务的过期时间已超过了90秒，那么会进行服务剔除（如果服务端已开启了自我保护机制的话，并且本次需剔除服务的数量的占比大于15%，那么不会全部剔除，只会剔除最多15%）。



### 3.2、集群注册中心(eureka)

需修改的地方分别是注册中心服务端，和客户端。

#### 3.1.1、服务端

服务端的配置文件互相配置，相互守望

![image-20210412230618942](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210412230620.png)



#### 3.1.2、客户端

defaultZone需要同时指向两个注册中心服务端的绝对路径地址

![image-20210412230844579](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210412230845.png)



### 3.3、支付模块集群

支付模块提供接口被调用，做集群的话，就不能使用RestTemplate调用了，因为RestTemplate地址是直接写死的，所以要引入负载均衡的功能。



### 3.4、DiscoveryClient

**作用**：服务注册发现（可以获取到注册中心中所注册的服务清单列表的详细信息）

**概述**：discoveryClient这个bean对象，Spring将需要注册到注册中心的服务的信息都封装到这个对象里边，然后提供方法了获取相关信息

```java
@RestController
@RequestMapping("/payment")
public class PaymentCtl {
    
    @Resource
    private DiscoveryClient discoveryClient;
    
	// discoveryClient对象存储了注册中心中所注册的服务清单详细信息，获取后提供接口对外暴露
    @GetMapping("/discovery")
    public Object discovery() {
        // 获取注册中心中所注册的服务名称列表
        List<String> services = discoveryClient.getServices();
        for (String service : services) {
            System.out.println("服务名称：" + service);
            /*
                服务名称：cloud-order-service
                服务名称：cloud-payment-service
             */
        }

        // 获取指定某个服务的详细信息
        List<ServiceInstance> instances = discoveryClient.getInstances("cloud-payment-service");
        for (ServiceInstance instance : instances) {
            System.out.println(instance.getServiceId() + "，" + instance.getScheme() + "，" + instance.getHost() + "，"
                              + instance.getPort() + "，" + instance.getUri());
            /*
                CLOUD-PAYMENT-SERVICE，null，192.168.43.247，8002，http://192.168.43.247:8002
                CLOUD-PAYMENT-SERVICE，null，192.168.43.247，8001，http://192.168.43.247:8001
             */
        }
        return this.discoveryClient;
    }
}
```



### 3.5、注册中心(zookeeper)

#### 3.5.1、安装zookeeper

**1、linux下载zookeeper压缩包**

```shell
wget https://archive.apache.org/dist/zookeeper/zookeeper-3.4.9/zookeeper-3.4.9.tar.gz
```



**2、解压压缩包**

```shell
tar -zxvf apache-zookeeper-3.4.9.tar.gz
```



**3、关闭linux的防火墙**

```shell
systemctl stop firewalld
```



**4、启动zookeeper**

启动之前先确认所下载的conf目录的zoo.cfg文件的名字是否是其它，是其它的话需要使用mv命令改成zoo.cfg

![image-20210414175017134](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210414175018.png)

**启动**：去bin目录下输入 ./zkServer.sh start



#### 3.5.2、zookeeper具体使用

**1、bin目录具体**

![image-20210414181233451](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210414181234.png)



**2、从zookeeper服务端获取已注册微服务的详细信息**

![image-20210414182027305](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210414182028.png)



#### 3.5.3、zookeeper是临时节点

eureka有自我保护机制，服务断掉后，不会立马在注册中心将其删除，但是zookeeper会（一无心跳，立即删除）。



### 3.6、注册中心(consul)

#### 3.6.1、安装、运行

直接用docker pull拉镜像，然后运行

![image-20210415231904528](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210415231905.png)

![image-20210415231839625](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210415231840.png)



#### 3.6.2、消费者注册进注册中心

套路和eureka、zookeeper差不多。



### 3.7、CAP理论

C：Consistency（强一致性）

A：Availability（可用性）

P：Partition tolerance（分区容错性）

CAP理论关注粒度是数据，而不是整体系统设计的侧罗

AP（Eureka）

CP（Zookeeper/Consul）



### 3.8、Ribbon(负载均衡工具)

#### 3.8.1、简介

Spring Cloud Ribbon是基于Netflix Ribbon实现的一套客户端   （负载均衡的工具）

简单的说，Ribbon是Netflix发布的开源项目，主要功能是提供**客户端的软件负载均衡算法和服务调用**。Ribbon客户端组件提供一系列完善的配置项如连接超时，重试等。简单的说，就是在配置文件中列出Load Banlancer（简称LB）后面所有的机器，Ribbon会自动地帮助你基于某种规则（如简单轮询，随机连接等）去连接这些机器。我们很容易使用Ribbon实现自定义的负载均衡算法。



#### 3.8.2、工作原理

Ribbon在工作时分成两步

- 第一步先选择EurekaServer，它优先选择在同一个区域内负载较少的server
- 第二步再根据用户指定的策略，在从server取到的服务注册列表中选择一个地址。

其中Ribbon提供了多种策略：比如轮询、随机和根据响应时间加权



> RPC远程调用框架的思想

RPC远程调用时，需要你给我提供一个微服务的名称，然后我拿着这个名称去注册中心找相应的，然后利用轮询或其它负载均衡的算法去进行远程调用。



> 新版本的Eureka天生就引入Ribbon了

![image-20210415235717798](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210415235752.png)



#### 3.8.3、代码

注：RestTemplate加了@LoadBalenced，默认是轮询策略，不过使用Ribbon可以更改负载均衡的策略。

**1、自定义IRule负载均衡策略配置类**

```java
package com.atguigu.rule;

import com.netflix.loadbalancer.IRule;
import com.netflix.loadbalancer.RandomRule;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * Ribbon规定不能放在@ComponentScan能扫到的地方，而是定义其它目录，等@RibbonClient注解自己指定
 *
 * @author Shen Peihong
 * @version: 1.0
 * @date 2021/4/19
 */
@Configuration
public class MyRule {

    /**
     * 更改负载RestTemplate的负载均衡的策略（由轮询更改成随机）
     * @return
     */
    @Bean
    public IRule rule() {
        return new RandomRule();
    }

}

```



**2、Order消费者启动类需加注解@RibbonClient**

```java
package com.atguigu.springcloud;

import com.atguigu.rule.MyRule;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
import org.springframework.cloud.netflix.ribbon.RibbonClient;

/**
 * Created by Shen Peihong on 2021/2/20
 * Description:
 *
 * @since 1.0.0
 */
@SpringBootApplication
@EnableEurekaClient
// 指定当调用哪个微服务时，需要更换成自定义负载均衡策略
@RibbonClient(value = "CLOUD-PAYMENT-SERVICE", configuration = MyRule.class)
public class OrderMain80 {

    public static void main(String[] agrs){
        SpringApplication.run(OrderMain80.class, agrs);
    }

}

```



### 3.9、OpenFeign

**作用：**服务间远程调用

**具体使用：**

#### 3.9.1、pom.xml

```xml
    <dependencies>
        <dependency>
            <groupId>com.atguigu.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
        </dependency>

        <!-- eureka客户端 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>

        <!-- 利用openfeign进行服务间的调用 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
    </dependencies>
```



#### 3.9.2、application.yml

```yml
server:
  port: 80

spring:
  application:
    name: comsumer-openfeign-order

eureka:
  client:
    register-with-eureka: true
    service-url:
      defaultZone: http://localhost:7001/eureka/

```



#### 3.9.3、主启动类

```java
@SpringBootApplication
@EnableFeignClients
public class OpenFeignMain80 {
    public static void main(String[] agrs){
        SpringApplication.run(OpenFeignMain80.class, agrs);
    }
}
```



#### 3.9.4、业务类

```java
// 不用@Service，使用了@FeignClietn注解，就会自动地给我们这个类去生成具体的实现类，并交给Spring容器管理了。
@FeignClient(value = "cloud-payment-service")
public interface OpenfeignService {

    @GetMapping(value = "/payment/getPaymentById/{id}")
    public CommonResult getPaymentById(HttpServletRequest request, @PathVariable(name = "id") Long id);

    @GetMapping("/payment/discovery")
    public Object discovery();

}
```



#### 3.9.5、使用总结

**思想：**服务间的调用，需要知道被调服务的服务名称 + 具体接口地址，OpenFeign相当于在消费者模块定义了接口，映射了服务提供者的Controller接口

**使用要点：**

- 引入依赖spring-cloud-starter-openfeign

- 在启动类使用@EnableFeignClients注解，激活打开此功能
- 业务service接口(具体的定义跟需要调的ctl接口头部一致)，使用@FeignClietn注解指向需要调用的服务名称



#### 3.9.6、OpenFeign超时控制

OpenFeign默认1秒超时，可以更改客户端项目配置文件，改变ReadTimeout或ConnectTimeout的值

```yml
ribbon:
  # 指的是建立连接所有的时间，适用于网络状况正常的情况下，两端连接所用的时间
  ReadTimeout: 5000
  # 指的是建立连接后从服务器读取到可用资源所用的时间
  ConnectTimeout: 5000
```



#### 3.9.7、OpenFeign日志打印

**配置类(加个bean)**

```java
package com.atguigu.springcloud.config;

import feign.Logger;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * @author Shen Peihong
 * @version: 1.0
 * @date 2021/4/19
 */
@Configuration
public class FeignConfig {

    /**
     * openFeign进行服务间的调用时，可以指定日志级别(看看自己是否需要在服务间调用时需要打详细的日志)
     *
     * @return
     */
    @Bean
    public Logger.Level feignLoggerLevel() {
        return Logger.Level.FULL;
    }

}

```



**修改配置文件**

```yml
logging:
  level:
    # feign日志以什么级别监控哪个接口
    com.atguigu.springcloud.service.OpenfeignService: debug
```



## 4、中级

### 4.1、Hystrix

#### 4.1.1、服务雪崩

服务之间调用时出错（级联故障）：A -> B -> C -> D



#### 4.1.2、Hystrix是什么？

​		Hystrix是一个用于处理分布式系统的**延迟**和**容错**的开源库，在分布式系统里，许多依赖不可避免的会调用，比如超时、异常等，Hystrix能够保证在一个依赖出问题的情况下，**不会导致整体服务失败，避免级联故障，以提高分布式系统的弹性**。

​		“断路器”本身是一种开关装置，当某个服务单元发生故障之后，通过断路器的故障监控（类似熔断保险丝），**向调用方返回一个符合预期的、可处理的备选响应（FallBack），而不是长时间的等待或者抛出调用方无法处理的异常**，这样就保证了服务调用方的线程不会被长时间、不必要地占用，从而避免了故障在分布式系统中的蔓延，乃至雪崩。



#### 4.1.3、三大作用

##### 4.1.3.1、服务降级（Fallback）

**1、具体作用概述**

"服务器忙，请稍后再试"，不让客户端等待并立刻返回一个友好提示



**2、哪些情况会触发降级？**

- 程序运行异常（下标越界、分母为0等）

- 超时
- 服务熔断触发服务降级

- 线程池/信号量打满也会导致服务降级



##### 4.1.3.2、服务熔断（break）

**1、具体作用概述**

类似保险丝打到最大服务访问后，直接拒绝访问，拉闸断电，然后调用服务降级的方法并返回友好提示

服务降级 -> 进而熔断 -> 恢复调用链路



##### 4.1.3.3、服务限流（flowlimit）

**1、具体作用概述**

秒杀高并发等操作，严禁一窝蜂的过来拥挤，大家排队，一秒钟N个，有序进行



#### 4.1.4、服务降级代码

##### 4.1.4.1、服务端

**1、pom.xml**

```xml
    <dependencies>
        <dependency>
            <groupId>com.atguigu.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
            <version>2.2.1.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
        </dependency>
    </dependencies>
```



**2、application.yml**

```yml
server:
  port: 8008

spring:
  application:
    name: cloud-payment-hystrix

eureka:
  client:
    register-with-eureka: true
    fetch-registry: true
    service-url:
      defaultZone: http://localhost:7001/eureka/
```



**3、主启动类**

```java
@SpringBootApplication
@EnableEurekaClient
// 激活Hystrix功能
@EnableHystrix
public class PaymentHystrixMain8008 {

    public static void main(String[] agrs){
        SpringApplication.run(PaymentHystrixMain8008.class, agrs);
    }

}
```



**4、service方法**

```java
package com.atguigu.springcloud.service.impl;

import com.atguigu.springcloud.service.HystrixService;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixProperty;
import org.springframework.stereotype.Service;

import java.util.concurrent.TimeUnit;

/**
 * @author Shen Peihong
 * @version: 1.0
 * @date 2021/4/22
 */
@Service
public class HystrixServiceImpl implements HystrixService {
    @Override
    public String ok(String id) {
        return "哈哈OK：id = " + id;
    }

    // 服务降级，当该方法执行的时间超过1.5秒，则直接调用降级的方法
    @HystrixCommand(fallbackMethod = "fallBack", commandProperties = {
            @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "1500")
    })
    @Override
    public String timeOut(String id) {
        long i = 3;
        try {
            TimeUnit.SECONDS.sleep(i);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return "哈哈timeOut： id = " + id;
    }

    public String fallBack(String id){
        return "呜呜呜呜，不太行， id = " + id;
    }
}

```



##### 4.1.4.2、客户端

**1、Feign接口**

```java
package com.atguigu.springcloud.service;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;

/**
 * @author Shen Peihong
 * @version: 1.0
 * @date 2021/4/22
 */
@FeignClient(value = "CLOUD-PAYMENT-HYSTRIX")
public interface FeignPaymentService {

    @GetMapping("/hystrix/ok")
    public String ok(@RequestParam(name = "id") String id);

    @GetMapping("/hystrix/timeOut")
    public String timeOut(@RequestParam(name = "id") String id);

}

```



**2、Controller**

```java
package com.atguigu.springcloud.controller;

import com.atguigu.springcloud.service.FeignPaymentService;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixProperty;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;

/**
 * @author Shen Peihong
 * @version: 1.0
 * @date 2021/4/22
 */
@RestController
public class HystrixOrderController {

    @Resource
    private FeignPaymentService feignPaymentService;

    @GetMapping("/order/feign/ok")
    public String ok(String id){
        return feignPaymentService.ok(id);
    }

    @HystrixCommand(fallbackMethod = "fallBack1", commandProperties = {
            @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "50000")
    })
    @GetMapping("/order/feign/timeOut")
    public String timeOut(String id){
        long current = System.currentTimeMillis();
        String timeOut = feignPaymentService.timeOut(id);
        System.out.println((System.currentTimeMillis() - current) / 1000);
        return timeOut;
    }

    public String fallBack1(String id) {
        return "我是客户端，我的要求高一点，超时啦1111111";
    }

}

```



**3、启动类**

```java
@SpringBootApplication
// 服务客户端
@EnableEurekaClient
// 开启openFeign功能
@EnableFeignClients
// 客户端侧开启hystrix
@EnableHystrix
public class ComsumerHystrixOrder80 {

    public static void main(String[] agrs){
        SpringApplication.run(ComsumerHystrixOrder80.class, agrs);
    }

}

```



**4、application.yml**

```yml
feign:
  # 支持在客户端使用hystrix做服务降级
  hystrix:
    enabled: true
  # 设置openFeign的读取、连接超时时间
  client:
    config:
      default:
        read-timeout: 5000
        connect-timeout: 5000

# 设置客户端服务降级默认的超时时间 （客户端使用hystrix，一定要配上默认的超时时间，否则可能存在疯狂调兜底方法）
hystrix:
  command:
    default:
      execution:
        isolation:
          thread:
            timeoutInMilliseconds: 5000
```



##### 4.1.4.3、全局fallBack

注意：

- 一个Ctl对应一个全局兜底，不同Ctl之间不能共用
- 全局兜底方法必须无形参，然后返参一定要跟所调方法的返参一致（单个方法的服务降级兜底方法就必须方法签名一致）

```java
package com.atguigu.springcloud.controller;

import com.atguigu.springcloud.service.FeignPaymentService;
import com.netflix.hystrix.contrib.javanica.annotation.DefaultProperties;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixProperty;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;
import javax.servlet.http.HttpServletRequest;

/**
 * @author Shen Peihong
 * @version: 1.0
 * @date 2021/4/23
 */
@RestController
// 指明指向的全局兜底方法
@DefaultProperties(defaultFallback = "defaultFallback")
public class HystrixOrder2Controller {

    @Resource
    private HttpServletRequest request;

    @Resource
    private FeignPaymentService feignPaymentService;

    @GetMapping("/order/feign/ok2")
    public String ok(String id){
        return feignPaymentService.ok(id);
    }

    @HystrixCommand
    @GetMapping("/order/feign/timeOut2")
    public String timeOut(String id){
        int i = 10/0;
        long current = System.currentTimeMillis();
        String timeOut = feignPaymentService.timeOut(id);
        System.out.println((System.currentTimeMillis() - current) / 1000);
        return timeOut;
    }

    /**
     * 兜底方法（不能带形参）
     *
     * @return
     */
    public String defaultFallback() {
        System.out.println("全局fallback来接收");
        return "全局fallback来接收";
    }

}

```



##### 4.1.4.4、feign接口fallBack

**1、feign接口**

```java
package com.atguigu.springcloud.service;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;

/**
 * @author Shen Peihong
 * @version: 1.0
 * @date 2021/4/22
 */
// fallback书写承包feign接口fallback的方法的类的反射
@FeignClient(value = "CLOUD-PAYMENT-HYSTRIX", fallback = PaymentFallBackService.class)
public interface FeignPaymentService {

    @GetMapping("/hystrix/ok")
    public String ok(@RequestParam(name = "id") String id);

    @GetMapping("/hystrix/timeOut")
    public String timeOut(@RequestParam(name = "id") String id);

}

```



**2、feign接口实现类**

```java
package com.atguigu.springcloud.service;

import org.springframework.stereotype.Component;

/**
 * @author Shen Peihong
 * @version: 1.0
 * @date 2021/4/25
 */
// 记得交给Spring容器管理
@Component
public class PaymentFallBackService implements FeignPaymentService {
    @Override
    public String ok(String id) {
        return "ok,承包feign接口的降级兜底方法，" + id;
    }

    @Override
    public String timeOut(String id) {
        return "timeOut,承包feign接口的降级兜底方法，" + id;
    }
}

```



**3、Controller**

```java
package com.atguigu.springcloud.controller;

import com.atguigu.springcloud.service.FeignPaymentService;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;

/**
 * @author Shen Peihong
 * @version: 1.0
 * @date 2021/4/25
 */
@RestController
public class HystrixOrder3Controller {

    @Resource
    private FeignPaymentService feignPaymentService;

    // 由于fallback是在feign接口上大做文章，故Controller层不需要再加任何注解了
    @GetMapping("/order/feign/ok3")
    public String ok(String id){
        return feignPaymentService.ok(id);
    }

}

```



#### 4.1.5、服务熔断

**理论**：熔断机制是应对雪崩效应的一种微服务链路保护机制，当扇出链路的某个微服务出错不可用或者响应时间太长时，会进行服务的降级，进而熔断该节点微服务的调用，快速返回错误的响应信息。

**当检测到该节点微服务调用响应正常后，恢复调用链路**

在Spring Cloud框架中，熔断机制通过Hystrix实现。Hystrix会监控微服务间调用的状况，

当失败的调用到一定阈值，缺省是5秒内20次调用失败，就会启动熔断机制。熔断机制的注解是@HystrixCommand



熔断思想：达到阈值，先Close跳闸(打开熔断)，再慢慢进入半开状态Half-Open，最后好了慢慢继续开启Open

**流程：服务降级 -> 服务熔断 -> 恢复调用链路**

```java
	@Override
    // 服务熔断，十次请求内，如果访问异常，先调用服务降级的方法，超过阈值的话，即使你不异常了，也同样会调用降级方法
    // 慢慢地就会恢复调用链路了  (属性都在HystrixCommandProperties类中定义)
    // 服务降级 -> 服务熔断 -> 恢复调用链路
    @HystrixCommand(fallbackMethod = "fallbackMethod", commandProperties = {
            @HystrixProperty(name = "circuitBreaker.enabled", value = "true"), // 开启断路器
            @HystrixProperty(name = "circuitBreaker.requestVolumeThreshold", value = "10"), // 十次请求
            @HystrixProperty(name = "circuitBreaker.sleepWindowInMilliseconds", value = "10000"), // 时间窗口期
            @HystrixProperty(name = "circuitBreaker.errorThresholdPercentage", value = "60") // 阈值：百分之六十
    })
    public String curcuitBreakerOK(Integer id) {
        if (id <= 0){
            throw new RuntimeException("RuntimeException1111");
        }
        return "啦啦啦啦啦啦啦啦啦啦，" + id;
    }

    String fallbackMethod(Integer id){
        return "不太行" + id;
    }
```



#### 4.1.6、HystrixDashBoard

作用：豪猪哥做服务监控

**1、pom.xml**

```xml
        <!-- 引入hystrix豪猪哥 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
        </dependency>
```



**2、启动类**

```java
@SpringBootApplication
@EnableHystrixDashboard
public class HystrixDashboardMain9001 {

    public static void main(String[] agrs){
        SpringApplication.run(HystrixDashboardMain9001.class, agrs);
    }

}
```



**3、被监控类需要加此依赖包和此Bean**

```xml
        <!-- 监控 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
```

```java
    @Bean
    public ServletRegistrationBean getServlet() {
        HystrixMetricsStreamServlet streamServlet = new HystrixMetricsStreamServlet();
        ServletRegistrationBean registrationBean = new ServletRegistrationBean(streamServlet);
        registrationBean.setLoadOnStartup(1);
        registrationBean.addUrlMappings("/hystrix.stream");
        registrationBean.setName("HystrixMetricsStreamServlet");
        return registrationBean;
    }
```



**4、豪猪哥页面**

![image-20210425224311441](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210425224312.png)



## 5、Gateway(网关)

### 5.1、作用

网关 （一开始用zuul多一点，Netflix开发的，不过现在被Spring的Gateway取代了）

**路由转发 + 执行过滤器链**



### 5.2、优势

底层使用Spring的Webflux响应式编程开发的，基于Netty（基于NIO）的异步非阻塞模型开发的。



### 5.3、三大概念

#### 5.3.1、Route(路由)

路由是构建网关的基础模块，它由ID、目标URI，一系列的断言和过滤器组成，如果断言为true，则匹配该路由

**（对请求做路由转发的）**



#### 5.3.2、Predicate(断言)

参考的是Java8的java.util.function.Predicate

开发人员可以匹配HTTP请求中的所有内容（例如请求头或请求参数），**如果请求与断言相匹配，则进行路由转发**



#### 5.3.3、Filter(过滤)

指的是Spring框架中**GatewayFilter**的实例，使用过滤器，可以在请求被路由前或者之后对**请求**进行修改。

**请求的生命周期**：pre（路由转发前）、post（路由转发之后）



#### 5.3.4、三大概念总结

web请求，通过一些匹配条件，定位到真正的服务节点。并在这个转发过程的前后，进行一些精细化控制

predicate就是我们的匹配条件；

而filter就可以理解为一个无所不能的拦截器。有了这两个元素，再加上目标uri，就可以实现一个具体的路由了。



### 5.4、代码实战

> 1、yml配置文件

```yml
server:
  port: 9527

spring:
  application:
    name: cloud-gateway-servce
  cloud:
    gateway:
      # lb 负载均衡 (貌似不加这个，下面也可以负载均衡成功)
      #discovery:
      #  locator:
      #    enabled: true
      routes:
        - id: cloud-payment
          # 转发地址
          uri: lb://cloud-payment-service
          # 断言
          predicates:
            - Path=/cloud-payment/**
        - id: cloud-order
          uri: lb://cloud-order-service
          predicates:
            - Path=/cloud-order/**
            # 说明请求得带上Cookie，并且需要有属性username=123（右边是正则表达式，可以定值，也可以变值），如果不按要求传，断言匹配不到直接404
            # - Cookie=username,123
            # - Header=X-Request-Id, \d+
            # - Method=Get
            # 需要入参有username，并且对应的值要为整数才行
            # Query=username, \d+

          # 网关过滤器(可以在路由转发前后做一些事情,一些常常干的事情，可以使用GatewayFilter提供的，如下添加请求参数)
          # 但是我们一般都是自定义过滤器，把想做的事情都用代码写出来，比如全局日志记录、鉴权
          filters:
            # 过滤器工厂会在匹配的请求参数上加上一对请求参数，名称为X-Reuqest-Id，值为1024
            - AddRequestParameter=X-Request-Id,1024
eureka:
  instance:
    hostname: ${spring.cloud.client.ip-address}
    instance-id: ${spring.cloud.client.ip-address}:${spring.application.name}:${server.port}
    prefer-ip-address: true
  client:
    register-with-eureka: true
    fetch-registry: true
    service-url:
      defaultZone: http://localhost:7001/eureka/

```



> 使用代码配置路由转发规则

```java
package com.atguigu.springclou.config;

import org.springframework.cloud.gateway.route.RouteLocator;
import org.springframework.cloud.gateway.route.builder.RouteLocatorBuilder;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * 配置网关有两种方式
 * 1、yml文件
 * 2、@Bean的形式：RouteLocator路由定位器
 * 总结：
 * 作用就像nginx一样，拦截一个请求，然后断言判断，如何的话，进行网络段转发
 *
 * @author Shen Peihong
 * @version: 1.0
 * @date 2021/4/27
 */
@Configuration
public class GatewayConfig {

    /**
     * 配置路由转发规则
     *
     * @param routeLocatorBuilder
     * @return
     */
    @Bean
    public RouteLocator routeLocator(RouteLocatorBuilder routeLocatorBuilder){
        // 断言只要匹配上（一般/前缀/**），那么就会去转发到指望的网段（请求后缀仍然不变，拼接到转发网段之后）
        RouteLocatorBuilder.Builder routes = routeLocatorBuilder.routes();
        routes.route("cloud-eureka", r -> r.path("/").or().path("/eureka/**")
                    .uri("http://localhost:7001"));
        return routes.build();
    }

}

```



> 代码配置全局过滤器

```java
package com.atguigu.springclou.filter;

import lombok.extern.slf4j.Slf4j;
import org.springframework.cloud.gateway.filter.GatewayFilterChain;
import org.springframework.cloud.gateway.filter.GlobalFilter;
import org.springframework.core.Ordered;
import org.springframework.stereotype.Component;
import org.springframework.web.server.ServerWebExchange;
import reactor.core.publisher.Mono;

/**
 * 网关过滤器
 *
 * @author Shen Peihong
 * @version: 1.0
 * @date 2021/4/28
 */
@Component
@Slf4j
public class GatewayFilter implements GlobalFilter, Ordered {
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        log.info("接收到请求，当前请求地址为：" + exchange.getRequest().getPath());
        return chain.filter(exchange);
    }

    @Override
    public int getOrder() {
        return 0;
    }
}

```





## 5、高级