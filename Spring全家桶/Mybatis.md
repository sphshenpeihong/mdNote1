# 一、Myabtis

## 1、Mybatis基础

### 1.1、什么是Mybatis？

Mybatis是一款优秀的持久层框架，它支持自定义SQL、存储过程以及高级映射。Mybatis免除了几乎所有的JDBC代码以及设置参数和获取结果集的工作。Myabtis可以通过简单的XML或注解来配置和映射原始类型、接口和Java POJO为数据库中的记录



### 1.2、从 XML 中构建 SqlSessionFactory

**一般命名**：sqlMapConfig.xml

```java
//mybatis的配置文件
String resource = "org/mybatis/example/mybatis-config.xml";
//获取配置文件的输入流
InputStream inputStream = Resources.getResourceAsStream(resource);
//利用建造者模式建造出sqlSessionFactory工厂出来
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
//使用sqlSessionFactory工厂创建出SqlSession对象 （每个线程都需要拥有一个SqlSession，不准共用，否则有线程安全问题）
SqlSession session = sqlSessionFactory.openSession();
//使用SqlSession对象去获取mapper对象
BlogMapper mapper = session.getMapper(BlogMapper.class);
//mapper对象调用方法
Blog blog = mapper.selectBlog(101);
```



### 1.3、从SqlSessionFactory中获取SqlSession

```java
try (SqlSession session = sqlSessionFactory.openSession()) {
  BlogMapper mapper = session.getMapper(BlogMapper.class);
  Blog blog = mapper.selectBlog(101);
}
```



### 1.4、mybatis配置文件

1.properties(读取配置文件) //一般是需要读取连接数据库的相关信息

**2.settings(全局配置参数)**

//可以配置日志工厂等，如果配置slf4j的话，需要自己maven导包，并且书写log4j.properties文件(规定好日志输出的格式，日志级别、文件输出的相关配置、控制台输出的相关设置)

3.typeAliases(类型起别名) //一般直接对某个PO的包做别名呢。不会单个类做别名

4.typeHandlers(类型处理器)

5,objectFactory(对象工厂)

6.plugins(插件)

7.environments(环境集合属性对象，与spring框架整合后Say GoodBye)

​	a) environment(环境子属性对象)

​	b) transactionManager(事务管理) //Mybatis使用的事务管理器默认是JDBC，常见的有DHCP、D

​	c) dataSource(数据源)

8.配置映射器(接口与XML文件的映射)位置

```xml
<mappers>
    <package>com.sikiedu.mapper</package>
</mappers>  
```

衔接上面，虽然配置了映射关系后，但是由于一般情况下，我们的配置文件时写在resource目录下的，现在写在src下的话，那么势必会出现导出资源错误（找不到XML）啥的，如下图所示：

![image-20201212155530598](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20201212155949.png)

因为是先生成整个target包后才能运行的，而生成target包的工作是maven来做的，那maven只知道去加载resource目录下的配置文件，如果某些配置文件出现在src的目录下的话，就需要在pom.xml中去额外指定了。



### 1.5、mapper动态代理开发需知

遵守mapper动态代理开发的四大原则 + 一个注意

- 接口名（全包名）要和mapper.xml里的namespace一致

- 接口里的方法名要和mapper.xml中要调用的sql语句的id一致
- 接口里的方法形参的类型要和mapper.xml中指向标签里面的parameterType属性一致
- 接口里方法返回值类型要和mapper.xml中指向标签的resultType一致

- 注意：mapper动态代理开发中，是根据接口的返回值的类型来自动选择的（List类型就返回List）



### 1.6、XML映射器

**mybatis的XML文件里面的具体标签如下：**

- insert：映射插入语句 （标签里面就只能写insert语句了哈）
- delete：映射删除语句
- update：映射更新语句
- select：映射查询语句
- sql：可被其它语句引用的可重用语句块
- resultMap：描述如何从数据库结果集中加载对象，是最复杂也是最强大元素 （一般多表连接得到的结果后需要使用这个标签去指明映射关系）
- cache：该命名空间的缓存配置
- cache-ref：引用其它命名空间的缓存配置



#### 1.5.1、select标签

**简单的案例：**

```xml
<!-- #{} 占位符可以防止SQL注入，运行时自动添加上''单引号的 -->
<!-- #{username,javaType=String,jdbcType=varchar} 占位符可以声明类型，不声明则mybatis自己推断 -->
<!-- ${} 字符串拼接 or 1=1 不会自动增加单引号 写sql语句时需要自己加上单引号 -->
<!-- 模糊查询的话，一定要在SQL语句写死写上前后%，不能等着传进来，万一用户传了一个 or 1=1 就凉瓜皮了 -->
<select id="selectUserByUsername" paramterType="String" result="user">
	select * from user where u_username like "%"#{username}"%"
</select>
```

**具体select标签里面的属性解析：**

```xml
<select
  id="selectPerson"     #指向DAO接口的名称，该属性必选
  paramerType="int"    #方法形参，该属性可选，因为Mybatis可以通过类型处理器(TypeHandler)推断出具体传入语句的参数
  resultType="hashmap"  #接口返参类型，如返参类型是集合的话，只能用其泛型，resultType和resultMap只能同时使用一个 
  resultMap="personResultMap" #值是指向resultMap标签的id，查询若是多表查询，必定需要该标签来指向映射关系
  flushCache="false"  #若其为true，只要语句被调用，都会导致本地缓存和二级缓存被情况，默认值为false
  useCache="true"   #若其为true后，将会导致本条语句的结果被二级缓存缓存起来，默认值：对select元素为true
  timeout="10"     #这个设置时在抛出异常之前，驱动程序等待数据库返回请求结果的秒数。默认值为未设置  
        >
</select>
```

**关于parameterType支持几种类型：**

- 基本类型：直接一对一映射了
- 包装类类型：和基本类型差不多，直接一对一映射了
- 类类型：占位符使用可以直接用该成员变量，也可以使用类名.成员变量名的形式 （如果是集合或数组类型的话，一般是用在collection标签的呢。不会在占位符直接使用的呢。）
- hashmap类型：可以直接用其key填充。



**关于resultMap标签（返回VO类型）：**

```xml
<!-- type属性是返回值的类型，id是唯一标识 -->
<resultMap type="userVO" id="uservoList">
	<id property="u_id" column="u_id" />
    <result property="u_username" column="u_username" />
    <result property="u_sex" column="u_sex" />
    <association property="country" javaType="Country">
    	<id property="id" column="c_id" />
        <result property="c_countryname" column="c_countryname" />
    </association>
</resultMap>
<select id="selectAllUserVO" resultMap="uservoList">
    select user.u_id, user.u_username, user.u_sex, country.c_id, country.c_countryname
    from user left join country on user.u_cid = country.c_id
</select>
```



#### 1.5.2、insert标签

```xml
<!-- mybatis接收参数和springmvc一样，接收参数，如果是类类型的话，可以使用类的成员变量直接接收 -->
<!-- useGeneratedKeys + keyPorperty = 自增完后，会将主键的值回显到给Java对象的id成员变量中。 -->
<insert id="insertUser" parameterType="user" useGeneratedKeys="true" keyProperty="id">
	insert into user values(null,#{u_user},#{u_password},#{u_sex},#{u_createTime},#{u_cid})
</insert>
```



#### 1.5.3、update标签

```xml
<update id="updateUser" parameterType="user">
	update user set u_username = #{u_username} where u_id = #{u_id}
</update>
```



#### 1.5.4、delete标签

```xml
<delete id="deleteUser" parameterType="int">
	delete from user where u_id = #{u_id}
</delete>
```



#### 1.5.5、动态sql的一些常用标签：

**//（更方便地拼接sql语句）**

- if标签：可以在设置条件前判断是否为空或为空串
- where标签：解决if标签拼接字符串AND符号问题
- trim标签：定制where标签的规则（一般很少使用，一般直接使用WHERE来去除前AND）
- set标签：解决update时字符串拼接逗号 “，” 问题
- foreach标签：使用到IN时，右边必定需要这种格式("1","2","3")  可以使用foreach标签遍历拼接
- sql标签：可以提取重复sql语句片段
- choose、when、otherwise标签：相当于java的switch、case、default



**1.介绍if和where标签：**

```xml
<!-- 多条件查询，可能给出三个条件，但命中1个，也可能命中2个，可能都不命中，导致全表查询，所以一定在service层一定要做好非空处理，以免导致全表查询 -->
<!-- 使用where标签可以去除前AND -->
<select id="selectUserListByUser" parameterType="User" resultType="User">
	select * from user
    <where>
    	<if test="u_username != null and u)username!='' ">
        	and u_username like "%"#{u_username}"%"
        </if>
        <if test="u_sex != null and u_sex != '' ">
        	and u_sex = #{u_sex}
        </if>
        <if test="u_cid != null and u_cid != '' ">
        	and u_cid = #{u_cid}
        </if>        
    </where>
</select>
```



**2.下面介绍set标签，去除后逗号：**

```xml
<update id="updateSetUser" parameterType="User">
	update user
    <set>
        <if test="u_username != null and u_username != '' ">
        	u_username = #{u_username},
        </if>
    	<if test="u_password != null and u_password != '' ">
        	u_password = #{u_password},
        </if>
        <if test="u_sex != null and u_sex != '' ">
        	u_sex = #{u_sex}
        </if>
    </set>
    where
    <if test="u_id != null and u_id != '' ">
    		u_id = #{u_id}
   	</if>
</update>
```



**3.下面介绍foreach标签：当入参是集合类型、数组(array)的话，那么使用foreach标签来接收并遍历拼接了。**

入参若直接是list：

```xml
<!-- 使用foreach遍历数组的元素，collection用来接收一整个数组或集合 -->
<!-- 入参若是list，则collection的值取list  -->
<select id="selectUserListByIds" resultType="User">
	select * from user
    where u_id 
    IN
    <foreach collection="list" item="id" open="(" close=")" saparator=",">
    	#{id}
    </foreach>
</select>
```

入参若直接是数组：

```xml
<!-- 入参若是数组，则collection的值取array -->
<select id="selectUserListByIds" resultType="User">
	select * from user
    where u_id 
    IN
    <foreach collection="array" item="id" open="(" close=")" saparator=",">
    	#{id}
    </foreach>
</select>
```

入参若是VO类，但是成员变量是list类型：

```xml
<!-- 入参是VO类，但是成员变量是list类型，直接取该成员变量名即可 -->
<select id="selectUserListByIds" resultType="User">
	select * from user
    where u_id 
    IN
    <foreach collection="listVo" item="id" open="(" close=")" saparator=",">
    	#{id}
    </foreach>
</select>
```



**4.下面介绍一下sql和include标签**

```xml
<sql id="mySelect">
    select * from user
</sql>

<select>
	<include refid="mySelect"></include>
    <where>
		<if test="u_username != null and u_username != '' ">
        	and u_username = #{u_username}
        </if>
	</where>
</select>
```



**5. choose、when、otherwise**

```xml
<!-- List<QyUserPO> getUserList(); -->
<select id="getUserList" parameterType="map" resultType="QyStudentPO">
    select * from qy_student
    <where>
        <choose>
            <!-- when、otherwise组合就像switch或说像if、else if、else一样，匹配到了就不会再匹配下一个 -->
            <when test="classId != null and classId != ''">
                and class_id = #{classId}
            </when>
            <when test="studentName != null and studentName != ''">
                and student_name = #{studentName}
            </when>
            <otherwise>
                and id = 5
            </otherwise>
        </choose>
    </where>
</select>
```





## 2、实操演练

### 2.1、XML配置文件实操

#### 2.1.1、单值传参---简单查询

```java
    //单表单数据查询 通过主键id查询
    public QyClassPO getClassById(@Param("id") int id);
```

```xml
    <!-- 如果XML中有if、when等标签的话，就无法自动匹配，必须在Mapper接口加上@Param("id")手动映射 -->
	<!-- 没有if等标签的话，就可以不用在Mapper接口形参处加上@aram注解手动映射了。 -->
	<select id="getClassById" parameterType="integer" resultType="QyClassPO">
        select * from qy_class c
        <where>
            <if test="id != null and id != '' ">
                c.id = #{id}
            </if>
        </where>
    </select>
```



#### 2.1.2、多值传递---建议用**Map**传递参数

//属性支持基本类型，类类型、数组、List等类型，而且Mapper接口不需要@Param注解即可自动映射

```java
	//Service层将值放入Map
    @Override
    public List<QyClassPO> getClassByMap(Integer id) {
        CSVO csvo = new CSVO();
        csvo.setClassId(2);
        Map<String, Object> map = new HashMap<String, Object>(){{
            put("ids", Lists.newArrayList(1,2,3,4));
            put("className","班级二");
            put("csvo", csvo);
        }};
        return IClassMapper.getClassByMap(map);
    }
	//DAO层定义接口
    public List<QyClassPO> getClassByMap(Map<String, Object> map);
```

```xml
<!-- 判断String类型的话，判断是否为空并且是否空串 -->
<!-- 判断基本类型或数值类型的话，判断是否为null即可 -->
<!-- 判断数组或list类型的话，判断是否为空并且长度是否>0 -->
    <select id="getClassByMap" resultType="QyClassPO">
        select * from qy_class c
        <where>
            <if test="className != null and className != '' ">
                and c.class_name = #{className}
            </if>
            <if test="ids != null and ids.size > 0 ">
                and c.id IN
                <foreach collection="ids" item="id" open="(" close=")" separator=",">
                    #{id}
                </foreach>
            </if>
            <if test="csvo != null and csvo.classId != null and csvo.classId != '' ">
                and c.id = #{csvo.classId}
            </if>
        </where>
    </select>
```



#### 2.1.3、解决查询的列无法自动映射PO的问题

**方法1：查询的列使用as去改成可以映射到PO的字段。**

```java
    /**
     * 查询一条数据取别名
     *
     * @param id
     * @return
     * @throws Exception
     * @author Shen Peihong
     * @date 2021/1/19
     */
    @Select("select id,user_name as userNamea from qy_user where id = #{id}")
    Param1VO selectOne(@Param("id") Integer id) throws Exception;
```



**方法2：使用resultMap手动映射查询列与PO字段的映射关系。**

//单表

```xml
    <!-- 结果集映射，SQL语句查询完毕后，具体返回类型根据这里的type，列与字段的映射规则如下。
         一般无法进行ORM自动映射的才手动映射
     -->
    <resultMap id="rmParam1VO" type="Param1VO">
        <!-- 表的主键id用id标签，其它列用result标签 -->
        <result property="userNamea" column="user_name"/>
    </resultMap>

    <select id="selectOne1" resultMap="rmParam1VO">
        select * from qy_user
        <where>
            <if test="id != null and id != ''">
                AND id = #{id}
            </if>
        </where>
    </select>
```



//多表





#### 2.1.4、Mybatis分页插件

```java
    @RequestMapping("/test2.do")
    public void test1() throws Exception{
        //初始化分页对象存放到ThreadLocal里面的Map中
        PageHelper.startPage(4, 2);
        //使用了分页插件的话，那么SQL就不能再去重复写Limit了，因为Mybatis底层也是通过获取ThreadLocal的值
        List<QyUserPO> list = userService.getAllUser();
        //利用PageInfo的构造方法去get到相关值
        //因为使用了分页插件，所以实际查询完毕的实例类型是Page，继承了ArrayList
        //但继承或实现集合相关接口的话，debug是只能看到0、1、2（列表的值），无法看到其它成员变量
        //故重新定义一个PageInfo对象去接收相关的值呢。
        PageInfo<QyUserPO> pageInfo = new PageInfo<>(list, 3);
        System.out.println(pageInfo);
    }

```



#### 2.1.5、association和collection

1. **collection（一对多）**

```xml
    <!-- 一对多，这里需要注意，<id>标签不写的话，当查出两条后，mybatis可能会出现selectOne()的问题
 		所以这里给自己规定一下，凡是使用resultMap的话，无论id是否自动映射，id标签都不要省略-->
	<resultMap id="ClassVORM" type="ClassVO">
        <id property="id" column="id"/>
        <result property="className" column="class_name"/>
        <collection property="studentPOList" ofType="QyStudentPO" >
            <id property="id" column="student_id"/>
            <result property="classId" column="id" />
        </collection>
    </resultMap>

    <!-- List<StudentVO> getStudent(); -->
    <select id="getClassVO" resultMap="ClassVORM">
        SELECT c.id, c.class_name, s.id AS student_id, s.student_name FROM qy_class c
        INNER JOIN qy_student s ON c.id = s.class_id
        WHERE c.id = 1
        ORDER BY class_id ASC
    </select>
```

2. association（多对一）

```xml
    <resultMap id="StudentVORM" type="StudentVO" autoMapping="true">
        <association property="classPO" javaType="QyClassPO">
            <id property="id" column="class_id"/>
        </association>
    </resultMap>

    <!-- List<StudentVO> getStudent(); -->
    <select id="getStudent" resultMap="StudentVORM">
        SELECT c.id AS class_id, c.class_name, s.id, s.student_name FROM qy_class c
        INNER JOIN qy_student s ON c.id = s.class_id
        ORDER BY class_id ASC
    </select>
```









## 3、Mybatis源码剖析

### 3.1、Mybatis核心组件

1. **Configuration：**用于描述Myabtis的**主配置信息**，其他组件需要获取配置信息时，直接通过Configuration对象获取。除此之外，Myabtis在应用启动时，将Mapper配置信息、类型别名、TypeHandler等注册到Configuration组件中，其他组件需要这些信息时，也可以从Configuration对象中获取。（Mybatis框架启动时，会对所有的配置信息进行解析，然后将解析后的内容注册到Configuration对象的这些**属性**中。）
2. **MappedStatement**：MappedStatement用于描述Mapper中的SQL配置信息，是对Mapper XML配置文件中<select|update|delete|insert>等标签或者@Select/@Update等注解配置信息的封装。

3. **SqlSession**：SqlSession是Myabtis提供的面向用户的API，表示和数据库交互时的会话对象，用于完成数据库的增删改查功能。SqlSession是Executor组件的外观，目的是对外提供易于理解的使用的数据库操作接口。
4. **Executor**：Executor是Myabtis的SQL执行器，Myabtis中对数据库所有的增删改查操作都是由Executor组件完成的。
5. **StatementHandler**：StatementHandler封装了对JDBC Statement对象的操作，比如为Statement对象设置参数，调用Statement接口提供的方法与数据库交互，等等。
6. **ParameterHandler**：当Myabtis框架使用的Statement类型为CallableStatement和PreparedStatement时，ParameterHandler用于为Statement对象参数占位符设置值。
7. **ResultSetHandler**：ResultSetHandler封装了对JDBC中的ResultSet对象操作，当执行SQL类型为SELECT语句时，ResultSetHandler用于将查询结果转换成Java对象。
8. **TypeHandler**：TypeHandler是Myabtis中的类型处理器，用于处理Java类型与JDBC类型之间的映射。它的作用主要体现在能够根据Java类型调用CallableStatement和PreparedStatement对象对应的setXXX()方法为Statement对象设置值，而且能够根据Java类型调用ResultSet对象对应的getXXX()获取SQL执行结果。



### 3.2、Configuration个别重要配置

```yml
mybatis:
  configuration:
    map-underscore-to-camel-case: true  #开启驼峰映射
     #指定Mybatis应如何自动映射列到字段或属性，NONE标识关闭自动映射；PARTIAL只会自动映射没有定义嵌套结果映射的字段。FULL会自动映射任何复杂的结果集（无论是否嵌套），默认值是PARTIAL，我们可以在resultMap标签使用autoMapping=true。标签指定优先级比较好，全局配置优先级稍低
    auto-mapping-behavior: full  
```

























