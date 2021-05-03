# 一、SpringMVC

## 1、框架底层思想

### 1.1、MVC在B/S系统下的应用

M：指处理的整个过程

V：获取到request域对象，展示页面

C：控制器 接收用户请求和响应用户的请求，请求Model进行处理，将Model返回的结果，渲染到视图上，将模型数据填充到request域



### 1.2、SpringMVC底层流程

```txt
白话理解一次请求的经过：前端向后台发起请求，首先通过前端控制器，然后再由前端控制器发送到Springmvc的Controller，又名叫Handler处理器（如果是Strust2，那么由前端控制器发送给Action 前端控制器在web.xml配置好Springmvc用Servlet的DispathcerServlet，而Strust2用filter）前端向后台发起一个URL请求，首先经过在web.xml配置的前端控制器（配置前端控制器时，需要自启动前端控制器，前端控制器本身是一个类DispatcherServlet，把这个类提前加载到JVM(JVM会加载到内存)即可，顺便修改一下springmvc.xml的路径在src下），然后前端控制器不懂这个URL请求，需要接主HandlerMapping处理器映射器帮我们解析这个URL请求，然后我们解析完毕后，就可以去准备执行请求路径对应的Controller（也即是Controller层中@RequestMapping对应的路径），想执行的话，还需要通过HandlerAdapter处理器适配器来执行。
```

![image-20210407132047713](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210407132048.png)

> 一次请求的具体全过程

第一步：用户发起请求到前端控制器（DispatcherServlet）

第二步：前端控制器请求HandlerMapping查找Handler

​		可以根据xml配置、注解进行查找（Controoler层会写@RequestMapping 到时候我们根据请求的url查找注解对应的路径）

第三步：处理器映射器HandlerMapping向前端控制器返回Handler

第四步：前端控制器调用处理器适配器去执行Handler

第五步：处理器适配器去执行Handler

第六步：Handler执行完成给适配器返回ModelAndView

第七步：处理器适配器向前端控制器返回ModelAndView

​		ModelAndView是Springmvc框架的一个底层对象，包括Model和View

第八步：前端控制器请求视图解析器去进行视图解析

​		根据逻辑视图名解析成真正的视图（JSP）

第九步：视图解析器向前端控制器返回View

第十步：前端控制器进行视图渲染

​		视图渲染将模型数据（在ModelAndView对象中）填充到request域

第十一步：前端控制器向用户响应结果



> 组件

1、前端控制器DispatcherServlet（不需要程序员开发）

作用：接收请求，响应结果，相当于转发器、中央处理器

有了DispatcherServlet减少了其它组件之间的耦合度



2、处理器映射器HandlerMapping（不需要程序员开发）

作用：根据请求的url查找Handler



3、处理器Handler（Controller层 需要程序员开发）



4、处理器适配器HandlerAdapter（不需要程序员开发）

作用：按照特定规则（HandlerAdater要求的规则）去执行Handler

注意：编写Handler时按照HandlerAdapter的要求去做，这样适配器才可以去正确执行Handler（相当于Controller里面使用的注解以及语法都是按照约定的）



5、视图解析器View resolver（不需要程序员开发）

作用：进行视图解析，根据逻辑视图名解析成真正的视图名(view)



6、视图View（需要程序员开发JSP  前端）

View是一个接口，实现类支持不同的View类型（JSP、Freematker、pdf、excel）



### 1.3、搭一个Springmvc项目

#### 1.3.1、在web.xml中配置前端控制器

```xml
<!-- springmvc前端控制器 -->
  <servlet>
      <servlet-name>springmvc</servlet-name>
      <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
<!-- 如果不配置contextConfigLocation，默认加载的是/WEB-INF/servlet名称-servlet.xml(springmvc-servlet.xml) -->
      <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc.xml</param-value>
      </init-param>
<!--  自启动 -->
      <load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
      <servlet-name>springmvc</servlet-name>
      <!--  
      第一种：*.action，访问以.action结尾由DispatcherServlet进行解析
      第二种：/  所有访问的地址都由DispatcherServlet进行解析，对于静态文件的解析需要配置不让DispatcherServlet进行解析
            使用此种方式可以实现RESTful风格的url
      第三种： /*  这样配置不对，使用这种配置，最终要转发到一个jsp页面时，
      仍然会由DispatcherServlet解析jsp地址，不能根据jsp页面找到handler，会报错。
       -->
      <url-pattern>.action</url-pattern>
  </servlet-mapping>
```



#### 1.3.2、配置处理器适配器

处理器适配器最终是去执行Handler，但是它规定Handler一定得实现Controller接口 Controller接口里面有一ModelAndView类型的方法，所以到时候Hanlder得去重写该方法

在classpath下的springmvc.xml中配置处理器适配器

```xml
<!-- 配置处理器适配器 Spring容器开启的时候会加载这个类 这个类里面书写了一些方法 规定Handler得实现Controller接口  -->
<bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"></bean>
```



#### 1.3.3、配置开发的Handler

```xml
<!--    配置Handler处理器 -->
<bean id="/queryItem.action" class="com.sikiedu.controller.DemoController"></bean>
```

```java
    //这个就是我们的Handler，但是我们的适配器规定我们Handler需要去实现Contrller接口，所以Handler也得去重写接口里面的方法呢
    //Controller接口里面有一个ModelAndView类型的方法，所以Handler需要去重写这个ModelAndView类型的方法
    //而我们返回的这个ModelAndView类型(把我们想传到前端的值添加到这个对象里面 addObject() )  该类型恰恰是request域
    public class ItemsController implements Controller {
        @Override
        public ModelAndView handleRequest(HttpServletRequest arg0, HttpServletResponse arg1) throws Exception {
            List<Items> itemsList = new ArrayList<Items>();
            Items items1 = new Items();
            items1.setName("联想电脑");
            items1.setPrice(3000f);
            items1.setDetail("联想电脑好用");

            Items items2 = new Items();
            items2.setName("苹果电脑");
            items2.setPrice(43000f);
            items2.setDetail("苹果电脑好用");

            itemsList.add(items1);
            itemsList.add(items2);
            ModelAndView modelAndView = new ModelAndView();
            //相当于request.setAttribute(key,value); 把值放到request域 到时候前端可以直接通过key取值
            //往域中存放值
            modelAndView.addObject("itemsList", itemsList);
            //往域中指定某个视图可以取值
            modelAndView.setViewName("/WEB-INF/jsp/items/itemsList.jsp");

            return modelAndView;
        }
    }
```



#### 1.3.4、配置处理器映射器

配法1

```xml
<!--     配置处理器映射器 -->
<bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"></bean>
```

配法2

```xml
<!--    配置映射器 -->
<bean class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
    <property name="urlMap">
        <map>
            表示希望想执行id：demo123
            <entry key="demo" value-ref="demo123"></entry>
        </map>
    </property>
</bean>        
```



#### 1.3.5、配置视图解析器

```xml
<!--    配置视图解析器 -->
<bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix"  value="/"></property>
    <property name="suffix"  value=".jsp"></property>
</bean>
```



总结以上：

```txt
	在XML配置的bean所对应的类，Tomcat启动的时候，都会加载到对应的容器当中，springmvc.xml到时候会加载到Spring容器当中
前端控制器是加载到Web容器，当客户发起请求的时候，通过Web容器中的前端控制器，然后前端控制器会到Spring容器
根据用户请求的URL寻找到映射器，映射器帮忙映射到查找的Handler处理器，然后再通过处理器适配器去执行这个Handler，
执行完毕后返回ModelAndView到了前端控制器，前端控制器再通过View resolver 去解析jsp 解析完毕才可以传到前端
```



## 2、实际开发应用

### 2.1、SpringMVC和Strust2的区别

接收前端参数的区别：

- Springmvc是基于方法开发的，Strust2是基于类开发的（SpringMVC将url和controller方法映射，映射成功后Springmvc生成一个Handler对象，对象中只包括了一个method，方法执行结束，形参数据销毁）

- SpringMVC可以进行单例开发，并且建议使用单例开发，Struts2通过类的成员变量接收参数，无法使用单例，只能使用多例



### 2.2、@Requestmapping的属性使用方式

- produces：指定响应体的返回类型（Content-Type）和编码

```java
// 例子
 @GetMapping(value = "createSignature",produces = "application/json; charset=UTF-8")
// 返回值的类型不需要利用response对象去指定，直接在注解上声明即可。
```



### 2.3、前后端传参类型

#### 2.3.1、包装类型pojo参数绑定

- 在形参中添加HttpServletRequest request参数，通过request接收查询条件参数（比较笨，不建议使用）

- 在形参中让包装类型的pojo接收查询条件参数

// 关于自动映射注意要点：前端name的命名 要和后端方法形参对象的属性名对得上

例：

**前端：**

```html
<input name ="itemsCustom.name" />
```

**后端：**

```java
// Controller形参 
public ModelAndView queryItems(ItemsQueryVo itemsQueryVo) throws Exception{
    
}
```

**VO类：**

```java
public class ItemQueryVo{
    private Items items;
    private ItemsCustom itemsCustom;
}
```

前后端利用POJO传值总结：

- 前端的name传值，若为基本类型的话，形参需要一一对应(名字一定要映射得上)

- 类引用类型的话，需要前端name的命名，要和后台方法形参**对象的属性名**对得上



#### 2.3.2、集合类型的参数绑定

可以用数组、list、Map等类型接收前端参数

**数组：**

```txt
需求：商品批量删除，用户在页面选择多个商品，批量删除 （传商品的ids的值）
前端：查询商品列表是使用foreach遍历后台返回的商品list，name=“ids” name值可以相等
controller：形参使用对应类型的数组接收
```



**list绑定：**

```txt
通常在需要批量提交数据时，将提交的数据绑定到list<pojo>中，比如录入（录入多门课成绩，批量提交）
本例子需要：批量商品修改，在页面输入多个商品信息，将多个商品信息提交到controller方法中
```

```java
后端：
//1.进入批量商品修改页面（页面样式参考商品列表实现）
//2.批量修改商品提交
//使用List接收页面提交的批量数据，通过包装pojo接收，在包装pojo中定义list<pojo>属性
public String editItemsAllSubmit(ItemsQueryVo itemsQueryVo)throws Exception{ return "" ;}  
//包装类里面有List<itemsCustom> 属性 到时候前端传list<itemsCustom>即可
```

```html
前端：
<!-- forEach循环遍历后台放在request域的list   items属性是取request域的list值  var指向list下标为0的对象  varStatus指向list下标为0的状态(status.index可以取索引) -->
<c:forEach items="${itemsList}" var="item" varStatus="status"  >  
    <tr>
        <!-- 1.如果是类引用类型的话，name指向形参的属性， 2.如果是基础类型的话，name直接指向形参 -->
        <td><input name="itemsList[${status.index}].name" value="${item.name}"></td> 
        <td><input name="itemsList[${status.index}].price" value="${item.price}"></td>
    </tr>
</c:forEach>
```



**map绑定：**

```html
前端：
<input type = "text" name="itemsMap['name']" value="">
```

```java
后端：
//包装类里面有Map<String,Object> itemsMap
public String editItemsAllSubmit(ItemsQueryVo itemsQueryVo)throws Exception{ return "" ;}  
```



### 2.4、Pojo数据回显

```java
@RequestMapping("editItemsSubmit")
public String editItemsSubmit(Model model, HttpServletRequest request, Integer id,
                              @ModelAttribute("items") @Validated(value = {ValidGroup1.class}) ItemsCustom itemsCustom) {

}
```

```txt
前端：value="${items.name}" //因为点了提交后，该方法没有返回数据放置一个名字叫items的对象在request域，所以没值传到前端，  	将@ModelAttribute("items")放到方法形参前面可以使用形参数据回显。。。
	将@ModelAttribute("items")放到方法形参前面：数据回显(该注解数据回显只支持pojo类型，不支持简单类型)。。。  //其实不使用注解的话，直接使用model.addAttribute() 把值放到request域即可
	将@ModelAttribute("items")方法上面(替代@RequestMapping)：可以将方法的返回值放在request域当中
```



### 2.5、全局异常处理

全局异常处理器处理思路：

解析出异常类型

如果该异常类型**是**系统自定义的异常，直接取出异常信息，在错误页面展示

如果该异常类型**不是**系统自定义的异常，构造一个自定义的异常类型(信息为"未知错误")



### 2.6、 SpringMVC相关注解

#### 2.6.1、@RequestBody

属性

- required：默认是true，声明请求是否需要携带body（Get请求肯定要设置成false了，不然就400了）

// 用swagger的时候，这个要改成false，因为虽然postman和页面都是可以调成功的，但是swagger默认的请求类型是"body"，假设改成"query"的话，我们入参又不是基本类型，所以这里直接改required=false，意思就是不需要携带请求体body了



## 3、深入源码分析

### 3.1、Servlet与SpringMVC的联系



### 3.2、DispatcherServlet

![image-20210317143233726](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210317143234.png)





