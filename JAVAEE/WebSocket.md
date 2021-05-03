# 一、WebSocket

## 1、概念

长连接，**双向**通信



## 2、代码实战

###  2.1、原始代码

#### 2.1.1、引入jar包

```xml
		<dependency>
			<groupId>javax.websocket</groupId>
			<artifactId>javax.websocket-api</artifactId>
			<version>1.1</version>
			<scope>provided</scope>
		</dependency>
```



#### 2.1.2、书写的Websocket类需加注解

```java
// 到时候会扫描到这个类，成为websocket实例对象
@ServerEndpoint(value = "/op/hr/learn/live/{liveId}/{userId}/{websocketLoginCode}")
```



#### 2.1.3、相关注解理解

@OnOpen、@OnClose、@OnMessage、@Error

> @OnOpen

建立连接时触发



> @OnClose

释放连接时触发



> @OnMessage

客户端推消息时触发



> @Error

出现错误时，触发



#### 2.1.4、Session

封装本次连接的会话对象，可以利用该对象获取客户端的相关信息，也可以调API主动往客户端推信息



### 2.2、SpringBoot

#### 2.2.1、引入jar包



