# 一、Swagger

**Swagger的作用：生成相关后端接口和实体类文档 + 测试接口**

**使用Springboot集成Swagger2 （Springboot内部没集成Swagger2）**

## 1、引入使用步骤

1、引入jar包：swagger2和ui（提供图形化界面）

![image-20201222182754477](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222182755.png)

2、书写配置类

![image-20201222182823024](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222182824.png)



## 2、介绍Swagger2-ui的图形化界面(4部分)

![image-20201222182846567](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222182847.png)

1、实体类信息：Swagger扫描到SpringMVC的相关@xxxxMapping注解时，如果返回实体类，那么就会记在Models这里 （只要我们的接口中，返回值中存在实体类，它就会被扫描到Swagger中）

2、接口信息：扫描到相关类的(@xxxxMapping)接口都会记得在Swagger

3、Swagger信息：一些作者等相关信息

4、分组：可以进行分组展示



## 3、介绍常用注解

**1、@ApiModel("用户实体类")：给类重命名展示的，展示在Model就不会显示类名User，而是展示用户实体类这个文本  ，描述实体类**

**2、@ApiModelProperty("用户名")：给实体类的成员变量加Swagger注释的  ，描述成员变量的作用**



**3、@Api  （作用在Controller类上边）**

**4、@ApiOperation("获取银行信息VO")：给某个Controller接口加Swagger注释的  ，描述接口的作用**

5、@ApiParam("用户名")：给Conteoller接口的方法形参加注释，描述形参的作用  //若使用的是@GetMapping的话，那么不可以和@ApiParam结合使用，因为使用了@ApiParam注解，那么即声明该参数需要传递Body请求体，但是Get请求只能Url拼接。

**6、@ApiImplicitParams()：用于方法，包含多个@ApiImplicitParam**

**7、@ApiImplicitParam()：用于方法，表示单独的请求参数**

![image-20201222182906806](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222182907.png)

![image-20201222182940578](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222182941.png)





## 4、注解举例说明

**注解在Model实体类上：**

1、**@ApiModel**：用于实体类

- value：表示对象名

- description：描述

2、**@ApiModelProperty**：用于实体类变量

- value：字段说明

- name：重写属性名字

- dataType：重写属性类型
- required：是否必填
- example：举例说明
- hidden：隐藏

![image-20201222183003935](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222183004.png)



**注解在Controller方法上：**

**1、@Api**：用于Controller类，表示标识这个类是swagger的资源

- tags：表示说明

2、**@ApiImplicitParams**：用于Controller接口上，包含多个@ApiImplicitParam



3、**@ApiImplicitParam**：表示单独的请求参数

- name：参数名
- value：参数说明
- required：是否必须
- dataType：数据类型
- example：举例说明
- paramType：参数类型



4、**@ApiOperation**：用于Conteoller接口上方

- value：用于方法描述
- notes：用于提示内容
- tags：可以重新分组(视情况而用)



4、@ApiParam：用于接口的方法形参

- name：参数名
- value：参数说明
- required：是否必填

![image-20201222183024786](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222183026.png)







