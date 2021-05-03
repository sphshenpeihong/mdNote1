# 一、Spring

## 1、IOC和DI

### 1.1、IOC和DI基础概念

**1. IOC：控制反转**

- 反转：创建对象这份工作由我们自己执行反转给Spring帮我们执行

- 控制：就是由我们负责创建、管理、销毁对象，掌控对象的生命周期等，我们需要使用对象的时候跟Spring申请即可

IOC是一种编程思想，也是一种新的设计模式，它需要DI（依赖注入）的支持（Web依赖Service、Service依赖DAO 比方说Service不注入DAO的话，那么getBean关于Service就是一个空的对象）



**2. DI：依赖注入**

- 依赖注入：交给Spring管理的bean对象，可以对类的成员变量(属性)进行注入

// 切记：不可以对static变量进行注入哈  因为涉及到JVM对不同变量的加载顺序问题



### 1.2、Java代码获取bean对象

Spring是一个容器，它将帮我们管理对象

```java
	ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");//根据spring配置文件获取容器对象
	User user = ac.getBean(User.class, "name");//利用操作配置文件的对象获取bean
```



### 1.3、配置bean（XML）

**Bean元素**：交由Spring管理的对象都要配置在bean标签中

​	i. bean标签介绍和创建方式：**空参构造**、静态工厂、动态工厂

​	ii. Scope属性介绍：Singleton、protoptype、reuqest、session

​	iii. 初始化方法init-method和销毁方法destroy-method介绍

**Spring的XML配置文件applicationContext.xml   (小试牛刀)**

```xml
    <!-- applicationContext配置的所有bean都会在容器创建的时候被创建出来
        如果配置的bean较多，那么在创建容器的时候，会产生内存过大的问题 -->
    <!-- name是起一个名字，我们可以通过这个name来利用容器获取对象 name可以使用特殊字符 name可以重复
        id和name作用基本相同，但不推荐使用 不支持特殊字符，不能重复 -->
    <!-- bean标签创建对象，使用空参构造方法，所以使用这种方式，需要该类有空参的构造方法 -->
    <!-- 1.不想我们创建spring容器的时候加载bean的话，可以使用lazy-init 延迟加载 -->
    <!-- 2.scope属性：默认singleton(单例) ，可选：prototype(多例 每次创建都是新的对象，使用strust创建action需使用到多例) 若使用singleton的话，那么创建多次对象都是同一对象 -->
    <!-- 3.init-method属性：容器对象创建时执行 -->
    <!-- 3.destroy-method属性：容器对象创建时执行 -->
<bean name="user" class="com.sph.bean.User" lazy-init="true" scope="singleton" init-method="init" destroy-method="destroy"/>
```



> 开发过程当中，肯定不会说只写一个Spring配置文件(applicationContext.xml)，可以写多个，然后在一个主配置文件中导入

```xml
<import resource="applicationContext_Annotation.xml"/>
```

```txt
注：一个项目中只需要创建一个Spring容器就足够了，不需要我们每次需要用到Spring容器的时候去创建，所以我们在Web.xml中配置Spring容器自启动（已有ContextLoadListener类的初始化方法去封装好新建Spring容器的代码，所以加载这个类的时候，就会去新建Spring容器，所以我们只需要让Tomcat启动的时候，加载这个类即可）
利用application域（利用监听器，实现对应的接口），ServletContext()的生命周期 随着Web项目的启动而创建 随着Web项目关闭而销毁
```



### 1.4、属性DI注入（XML）

三种场景：**set方法注入**、构造方法注入、复杂类型注入

​	**i. set方法注入**（可以注入基本类型，和引用类型(引用类型需要在Spring中配置依赖的bean)）

```xml
    <bean name="user" class="com.sph.bean.User" lazy-init="true" scope="singleton" init-method="init" destroy-method="destroy">
        <!-- set方法注入(给该类的对象注入属性)，前提是该类中有该set方法 -->
        <property name="u_id" value="123"></property>
        <!-- 这里是引用类型的注入 ref 依赖于pet配置的bean，这里获取pet配置的bean的对象-->
        <property name="pet" ref="dog"></property>
    </bean>
    <!-- 包装类也是依赖的一种，把pet对象交由spring容器管理-->
    <bean name="dog" class="com.sph.bean.Pet">
        <property name="pet_id" value="007"></property>
        <property name="pet_name" value="big_yellow"></property>
    </bean>
```

​	**ii. 构造函数注入**

```xml
    <!-- 使用构造方法的方式注入赋值(set方法注入是用property属性，构造方法是用constructor-arg) -->
    <!-- 使用构造方法注入的话，构造方法有多少个参数，就需要赋值多少个，一一对应呢 -->
    <!-- 注1：如果说构造方法形参的名字一样，但是类型不一样的话，需要使用属性type指定类型呢 -->
    <!-- 注2：如果构造方法的类型都一样，但是位置不一样的话，那我们需要使用index属性指定位置呢 -->
    <bean name="user1" class="com.sph.bean.User">
        <constructor-arg name="u_id" value="9527" type="java.lang.Integer"/>
        <constructor-arg name="u_password" value="9527password"/>
        <constructor-arg name="u_username" value="9527username"/>
        <constructor-arg name="pet" ref="dog"/>
    </bean>
```

​	**iii. 复杂类型注入**：Array、List、Map、Properties

```xml
	<bean name="collection" class="com.sikiedu.bean.MyCollection">
		<property name="array">
			<array>
				<value>123</value>
				<value>1笑王</value>
				<ref bean = "dog" />
			</array>
		</property>
		<property name="list">
			<list>
				<value>24s</value>
				<value>1cacz王</value>
				<ref bean = "dog" />
			</list>
		</property>
		<property name="set">
			<set>
				<value>24s</value>
				<value>1cafcscz王</value>
				<ref bean = "dog" />
			</set>
		</property>
	<!-- 	这里的引用类型可以因为上面的name是唯一标识 class指定了引用的类型 里面的property属性也即是spring创建完对象后的属性注入
		所以直接引用类型的对象在xml中写bean的时候 因为存在于spring容器中 而且property也写了 代表已完成属性注入
		除非延迟加载 声明该bean的属性 lazy-init的值为true -->
		<property name="map">
			<map>
				<entry key="1" value="笑话"></entry>
				<entry key="2" value-ref="dog"></entry>
				<entry key-ref="user1" value-ref="dog" />
			</map>
		</property>
	<!-- 	properties也是键值对 但是值只能是String类型 不可以是引用类型  在这里写键和值 key有标签 值直接写在后面的内容当中-->
		<property name="prop">
			<props>
				<prop key="name">小明</prop>
				<prop key="password">123</prop>
			</props>
		</property>
	</bean>
```



### 1.5、配置bean（注解）

非Springboot项目的话，我们需要在Spring的配置文件中开启组件扫描，扫描某些包下面的注解(@Component、@Service、@Controller)

> 注：@Component一分为三，具体看是哪层(Web、Service、DAO)就用哪个注解，功能一样@Controller() @Service() @Repository()

```xml
    <!-- 注解开发 -->
    <!-- 开启组件扫描 base-package 扫描该包下以及子包的所有注解 -->
    <context:component-scan base-package="com.sph.bean"/>
```

在实体类上添加注解声明，声明该类生成bean对象交给Spring容器管理：

```java
//<bean name="user" class="com.sph.bean.User">
//XML配置文件，是用上面的bean标签来实现把一个类交由spring容器帮我们管理
//如果我们相把这个类交由spring容器帮我们管理的话，使用注解方式，就用@Component("user") 进行声明，到时候注解扫描的时候，就会扫描到这里
@Component("user")
```



> 上面的方法是直接一个类加个注解就是一个bean对象了，那我们能不能在一个类里面配置多个bean呢？是可以的

**// 在一个配置类里面配置bean，若该bean在Spring容器中已有，那么则是修改，若该bean在Spring容器中没有，那么则是新增**

```java
package com.sph.practice.test.jedis.utils;

import com.fasterxml.jackson.annotation.JsonAutoDetect;
import com.fasterxml.jackson.annotation.PropertyAccessor;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.Jackson2JsonRedisSerializer;
import org.springframework.data.redis.serializer.StringRedisSerializer;

/**
 * Created by Shen Peihong on 2020/9/17 1:49
 * Description: 进行序列化处理
 *
 * @since 1.0.0
 */
@Configuration
public class RedisConfig {
    
    /**
     * redisTemplate 序列化使用的jdkSerializeable, 存储二进制字节码, 所以自定义序列化类
     * @param redisConnectionFactory
     * @return
     */
    @Bean
    public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {

        RedisTemplate<Object, Object> redisTemplate = new RedisTemplate<>();
        redisTemplate.setConnectionFactory(redisConnectionFactory);

        // 使用Jackson2JsonRedisSerialize 替换默认序列化
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);

        ObjectMapper objectMapper = new ObjectMapper();
        objectMapper.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        objectMapper.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);

        jackson2JsonRedisSerializer.setObjectMapper(objectMapper);

        // 设置value的序列化规则和 key的序列化规则
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        redisTemplate.setValueSerializer(jackson2JsonRedisSerializer);
        redisTemplate.setHashKeySerializer(new StringRedisSerializer());
        redisTemplate.setHashValueSerializer(jackson2JsonRedisSerializer);
        redisTemplate.afterPropertiesSet();
        return redisTemplate;
    }
}
```



### 1.6、属性DI注入（注解）

```java
//创建完spring容器对象后，可以在这个类中任意地获取spring容器的对象，也可以任意地对spring容器中的对象进行注入
@RunWith(SpringJUnit4ClassRunner.class) //不是创建Spring容器 制造Spring容器环境
@ContextConfiguration("classpath:applicationContext_Injection.xml") //读取spring的配置文件（创建spring容器）
public class AnnotationDI {

    // 引用类型注入
    @Resource(name="userService")
    UserService us; //该类是已经交由spring管理了的，我们这里创建了spring容器对象后就可以对它进行操作

    // 基本类型注入
    @Value("${is_open}")
    public boolean isOpen;
}
```



## 2、Spring-AOP(XML)

### 2.1、AOP基础概念

**AOP是一种思想**

面向切面编程；横切（抽取重复代码） 例：比如去购物，向购物商品、添加到购物车、收藏商品，都需要先执行**身份验证**，则可以使用过滤器Filter

> 利用AOP思想的具体应用

过滤器、Springmvc的前端控制器、开启事务,提交/回滚事务(实现方式：动态代理)



### 2.2、Spring中的aop思想

基于AOP这种思想，Spring无需我们自己写动态代理的代码，Spring可以将容器中管理对象生成动态代理对象，前提是我们对它进行一些设置（比如Myabtis中Sqlsession对象调用getMapper(UserMapper.class)获取到的Mapper对象可不是普通的对象，底层是用了动态代理，生成了增强代理对象，它会自动开启事务-提交事务-回滚事务等操作）



### 2.3、Spring aop相关名词说明

​	a) Proxy动态代理：被代理的对象必须实现接口

​	b) Cglib动态代理：被代理的对象不能被final修饰，基于继承



### 2.4、Spring aop相关名词说明

> AOP名词介绍

JoinPoint（连接点）：目标对象中，哪些方法会被拦截：save、delete、update、find（都可以被增强的方法，叫连接点）

**PonitCut**（切入点）：筛选连接点，你最终要增强的方法：save、delete、update（筛选连接点，筛选中的就叫做切入点）

**Advice**（通知/增强）：增强的代码（开启事务、提交事务）

Introduction（介入/引入）：在执行时期动态加入一些方法或行为

**Aspect**（切面）：通知和切入点，通知应用到哪个切点（比如将事务应用到增删改）

target（目标）：被代理对象（被代理对象 UserService）

weaving（织入）：把切面的代码应用到目标对象来创建新的代理对象的过程，同样是UserService类型的对象，但是使用完织入（把切面的代码应用到被代理对象）后，这个对象被增强了（使用了动态代理加了增强代码）

Proxy（代理）：把切面的代码应用到目标对象来创建新的代理对象



### 2.5、了解Spring中的aop事务

>  事务相关知识

​	**a) 什么是事务**：把多条数据库操作捆绑到一起执行，要么都成功，要么都失败；

​	**b) 事务的原则 ACID**：

​		i. 原子性（Atomicity）：事务包含的所有操作，要么全部成功，要么全部失败回滚，成功全部应用到数据库，失败的话，也不能对数据													库有任何影响   

​		ii. 一致性（Consistency）：事务在执行前和执行后必须一致；例如A和B一共有100块钱，无论A、B之间如何转账，他们的钱始终														相加都是100

​		iii. 隔离性（Isolation）：多用户并发访问同一张表适，数据库为每一个用户开启新的事务，该事务不能被其他事务所影响，相互有隔													离；

​		iv. 持久性（Durability）：一个事务一旦提交，则对数据库中的数据的改变是永久的，即便系统故障也不会丢失；

​	**c) 并发可能引起的问题**：

​		i. 脏读：一个事务读取到另一个事务未提交的数据；

​		ii. 不可重复读：一个事务读取到另一个事务已提交（Update操作）的数据，导致前后读取不一致

​		iii. 幻读（虚读）：一个事务中读取到别的事务插入（Insert操作的数据），导致前后读取不一致；

​	**d) 事务的隔离级别：**根据实际情况选择：

​		i. Serializable 串行化：可避免脏读、不可重复读和幻读；

​		ii. Repeatable read 可重复读：可避免脏读、不可重复读；（Mysql默认值）

​		iii. Read committed 读已提交：可避免脏读； 

​		iv. Read uncommitted 读未提交：任何情况都无法保证；



> Spring-aop事务：搭建环境

​	a) 事务基本操作：打开事务、提交事务、回滚事务；

​	b) Spring中利用接口来管理不同框架的事务操作；

​		i. 通过实现PlatformTransactionManager接口支持不同的框架完成各自的事务处理；

​		ii. 为不同平台提供对应的事务管理器的实现：    JDBC&Myabtis：DataSourceTransactionManager；

​	c) Spring-aop事务通过配置事务的隔离级别、事务传播行为、是否只读来操作；

​		i. 隔离级别：串行化、可重复读、读已提交、读未提交；

​		ii. 是否只读：

​			1. true：不可改变数据库中的数据，查询操作推荐

​			2. false：可以改变数据库数据

​		iii. 事务传播行为：事务方法嵌套调用的规则：

​			xService.x();     ->     yService.y();

​			1. REQUIRED：如果当前没有事务，就创建一个新事务，如果当前存在事务，就加入该事务，该设置是最常用的设置；



### 2.6、Spring aop配置

a) 导包

b) 自定义通知(增强)，五种自定义通知类型：

​	i. before 前置通知

​	ii. after 最终通知（后置通知）

​	iii. afterReturning 成功通知（后置通知）

​	iv. afterThrowing 异常通知（后置通知）

​	v. around 环绕通知

c) 配置applicationContext.xml

**Spring利用XML配置文件配置AOP有两种方式：**

//企业开发中经常用第二种，第一种我们自己玩玩而已

1. 下面的配置没引入AOP事务（自己自定义各通知需执行什么操作）

```xml
<!-- 	目标对象 -->
<bean name="UserService" class="com.sikiedu.service.UserServiceImpl"></bean>
<!-- 	通知对象 -->
<bean name="MyAdvice" class="com.sikiedu.aop.MyAdvice"></bean>	

<aop:config>
	<!-- 切入点(目标方法) 书写切入点表达式 格式为:public 类型 方法全包名   书写切入点的id是为了切面 因为切面是：通知+切入点 -->
	<!-- 配置切入点的作用 主要是为了配置想要增强哪些方法 -->
	<!-- 切入点就是书写中间到底哪部分想增强的方法 一般写全部* 后面 -->
	<!-- 			切入点表达式：可以配置要增强的方法    id是唯一标识-->
	<aop:pointcut expression="execution(* com.sikiedu.service.*ServiceImpl.*(..))" id="servicePC"/>

	<!-- 切面就是（通知+切入点）  把通知应用到哪个切入点 -->
	<!-- ref为通知对象的Name -->
	<aop:aspect ref="MyAdvice" >
		<!-- 	前置通知  即执行增强的方法前要做的事情-->
		<aop:before method="before"  pointcut-ref="servicePC"/>
		<!-- 无论成功或不成功都要调用方法 后置通知 -->
		<aop:after method="after"  pointcut-ref="servicePC"/>
		<!-- 	成功通知 后置通知 -->
		<aop:after-returning method="afterReturning"  pointcut-ref="servicePC"/>
		<!-- 异常通知 后置通知 -->
		<aop:after-throwing method="afterThrowing"  pointcut-ref="servicePC"/>
		<!-- 环绕通知 -->
		<aop:around method="around"  pointcut-ref="servicePC"/>
	</aop:aspect>
</aop:config>

```



2. 下面的AOP配置引入事务，这也是企业中经常使用的一种配置方式

```xml
	<!-- 事务通知 -->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <!-- name是需要进行事务处理的方法名 -->
            <!-- 通知这里只配一些查询的，然后设置只读，因为默认是可写可读 -->
            <!--<tx:method name="handleAccount" isolation="DEFAULT" propagation="REQUIRED" read-only="true"/>-->
            <tx:method name="find*" read-only="true"/>
            <tx:method name="get*" read-only="true"/>
            <tx:method name="load*" read-only="true"/>
            <tx:method name="list*" read-only="true"/>
            <tx:method name="count*" read-only="true"/>
            <tx:method name="search*" read-only="true"/>
            <tx:method name="pageList*" read-only="true"/>
            <!-- 有异常时进行回滚事务 -->
            <tx:method name="*" rollback-for="Throwable"/>
        </tx:attributes>
    </tx:advice>
    
    <!--切面 = 切点 + 通知-->
    <aop:config>
        <!-- 切点表达式使用bean(*Service) 即是：所以符合*Service命名的bean对象 -->
        <aop:pointcut id="txPC" expression="bean(*Service)"/>
        <aop:advisor advice-ref="txAdvice" pointcut-ref="txPC"/>
    </aop:config>
```



3. 利用注解配置事务

```java
@Transactional
// 在类或方法上面添加该注解即可，不过这种不如XML配置。。。XML配完后，直接一劳永逸。
```



## 3、Spring-AOP(注解)

### 3.1、AOP注解的原理

#### 3.1.1、@EnableAspectJAutoProxy

Q：@EnableAspectJAutoProxy是什么？

A：该注解里面使用了@Import(AspectJAutoProxyRegistrar.class)，给Spring容器中导入AspectJAutoProxyRegistrar组件(AnnotationAwareAspectJAutoProxyCreator)，那为啥注册完这个bean就能开启强大的功能了呢？具体需要看注册这个组件里面干了什么，我们发现这个组件的祖先类，其中有一个类实现了后置处理器接口以及Aware接口。

- 我们知道如果实现了这个后置处理器的接口，并重写了before和after的后置处理器方法的话，到时候Spring去加载你这个bean的时候，判断你用实现了这些接口的话，会去执行你重写的方法的(一般是处理这个bean的情况的。)。
- 我们知道如果实现了Aware接口的话，那到时候我们就可以拿到许多组件（比如ApplicationContext、环境相关、国际化相关等，这些到时候都是通过传参，传给我们重写接口的方法）



由上面我们知道@EnableAspectJAutoProxy注解本质就是注册一个后置处理器组件，然后放到Spring容器和BeanFactory中。

Spring容器创建的时候，注册bean之前，得先注册好所有的后置处理器，注册完后置处理器后，还需要注册BeanFactory，把剩下的单实例的Bean都加入到BeanFactory中。

前面所做的一切都是为了初始化bean前后服务的（初始化bean之前得先把BeanFactory）。



#### 3.1.2、注册后置处理器



#### 3.1.2、注册BeanFactory

//初始化Bean工厂的同时，也会去创建剩下的单实例的Bean（因为创建完毕后，又要加入到Bean工厂里面了）

//而创建Bean不是说直接就指定创建了，而是先获取所有的Bean，获取不到的话就创建出来





## 4、Bean的生命周期

// Bean有这么一个生命周期，然后Spring针对这些生命周期的时机，都封装了一些接口，定义了一些方法，则这些时机的时候去执行这些方法呢（原理：到了这些时机的时候，会看看你这个Bean有没有实现这个接口啊，或者有没有加上什么注解，它就可以利用反射拿到对应的方法）

**大概生命周期如以下：**

1. 创建对象（调用的是构造器）

2. BeanPostProcessor（Before）

3. 初始化方法(时机：分配内存空间的时候，这个其实也是和第一步创建对象连在一起的，属于连贯动作)

4. BeanPostProcessor（After）
5. PreDestroy（销毁对象前执行）

6. 销毁对象（bean标签的destroy-method属性、@Bean注解的destroyMethod属性、或Bean直接实现DisposableBean接口并重写方法）



### 4.1、创建对象

调用的是构造方法，所以我们想在Bean的对象被创建的时候，执行一些业务代码逻辑的话，可以写在构造方法里边



### 4.2、初始化前(后置处理器)

**BeanPostProcessor（Before）**

概述：BeanPostProcessor是bean的后置处理器

作用：在bean初始化前后进行一些处理工作

Java代码定义：

```java
// BeanPostProcessor是一个接口来的，被管理的Bean实现接口，重写方法即可
public interface BeanPostProcessor {
    // Bean初始化之前会去执行的方法
    // arg1：当前bean的信息
    // arg2：当前bean在Spring容器中的id
    // return：返回给Spring容器bean值（我们可以直接返回bean，或对bean做一些包装）
    @Nullable
    default Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        return bean;
    }

    // Bean初始化之后会去执行的方法
    // arg1：当前bean的信息
    // arg2：当前bean在Spring容器中的id
    @Nullable
    default Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        return bean;
    }
}
```



### 4.3、初始化

以下几个场景提供了当Bean初始化时需要执行的方法

- bean标签的init-method属性
- @Bean注解的initMehtod属性

- 需要注册的Bean直接实现InitialzingBean接口，然后实现里面的（afterPropertiesSet）方法
- jdk提供的@PostContruct注解（加在某个方法上面）



### 4.4、初始化后(后置处理器)

**BeanPostProcessor（After）**

// 和3.2是一组的，分别在Bean初始化前后执行。



### 4.5、销毁对象前处理

作用：在销毁对象(关闭Spring容器时)前处理

应用：可以在方法上加@PreDestroy注解 （jdk提供的）



### 4.6、销毁对象

以下几个场景提供了当Bean被销毁时需要执行的方法

- bean标签里面的destroy-method
- @Bean注解里面的DestroyMethod方法
- Bean实现DisposableBean接口，并重写方法



## 5、Spring注解驱动开发

### 5.1、@Profile

作用：Spring为我们提供的可以根据当前环境，动态的激活和切换一系列组件的功能。



### 5.2、@PropertySource

想法：可以在代码里面使用${}引入外部配置文件，但我们的项目不是SpringBoot的

作用：想引入配置文件的某个key/value键值对

使用：@PropertySource("classpath:/dbconfig.properties")

前提：如果非SpringBoot项目的话，当前类一定得是配置类才行（也即是需要加@Configuration注解）



## 6、常用组件

### 6.1、BeanPostProcessor

概述：bean的后置处理器

作用：为某个bean量身定做了后置处理器的话，到时候某个bean加载的时候，如果有对应后置处理器的话，就会去执行相关操作。

这个接口特别重要，Spring底层在Bean初始化前后都用到了许多后置处理器

Spring底层对BeanPostProcessor的使用：

​	bean赋值、注入其他组件、@Autowired、生命周期注解功能、@Asunc， xxxBeanPostProcessor



### 6.2、BeanFactoryPostProcessor

概述：beanFactory的后置处理器

该组件被调用的时机：在BeanFactory标准初始化之后调用，所有的bean定义已经保存加载到BeanFactory，但Bean的实例还未创建

比这个组件更早执行的是BeanDefinitionRegistryPostProcessor这个组件的方法，Spring先加载这个组件，然后执行这个组件里面的方法。



### 6.3、ApplicationListener

概述：事件监听器

作用：相当于Spring完成了某种动作后，会传一个事件给我们的实现

方式一：实现接口

```java
/*
    使用一些Spring提供的一些接口功能的一些套路两步骤
    1、实现Spring规定的接口，并且重写方法
    2、然后你这个类也要交给Spring容器管理
    -> 到时候Spring的执行的某个时机，就会去扫描容器哪一些bean实现了这个规定的接口，到时候就会给这批bean调重写方法的呢。
 */

@Component
public class ApplicationListenerEvent implements ApplicationListener<ApplicationEvent> {
    @Override
    public void onApplicationEvent(ApplicationEvent event) {
        System.out.println("事件收到了" + event);
    }
}
```

方式二：在接收事件方法上使用注解

```java
@Service
public class ApplicationListenerServiceImpl {

    @EventListener
    public void getEvent(ApplicationEvent event){
        System.out.println("使用注解去接收实现，收到了" + event);
    }

}
```



### 6.4、SmartInitializingSingleton

概述：创建完定义的beans后，会容器中是否存在实现了这个接口，如果有实现这个接口的话，那么会调用里面的重写方法

作用：相当于某一个时机到达的时候，就会调用这个接口实现类的重写方法呢







## 7、Spring源码剖析

### 7.1、获取Spring上下文对象的常用方法

1. 如果是SpringBoot项目，那么执行run方法后，返回值就会返回Spring上下文（ApplicationContext）对象了（也即是整个IOC容器对象）
2. 如果不是SpringBoot项目，但是我们又想拿到的话，一方面可以直接去Spring容器中获取，也可以书写一个Bean，实现ApplicationContextAware接口，并实现方法，方法的形参就会给你带过来了，当给你当过来后，你只需要赋值给自己的成员变量即可了（内部实现原理是利用了Bean的Before(后置处理器)，判断你这个Bean如果实现了这些接口的话，那么就会调用你所实现的setApplicationContext这个方法，把Spring上下文对象直接传参塞给你）







### 7.2、跟踪如何创建IOC容器

```java
public ConfigurableApplicationContext run(String... args) {
		StopWatch stopWatch = new StopWatch();
		stopWatch.start();
		ConfigurableApplicationContext context = null;
		Collection<SpringBootExceptionReporter> exceptionReporters = new ArrayList<>();
		configureHeadlessProperty();
		SpringApplicationRunListeners listeners = getRunListeners(args);
		listeners.starting();
		try {
			ApplicationArguments applicationArguments = new DefaultApplicationArguments(args);
			ConfigurableEnvironment environment = prepareEnvironment(listeners, applicationArguments);
			configureIgnoreBeanInfo(environment);
			Banner printedBanner = printBanner(environment);
			context = createApplicationContext();
			exceptionReporters = getSpringFactoriesInstances(SpringBootExceptionReporter.class,
					new Class[] { ConfigurableApplicationContext.class }, context);
			prepareContext(context, environment, listeners, applicationArguments, printedBanner);
			refreshContext(context);
			afterRefresh(context, applicationArguments);
			stopWatch.stop();
			if (this.logStartupInfo) {
				new StartupInfoLogger(this.mainApplicationClass).logStarted(getApplicationLog(), stopWatch);
			}
			listeners.started(context);
			callRunners(context, applicationArguments);
		}
		catch (Throwable ex) {
			handleRunFailure(context, ex, exceptionReporters, listeners);
			throw new IllegalStateException(ex);
		}

		try {
			listeners.running(context);
		}
		catch (Throwable ex) {
			handleRunFailure(context, ex, exceptionReporters, null);
			throw new IllegalStateException(ex);
		}
		return context;
	}
```





#### 7.2.1、refresh()

作用：刷新IOC容器，（调用这个方法之前，Spring上下文对象刚刚利用反射实例化对象出来）



## 8、总结Spring的一些思想

### 8.1、Class.forName()

Spring底层很多处地方都利用到了Class类的这个方法，这个方法底层是去调用C++的方法，下面具两个例子

- @ConditionalOnClass(Advice.class)：自动配置类有许多类或方法都加了这个注解，根据你项目中是否引入该类，来判断你是否开启该组件的自动化配置
- spring.factories：SpringBoot默认写了127个自动配置组件，然后利用上面说的@ConditionalOnClass注解来判断你是否要开启该组件自动化配置。





