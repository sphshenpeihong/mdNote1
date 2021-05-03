# 一、SpringBoot2

## 1、基础概念篇

### 1.1、SpringBoot的优点

- 创建独立Spring应用
- 内嵌web服务器
- 自动starter依赖，简化构建配置
- 自动配置Spring以及第三方功能

- 提供生产级别的监控、健康检查及外部化配置

- 无代码生成、无需编写XML



### 1.2、依赖管理

```txt
pom.xml中，parent标签即为父pom。父pom里面的标签的作用如下：
1、<properties></properties>所定义的版本，子pom可以使用${mysql.version}直接使用。不满意的话可以直接在子pom也写<properties></properties>进行覆盖 （版本仲裁）
2、<dependencyManagerment><dependencyMnagerment/>里面所定义的jar包都会指定好版本的，到时候我们子pom里面也引入依赖的时候，不需要去写版本号，直接使用的就是父pom所定义的版本了。
```



### 1.3、自动配置

**也即是每个用到的key/value都有默认值，如果想要改变的话，可以直接在yml或properties等配置文件中修改即可（按优先级读取），因为本质上，配置文件中的属性也是对应绑定到某个类上，而且这个类也会加载到IOC容器当中管理。（也即是，即使你不修改值，那么这个类会有默认值，由于加了相关注解，所以会交给Spring容器进行管理，然后我们可以在相关配置文件当中对类属性的值进行修改）**



#### 1.3.1、Springmvc启动器（场景）

```xml
    <!-- springmvc等jar包支持 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
```



#### 1.3.2、tomcat启动器

springmvc启动器里面又引入了tomcat的启动器（需要tomcat场景直接引入，开箱即用）

```xml
	<dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-tomcat</artifactId>
      <version>2.3.3.RELEASE</version>
      <scope>compile</scope>
    </dependency>
```



#### 1.3.3、spring-boot-starter

一般启动器里面都会去引用spring-boot-starter这个依赖包，因为这个很重要（自动配置）

```xml
	<dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter</artifactId>
      <version>2.3.3.RELEASE</version>
      <scope>compile</scope>
    </dependency>
```



#### 1.3.4、SpringBoot自动配置依赖包

```xml
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-autoconfigure</artifactId>
      <version>2.3.3.RELEASE</version>
      <scope>compile</scope>
    </dependency>
```



#### 1.3.5、@SpringBootApplication

@SpringBootApplication相当于以下三个注解组成

- @SpringBootConfiguration
- @EnableAutoConfiguration
- @ComponentScan（包扫描，作用就是扫描该包下的注解，不配置的话，默认只扫描启动类所在目录下。）

```txt
自动配置里面写了会去扫描同样归属在启动类所在目录及其以下目录，都会去扫描到注解，然后放到IOC容器当中。当然如果不在此目录下，也想主动去扫描的话，启动类的@SpringBootApplication注解也提供了属性让我们主动去扫描某些目录。方法如下：
```

![image-20210222225021064](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210222225022.png)



##### 1.3.5.1、@SpringBootConfiguration

该注解里面也是引入了@Configuration注解，也即是主类其实也是一个配置类来的（核心配置类）



##### 1.3.5.2、@EnableAutoConfiguration





##### 1.3.5.3、@ComponentScan

包扫描，具体要扫描哪些目录，如果不写，默认就扫描启动类目录下了



### 1.4、@Configuration

概述：注解直接加在类上面，声明这个类就是一个配置类，里面可以配置多个Bean，到时候配置的Bean对象以及当前这个配置类都会交给Spring容器进行管理，

```java
package com.sph.practice.mybatisplus.config;

import com.baomidou.mybatisplus.core.injector.ISqlInjector;
import com.baomidou.mybatisplus.extension.injector.LogicSqlInjector;
import com.baomidou.mybatisplus.extension.plugins.OptimisticLockerInterceptor;
import com.baomidou.mybatisplus.extension.plugins.PaginationInterceptor;
import com.baomidou.mybatisplus.extension.plugins.PerformanceInterceptor;
import com.github.pagehelper.Page;
import lombok.extern.slf4j.Slf4j;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;

/**
 * Created by Shen Peihong on 2021/2/10
 * Description:
 *
 * @since 1.0.0
 */
/*
    1、使用@Configuration声明的类，到时候配置类本身也会加入到IOC容器当中
    2、@Bean也会交给Spring容器管理
    3、2.X在@Configuration注解中新加了一个属性，proxyBeanMethods，默认为true，如果声明为true，则配置类对象是一个代理对象来的。
    （从容器里面获取到配置类对象，然后可以直接用对象调用方法，如果上面的属性为true，那么会扫描IOC容器是否有此bean，有的话直接拿容器
    没有的话，才重新new一个。 效率比较低，关键看是否组件依赖时，所依赖的对象是否非要从Spring容器拿(比方DAO依赖DataSource，是否dataSource需要同一个对象)）

 */
@Configuration(proxyBeanMethods = false)
@Slf4j
public class MybatisPlusConfig {
        /**
     * 注册乐观锁拦截器的bean到Spring容器中，MP执行update的方法时，会识别到是哪个实体类，然后拿到反射，判断字段有version字段的话
     * 会向Spring容器拿我们规定好了的乐观锁拦截器先处理一波（可能是用到了该bean里面的方法）
     *
     * @return
     */
    @Bean("getOptimisticLockerInterceptor")
    public OptimisticLockerInterceptor getOptimisticLockerInterceptor() {
        return new OptimisticLockerInterceptor();
    }
}
```



### 1.5、@Import

```java
@Import({QyTestPO.class, QyUserPO.class})
```

概述：直接加在配置类的上方，可以将指定的类交给Spring容器管理（调用的是类的无参构造方法，一定要确保类有无参构造方法才行）



### 1.6、@ImportResource

概述：SpringBoot项目里面配置bean一般都用注解了，很少看到ApplicationContext.xml的配置文件了(Spring的配置文件)，但是如果我们非要使用配置文件的方式去配bean或AOP的话，也是可以的。



### 1.7、@Conditional

**条件装配**

概述：当符合一些条件的情况下才去装配Bean实例到Spring容器当中。

#### 1.7.1、@ConditionalOnBean

// 存在指定的bean时才加载组件

```java
@Configuration
@Slf4j
public class SpringBootConfig {

    @Bean("dateVO")
    public DateVO dateVO(){
        return new DateVO();
    }

    /*
        当存在dateVO这个bean的时候才会去装配fieldVO这个bean
        注：但是涉及到加载时的先后顺序，这里如果把下面filedVO的代码写在dateVO上面的话，就有问题了。
     */
    @ConditionalOnBean(name = "dateVO")
    @Bean
    public FieldVO fieldVO(){
        return new FieldVO();
    }
    
}
```



#### 1.7.2、@ConditionalOnMissingBean

// 不存在当前bean或指定的bean时才加载组件

```java
    // 若没有属性，则代表没有当前组件时，则注册组件
    // 当有指定name或value的话，则代表没有指定组件时，才去注册组件
    // 这里可能会存在bean注册的先后问题（可以采用设置bean加载的优先级或顺序啥的）
    @ConditionalOnMissingBean(name = "dateVO")
    @Bean
    public ConditionalOnMissingBeanVO conditionalOnMissingBeanVO(){
        return new ConditionalOnMissingBeanVO();
    }
```



#### 1.7.3、@ConditionalOnProperty

// 存在指定的配置文件的key/value才加载组件

```java
	// ConditionalProperty和其它注解一样，也是控制组件是否要加载的，下面介绍注解的具体属性
    // 1、prefix：配置文件的前缀
    // 2、name：配置文件的值（组合前缀） 切记不可和value共同使用
    // 3、value：配置文件的值（组合前缀） 切记不可和name共同使用
    // 4、matchIfMissing：当为true时，如果前面声明的配置文件的值没有匹配上的话，也会去注册组件
    // 5、havingValue：假设key确实存在，但是这里还需要再匹配value值是否匹配上（双重校验key/value），匹配上才会去注册组件
    @Bean("dateVO")
    @ConditionalOnProperty(prefix = "sha1.sha2", value = "sha3", havingValue = "1", matchIfMissing = false)
    public DateVO dateVO(){
        return new DateVO();
    }
```



#### 1.7.4、@ConditionalOnClass

```java
    // 当我们项目有此类时，才会去注册组件
    @ConditionalOnClass(DispatcherServlet.class)
    @Bean
    @SuppressWarnings(value = "")
    public ConditionalOnClassVO conditionalOnClassVO(){
        return new ConditionalOnClassVO();
    }
```



#### 1.7.5、@ConditionalOnWebApplication

```java
    // 当我们项目是Servlet编程(非响应式编程)，才注册组件
    @ConditionalOnWebApplication(type = ConditionalOnWebApplication.Type.SERVLET)
    @Bean
    public ConditionalOnWebApplicationVO conditionalOnWebApplicationVO(){
        return new ConditionalOnWebApplicationVO();
    }
```



#### 1.7.6、@ConditionalOnResource

```java
    // 当classpath下有此资源文件时，，才会去注册组件
    @ConditionalOnResource(resources = "/local/local.properties")
    @Bean
    public ConditionalOnResourceVO conditionalOnResourceVO(){
        return new ConditionalOnResourceVO();
    }
```



### 1.8、@ConfigurationProperties

**作用：类的属性映射去读取配置文件/整个类属性进行DI注入**

配置文件

```yml
# 自定义配置项1  对象数据
my-car:
  carName: BYD
  carPrice: 10000
```



**方法一：**

> @ConfigurationProperties(prefix = "配置文件前缀") + @Component

读取配置文件类

```java
/**
 * Created by Shen Peihong on 2021/2/23
 * Description: Car类的值是直接读取配置文件的值(也即是属性进行DI注入)。然后这个类也是交给Spring容器管理
 * 也就是说，类里面的属性想注入值的话，并不一定要一个一个属性使用@Value进行DI注入
 * 也可以整个类去映射配置文件进行映射读取。
 *
 * @since 1.0.0
 */
// 读取配置文件类去读取配置文件的话，必须要保证这个类同时也是交给Spring容器管理的，这样才能拿到配置文件的值
@Component
@ConfigurationProperties(prefix = "my-car") //前缀必须和yml文件的前缀一直才能映射成功
@Data // 需要有get、set方法
public class Car {
    private String carName;
    private Integer carPrice;
}
```



**方法二：** （使用这种方法的话，可以按需装备读取配置文件的Bean）

> @ConfigurationPorperties(prefix = "配置文件前缀") + @EnableConfigurationPorperties(指向加了前面注解的类.class)

配置类

```java
// 配置类 只有有@Configuration的类都称为配置类
@Configuration
@Slf4j
@EnableConfigurationProperties(Car.class) //如果@ConfigurationProperties所在类没有注解@Component的话，那我们可以按需装配（指向的配置文件类需要的时候再开启允许装配）
public class SpringBootConfig {
    
}
```

读取配置文件类

```java
// 这里直接把@Component注解给注释掉了
//@Component
// 读取配置文件类去读取配置文件的话，必须要保证这个类同时也是交给Spring容器管理的，这样才能拿到配置文件的值
@ConfigurationProperties(prefix = "my-car")
@Data
public class Car {
    private String carName;
    private Integer carPrice;
}
```



### 1.9、yml配置文件各种格式详解

特别注意：

- 无论以什么格式，反正获取配置文件值的类必须是交给Spring容器管理的，这个毋庸置疑
- 属性必须有get、set方法

#### 1.9.1、简单key/value

Java

```java
@Value("${is_open}")
public boolean isOpen;
```

yml

```yml
# 自定义配置项0  简单key/value
is_open: true
```



#### 1.9.2、对象数据

Java

```java
@Component
// 读取配置文件类去读取配置文件的话，必须要保证这个类同时也是交给Spring容器管理的，这样才能拿到配置文件的值
@ConfigurationProperties(prefix = "my-car")
@Data
public class Car {
    private String carName;
    private Integer carPrice;
    private String carDesc;
}
```

yml

```yml
# 自定义配置项1  对象数据
my-car:
  carName: BYD
  carPrice: 10000
  carDesc: "好看哦"
```



#### 1.9.3、数组

Java

```java
@Component
@ConfigurationProperties(prefix = "car-name-array")
@Data
public class CarNameArray {

    String[] carNames;

}
```

yml

```yml
# 自定义配置项2  数组数据
car-name-array:
  carNames:
    - byd
    - yd
    - fengtian
    - bentian
```



#### 1.9.4、List（String类型）

Java

```java
@Component
@ConfigurationProperties(prefix = "car-name-list")
@Data
public class CarNameList {

    List<String> carNameList;

}
```

yml

```yml
# 自定义配置项3  List<String>
car-name-list:
  carNameList:
    - bydList
    - yd
    - fengtian
    - bentian
```



#### 1.9.5、List（类类型）

Java

```java
@Component
@ConfigurationProperties(prefix = "car-list")
@Data
public class CarList {

    private List<Car> carList;

}
```



yml

```yml
# 自定义配置项4  List<类类型>
car-list:
  carList:
    - carName: benchi
      carPrice: 100
      carDesc: "贵"
    - carName: wuling
      carPrice: 20
      carDesc:  "便宜"
```



#### 1.9.6、Map

Java

```java
@Component
@ConfigurationProperties(prefix = "car-map")
@Data
public class CarMap {

    private Map<String, Car> carMap;

}
```



yml

```yml
# 自定义配置项5 Map<String, 类类型>
car-map:
  carMap:
    car1:
      carName: benchi
      carPrice: 100
      carDesc: "贵"
    car2:
      carName: wuling
      carPrice: 20
      carDesc:  "便宜"
```



#### 1.9.7、复杂类型(类中类)

Java

```java
@Component
@ConfigurationProperties(prefix = "class-nest-class")
@Data
public class ClassNestClass {

    private Car car;

}
```



yml

```yml
# 自定义配置项6 类中类
class-nest-class:
  car:
    carName: benchi
    carPrice: 100
    carDesc: "贵"
```



#### 1.9.8、总结

//数组、List等类型的值一般都会配一条横杠“ - ”

// 许多Date、String等类型，yml文件中的值都不用使用单引号或双引号，默认会给你加上单引号的。那么什么场景下我们需要手动去加双引号呢？

答案是：我们需要使用到转义字符的时候，双引号就会给你换行（不会转义，理解是本身\n就是换行的转义的意思，不会转义也即是不会改变你本身的意义）



### 1.10、ResponseEntity

概述：Spring提供的Http请求传输通用类型



- 方式一：返回指定的code、desc

```java
    /**
     * 构造方法1  直接返回code和desc
     * 
     * @param body 数据值 例："Hello world"
     * @param status 状态码  例：HttpStatus.OK;
     */
    public ResponseEntity(@Nullable T body, HttpStatus status) {
        this(body, (MultiValueMap)null, (HttpStatus)status);
    }


```



- 方式二：返回指定的code、desc、响应头部headers

```java

```







### 1.11、@Resource、@Autowired、@Qualifier

**@Resource**：先通过定义的name，再通过类型  （也可以自己使用name和type属性）

**@Autowired**：直接根据类型查找bean

**@Qualifier**：结合@Autowired一起使用，就可以通过byName查找bean了



### 1.12、多module项目避坑

现在都是微服务的项目，那新建包的时候，可能前缀都一样，如果包的前缀都一











## 2、进阶篇

### 2.1、了解自动配置原理

底层会去加载jar包的META-INF/spring.factories配置文件，加载出需要待注册的组件

![image-20210226215154546](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210226215157.png)

里面都是写死的，写死了许多个需要去自动配置的组件（利用@Conditional(条件装配)注解去按需开启自动配置项）

![image-20210226215347349](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210226215348.png)

每个组件里面的代码的相关配置项也都是去读配置文件的值的。（利用@ConfigurationProperties）

![image-20210227005356975](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210227005358.png)

**总结：**

- SpringBoot先加载所有的自动配置类  xxxxAutoConfiguration
- 每个自动；配置类按照条件进行生效，默认都会绑定配置文件指定的值。xxxxProperties里面拿。xxxProperties和配置文件进行绑定
- 生效的xxxAutoConfiguration配置类就会给容器中装配很多组件
- 只要容器中有这些组件，相当于这些功能就有了
- **定制化配置：**
  - 用户直接自己@Bean替换底层的组件
  - **用户去看这个组件时获取的配置文件什么值就去修改即可（比方在properties、yml、yaml文件中修改）**



### 2.2、Web应用开发

#### 2.2.1、静态资源访问

##### 2.2.1.1、tomcat、war、jar包目录

> tomcat目录

![image-20210227172914575](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210227172915.png)



> war包目录

![image-20210227171134996](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210227171136.png)

![image-20210227171337954](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210227171338.png)

> jar包目录

![image-20210227173956194](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210227173957.png)

![image-20210227174057133](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210227174058.png)

![image-20210227174748129](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210227174749.png)

![image-20210227174812108](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210227174813.png)



##### 2.2.1.2、静态资源目录

只要把静态资源放在类路径下：/static、/public、/resource、**/META-INF/resources（很多jar包里面的页面都是放这个目录，比如swagger-ui.html）**

然后访问的时候：当前项目根路径/ + 静态资源名



##### 2.2.1.3、欢迎页支持（深入理解）

作用：当我们直接访问项目根路径时，直接弹出默认index.html页面

实施：直接丢index.html在默认的静态资源路径下即可

![image-20210227214321762](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210227214323.png)

**底层实现**：

1、可以去SpringBootAutoConfigure依赖包下找到SpringMvc对应的自动配置类，配置类里面有按需装载了许多类，而类里面又定义了很多的成员变量，基本上都是映射了配置文件，我们可以按需更改配置文件，但是更改了配置文件的话，必须重启才能生效，因为映射的类的对象底层是放在JVM里面的，除非你有办法更改JVM里面的存储区域那么也可以不用重启项目。 

2、我们知道Springmvc会在启动的时候，将我们的Controller接口(Handler)放在



##### 2.2.1.4、静态资源访问前缀

想法：目前访问静态资源没有前缀，想法是可以带一个前缀，后面后端做拦截器也好区分。（访问静态资源需要带一个前缀才能访问得到）

实施：直接在application.yml文件中修改该key即可

```properties
# 默认值是：/**
spring.mvn.static-path-pattern: /res/**
```

现象：发现配了上面静态资源前缀后，我们直接访问项目根路径：http://localhost:8085/shop/ 没有跳到index.html了，是因为SpringBoot这边有bug了。所以我们只能靠我们后端代码去进行请求转发了。

后端代码处理：

```java
package com.sph.practice.component.boot.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

/**
 * Created by Shen Peihong on 2021/2/27
 * Description:
 *
 * @since 1.0.0
 */
// 不能使用@RestController，因为里面有@ResponseBody(会将返回值转json对象)
@Controller
public class WelComePageCtl {
    /**
     * SpringBoot有bug，配了静态资源访问前缀的话，访问项目根目录无法默认跳到index.html
     * 所以需要对访问根路径的请求由接口来进行请求重定向
     *
     * @return
     */
    @RequestMapping("/")
    public String welComePage() {
        //不用带.html后缀，因为我们在application.yml中配置前后缀了。
        return "/res/index";
    }
}
```

愉快的结果：

![image-20210227220809194](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210227220810.png)



####  2.2.2、Rest原理探究

##### 2.2.2.1、DispatcherServlet流程

由于DispatcherServlet里面重写了父类定义的抽象方法：doService(req, resp)，故父类在执行的时候会调用到该方法，形成整条链路的连接，从而HttpServlet里面的doGet(req，resp)和doPost(req, resp)会一直调用到DispatcherServlet里面的doDispatch(req, resp)方法。



### 2.3、SpringMVC相关

#### 2.3.1、Controller方法形参支持

##### 2.3.1.1、注解

- @PathVariable("id")：Rest风格取值

- @RequestHeader("User-Agent")：获取请求头部信息

- @ModelAttribute：

- @RequestParam：绑定入参

- @MatrixVariable：矩阵变量传值，/user/1;jsessionId=12983   ，后端使用注解接收矩阵变量里面的值（可解决cookie被禁用）

- @CookieValue("token")：获取请求头Cookie的值

- @RequestBody：获取请求体的值，Post方式才有请求体，可以将json转java对象

- @RequestAttibute("username")：获取request域中，有通过setAttibute放值的值。 （类似调getAttribute方法获取值）

  

##### 2.3.1.2、注解Servlet API

- WebRequest：

- ServletRequest：

- MultipartRequest

- HttpSession：

- java.servlet.http.pushBuilder：

- principal：

- InputStream：

- Reader：

- HttpMethod：

- Locale：

- TimeZone：

- ZoneId：

  

##### 2.3.1.3、注解复杂函数

- Map：

- Errors/BildingResult：

- Model：

- RedirectAttibutes：

- ServletResponse：

- SessionStatus：

- UriComponentsBuilder：

- ServletUriComponentsBuilder：

  

##### 2.3.1.4、注解自定义对象参数

- 可以自动类型转换与格式化，可以级联封装



#### 2.3.2、SpringMVC相关组件

##### 2.3.2.1、HandlerMapping

概述：处理器映射器，Spring容器启动的时候，执行onRefresh()方法的时候，会将{ Controller接口的访问地址 ：Controller接口 }的映射给存到特定的handlerMapping组件中。需要的时候，直接用即可

> 例：DispatcherServlet类里面定义了

```java
    /** List of HandlerMappings used by this servlet. */
    @Nullable
    private List<HandlerMapping> handlerMappings;
```

> 会在某个时机，将Spring组件中实现该接口的组件，全部给add进这个List当中，然后DispatcherServlet处理业务逻辑的时候，可能会根据业务循环遍历去判断，例如以下

```java
    // 入参：req，返参：对应的handler
    mappedHandler = getHandler(processedRequest);

	/**
	 * 目的：入参req请求，出参具体映射的handler方法
	 * 方式：调用getHandler方法，然后遍历所有HandlerMapping组件，因为容器启动的时候，已经把所有请求与handler映射的键值对已经放在各种类型的HandlerMapping组件中了，最终返回对应的handler(此时handler的入参值，返参值还没确定)
	 */
	@Nullable
	protected HandlerExecutionChain getHandler(HttpServletRequest request) throws Exception {
		if (this.handlerMappings != null) {
			for (HandlerMapping mapping : this.handlerMappings) {
				HandlerExecutionChain handler = mapping.getHandler(request);
				if (handler != null) {
					return handler;
				}
			}
		}
		return null;
	}
```



##### 2.3.2.2、HandlerAdapter

概述：处理器适配器，根据HandlerMapping找到了对应的handler后，找到对应的handlerAdapter去执行该handler

> 获取handler对应的handlerAdapter

```java
// DispatcherServlet --> getHandlerAdapter    
	// 入参：handler，返参：可执行该handler的适配器HandlerAdapter
    HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());

	/**
	 * 思想：同样遍历遍历容器中所有的HandlerAdapter组件，调用接口定义的supports方法，具体逻辑由接口的实现类去定义即可
	 */
	protected HandlerAdapter getHandlerAdapter(Object handler) throws ServletException {
		if (this.handlerAdapters != null) {
			for (HandlerAdapter adapter : this.handlerAdapters) {
				if (adapter.supports(handler)) {
					return adapter;
				}
			}
		}
		throw new ServletException("No adapter for handler [" + handler +
				"]: The DispatcherServlet configuration needs to include a HandlerAdapter that supports this handler");
	}
```

> 找到了对应的handlerAdapter，然后去执行该handler

```java
// DispatcherServlet --> doDispatch
	// 实际执行handler的地方
    mv = ha.handle(processedRequest, response, mappedHandler.getHandler());
```

> 具体执行handler的整个流程

```java
// RequestMappingHandlerAdapter --> invokeHandlerMethod
	/**
	 * Invoke the {@link RequestMapping} handler method preparing a {@link ModelAndView}
	 * if view resolution is required.
	 * @since 4.2
	 * @see #createInvocableHandlerMethod(HandlerMethod)
	 */
	@Nullable
	protected ModelAndView invokeHandlerMethod(HttpServletRequest request,
			HttpServletResponse response, HandlerMethod handlerMethod) throws Exception {

		ServletWebRequest webRequest = new ServletWebRequest(request, response);
		try {
			WebDataBinderFactory binderFactory = getDataBinderFactory(handlerMethod);
			ModelFactory modelFactory = getModelFactory(handlerMethod, binderFactory);

            // 这里的定义了这么一个包装对象，把handler包装在里面
			ServletInvocableHandlerMethod invocableMethod = createInvocableHandlerMethod(handlerMethod);
			// 将所有参数解析器，全丢到invocableMethod对象中
            if (this.argumentResolvers != null) {
				invocableMethod.setHandlerMethodArgumentResolvers(this.argumentResolvers);
			}
            // 将所有返回值处理器，全丢到invocableMethod对象中
			if (this.returnValueHandlers != null) {
				invocableMethod.setHandlerMethodReturnValueHandlers(this.returnValueHandlers);
			}
			invocableMethod.setDataBinderFactory(binderFactory);
			invocableMethod.setParameterNameDiscoverer(this.parameterNameDiscoverer);

			ModelAndViewContainer mavContainer = new ModelAndViewContainer();
			mavContainer.addAllAttributes(RequestContextUtils.getInputFlashMap(request));
			modelFactory.initModel(webRequest, mavContainer, invocableMethod);
			mavContainer.setIgnoreDefaultModelOnRedirect(this.ignoreDefaultModelOnRedirect);

			AsyncWebRequest asyncWebRequest = WebAsyncUtils.createAsyncWebRequest(request, response);
			asyncWebRequest.setTimeout(this.asyncRequestTimeout);

			WebAsyncManager asyncManager = WebAsyncUtils.getAsyncManager(request);
			asyncManager.setTaskExecutor(this.taskExecutor);
			asyncManager.setAsyncWebRequest(asyncWebRequest);
			asyncManager.registerCallableInterceptors(this.callableInterceptors);
			asyncManager.registerDeferredResultInterceptors(this.deferredResultInterceptors);

			if (asyncManager.hasConcurrentResult()) {
				Object result = asyncManager.getConcurrentResult();
				mavContainer = (ModelAndViewContainer) asyncManager.getConcurrentResultContext()[0];
				asyncManager.clearConcurrentResult();
				LogFormatUtils.traceDebug(logger, traceOn -> {
					String formatted = LogFormatUtils.formatValue(result, !traceOn);
					return "Resume with async result [" + formatted + "]";
				});
				invocableMethod = invocableMethod.wrapConcurrentResult(result);
			}

			invocableMethod.invokeAndHandle(webRequest, mavContainer);
			if (asyncManager.isConcurrentHandlingStarted()) {
				return null;
			}

			return getModelAndView(mavContainer, modelFactory, webRequest);
		}
		finally {
			webRequest.requestCompleted();
		}
	}
```

>  上面专门定义了一个包装子类，去封装相关信息

```java
public class InvocableHandlerMethod extends HandlerMethod {
    
}
```

> 真正去执行handler的地方

```java
// RequestMappingHandlerAdapter --> invokeHandlerMethod	
	invocableMethod.invokeAndHandle(webRequest, mavContainer);
		--> Object returnValue = invokeForRequest(webRequest, mavContainer, providedArgs);//具体执行handler
```

> 执行handler之前，需要先获取到req的传参，然后获得到handler方法的形参上的值，这才能执行方法呢

```java
	/**
	 * 执行请求
	 */
	@Nullable
	public Object invokeForRequest(NativeWebRequest request, @Nullable ModelAndViewContainer mavContainer,
			Object... providedArgs) throws Exception {
		// 先获取到handler方法的参数的值
		Object[] args = getMethodArgumentValues(request, mavContainer, providedArgs);
		if (logger.isTraceEnabled()) {
			logger.trace("Arguments: " + Arrays.toString(args));
		}
		return doInvoke(args);
	}
```

> 获取handler方法参数的值

```java
// InvocableHandlerMethod --> getMethodArgumentValues
	/**
	 * 获取到handler方法形参的所有对象值
	 */
	protected Object[] getMethodArgumentValues(NativeWebRequest request, @Nullable ModelAndViewContainer mavContainer,
			Object... providedArgs) throws Exception {
		// 先获取到该handler方法的所有形参对象，此时还没赋好值
		MethodParameter[] parameters = getMethodParameters();
		if (ObjectUtils.isEmpty(parameters)) {
			return EMPTY_ARGS;
		}

        // 形参对象的值（发请求时传值）
		Object[] args = new Object[parameters.length];
		for (int i = 0; i < parameters.length; i++) {
			MethodParameter parameter = parameters[i];
			parameter.initParameterNameDiscovery(this.parameterNameDiscoverer);
			args[i] = findProvidedArgument(parameter, providedArgs);
			if (args[i] != null) {
				continue;
			}
			if (!this.resolvers.supportsParameter(parameter)) {
				throw new IllegalStateException(formatArgumentError(parameter, "No suitable resolver"));
			}
			try {
                // 利用参数解析器去解析形参参数，进而去得到对应的值
				args[i] = this.resolvers.resolveArgument(parameter, mavContainer, request, this.dataBinderFactory);
			}
			catch (Exception ex) {
				// Leave stack trace for later, exception may actually be resolved and handled...
				if (logger.isDebugEnabled()) {
					String exMsg = ex.getMessage();
					if (exMsg != null && !exMsg.contains(parameter.getExecutable().toGenericString())) {
						logger.debug(formatArgumentError(parameter, exMsg));
					}
				}
				throw ex;
			}
		}
		return args;
	}
```

> 利用参数解析器去解析参数

```java
// HandlerMethodArgumentResolverComposite --> resolveArgument
	/**
	 * 遍历每个形参，然后里面再去遍历参数解析器，看看哪个可以进行解析该形参
	 */
	@Override
	@Nullable
	public Object resolveArgument(MethodParameter parameter, @Nullable ModelAndViewContainer mavContainer,
			NativeWebRequest webRequest, @Nullable WebDataBinderFactory binderFactory) throws Exception {
		// 获取该形参对应的参数解析器
		HandlerMethodArgumentResolver resolver = getArgumentResolver(parameter);
        // 若找不到参数解析器，则抛异常给前端
		if (resolver == null) {
			throw new IllegalArgumentException("Unsupported parameter type [" +
					parameter.getParameterType().getName() + "]. supportsParameter should be called first.");
		}
        // 找到参数对应的解析器后，进行参数解析，返回得到该参数解析后真正的值
		return resolver.resolveArgument(parameter, mavContainer, webRequest, binderFactory);
	}
```

> 获取某个参数对应的参数解析器的遍历过程。

```java
// HandlerMethodArgumentResolverComposite --> getArgumentResolver
	/**
	 * Find a registered {@link HandlerMethodArgumentResolver} that supports
	 * the given method parameter.
	 */
	@Nullable
	private HandlerMethodArgumentResolver getArgumentResolver(MethodParameter parameter) {
		HandlerMethodArgumentResolver result = this.argumentResolverCache.get(parameter);
		if (result == null) {
			for (HandlerMethodArgumentResolver resolver : this.argumentResolvers) {
                // 判断当前参数解析器是否支持
				if (resolver.supportsParameter(parameter)) {
					result = resolver;
                    // 放入缓存，key:形参对象 value：解析该形参的参数解析器 
					this.argumentResolverCache.put(parameter, result);
					break;
				}
			}
		}
        // 返回参数解析器
		return result;
	}
```



##### 2.3.2.3、HanlderMehtodArgumentResolver

概述：**参数解析器**父接口，SpringMVC内置了20多个参数解析器，并且交给Spring容器管理，我们如果有自定义的参数解析器的话，也可以看看Springmvc如何提供接入，这样的话，我们就可以自定义参数类型或注解了（比如dqdp的@InterfaceParam）



##### 2.3.2.4、自定义类型参数绑定

参数解析器：ModelAttributeMethodProcessor来对页面传输的数据进行解析 （底层是判断是否是基本类型，最后！，非false就是true）

利用WebDataBinder web数据绑定器来对Http请求的数据绑定到我们的自定义类型对象中(会利用已有的Convert进行类型是否支持判断)

















