# 一、Springboot

## 1、Springboot基础

### 1.1、相关注解

- @Component：配置在类上边，声明该类交给Spring管理，这样的话，其它地方才能从Spring容器中拿到该bean

- @Configuration：这个一般用于要配置一个bean或要修改Spring容器里面已有的bean的时候，就加在某个配置类上，然后类里面提供方法去增加bean或修改已有bean，方法上面要记得加上@Bean，这样的话，到时候Springboo启动的时候，才会去扫描到，一个方法对应一个bean，方法的返回值一定是bean的class路径类型。

- @Controller：这个一般用于表现层
- @RestController：这个一般用于表现层
- @Service：这个一般用于业务逻辑层
- @Mapper：这个一般用于DAO
- @Repository：这个一般用于DAO
- @MapperScan：这个一般用于启动类或配置类主动去扫描DAO或扫描其它注解

- @Bean：这里用到了@Bean的话，那么这个类得使用@Configuration声明成配置类



@SpringBootApplication注解等价以下三个注解：

- @Configuration
- @EnableAutoConfiguration





### 1.2、默认静态资源目录

- classpath：/META-INF/resource/
- classpath：/resource/
- **classpath：/static/**
- classpath：/public/



### 1.3、application.properties

```properties
# 配置部署端口号
server.port: 8081  
# 配置URL前缀
server.servlet.context-path: /shop  

# 注册mysql驱动
spring.datasource.driver-class-name: com.mysql.cj.jdbc.Driver  
# 配置访问数据库的地址 并配置时差
spring.datasource.url: jdbc:mysql://127.0.0.1:3306/db_shopmaster?serverTimezone=GMT%2B8&useUnicode=true&characterEncoding=utf8  
# 数据库账号
spring.datasource.username: root  
# 数据库密码
spring.datasource.password: root  

# 使用springboot+jpa时，显示sql语句在控制台
spring.jpa.show-sql: true  

# 配置mvc视图前缀 访问某个地址时，就不用加前后缀了
spring.mvc.view.prefix: /WEB-INF/views/    
# 配置mvc视图后缀
spring,mvc.view.suffix: .jsp    
# 以毫秒为单位，不配置此项，默认是10s
spring.mvc.async.request-timeout: 10000    
# 配置日期格式
spring.mvc.data-format: 'dd/MM/yyyy'    
# 启动favicon.ico的解析
spring.mvc.favicon.enabled: true    

# 使用xml开发的话，需要配置此项
mybatis.mapper-locations: classpath:mapper/*.xml   
# 给实体类取别名
mybatis.type-aliases-package: com.zhang.ssmschoolshop.entity    

# 需要先在pom.xml引入对应的依赖 支持热部署(类文件发生改变时，不需要手动去重启应用了) 需要配合自动编译(静态和动态) 不然就需要手动Build 
devtoos.restart.enabled: true    

# logging.level设置日志级别，后面跟生效的区域，比如root表示整个项目，也可以设置为某个包下，也可以具体到某个类名(日志级别的值不区分大小写)
logging.level.com.zhang.shop.dao: debug    
```



### 1.4、多套环境配置文件

properties配置格式：

在Springboot中多环境配置文件名需要满足application-{profile}.properties的格式，其中{profile}对应你的环境标识

比如：

- application-dev.properties：开发环境
- application-test.properties：测试环境
- application-pro.properties：生产环境

// 至于哪个具体的配置文件会被加载，需要在application.properties文件中通过spring.profiles.active属性来设置，其值对应{profile}值







