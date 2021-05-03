# 一、MybatisPlus

## 1、快速入门

> Springboot整合MP

```xml
<!-- MP不侵入的特性，在Springboot项目中引入jar包就可以使用jar包里面封装的东西了。 -->  
<!-- springboot 整合 mybatis-plus -->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.0.5</version>
</dependency>
```

> 自定义的业务Mapper需要去继承继承Mapper并指定泛型(提供了许多基础方法)

```java
/**
 * Created by Shen Peihong on 2021/1/26 2:01
 * Description: 项目中写的Mapper只要继承了MP提供的BaseMapper<泛型>，就可以完成很多单表操作
 *
 * @since 1.0.0
 */
public interface PlusUserMapper extends BaseMapper<User> {
    
}
```



## 2、MP的一些插件

### 2.1、主键生成策略

> @TableId （加在主键id上）

```java
@Data
public class QyPlusUser {
    /*
    	关于type属性的取值问题
        AUTO(0),            //自增，需要数据库设置自增才有效
        NONE(1),            //不做 任何操作
        INPUT(2),           //自己手动set Id了
        ID_WORKER(3),       //利用雪花算法自动生成ID，并且会回填
        UUID(4),            //生成UUID
        ID_WORKER_STR(5);
     */
    // MP提供了很多主键生成策略，只需要你在实体类的id上面加个注解指定后
    @TableId(type = IdType.AUTO)
    private Long id;
}
```



### 2.2、自动填充值

**//小插曲：给一些实体类的属性自动填充值的两种办法：**

- 数据库层面：

// 另外如果填充的值是Date时间类型的话，那么也可以在数据库层面上去设置，不用自己用Java代码去set时间字段。

![image-20210203185430538](https://i.loli.net/2021/02/03/ngHqB1MkEUF2NbI.png)



- 代码层面

> @TableField （加在属性上）

**i. 属性1**：fill

作用：在某个属性上添加注解后指定这个属性，可以指定属性的填充策略（MP定义了一个接口，需要自己写实现类，把填充策略写在规定的方法上，到时候调用MP的增、改方法时，底层操作你这个实体类时，会利用反射拿到你的注解，知道了你开启自动填充的话，会根据你的类型（你是INSERT、UPDATE时才去利用填充策略）去找对应的填充策略方法。进而对该属性进行填充值了。）

```java
    @TableField(fill = FieldFill.INSERT)
    private Date createTime;

    @TableField(fill = FieldFill.INSERT_UPDATE)
    private Date updateTime;
```

```java
package com.sph.practice.mybatisplus.handle;

import com.baomidou.mybatisplus.core.handlers.MetaObjectHandler;
import org.apache.ibatis.reflection.MetaObject;
import org.springframework.stereotype.Component;

import java.time.LocalDateTime;
import java.util.Date;

/**
 * Created by Shen Peihong on 2021/2/3
 * Description: MP定义了这个接口，以及在这个接口里面定义了许多的方法，需要你把一些填充策略写在定义的方法里边
 * MP提供的增、改方法里边，会去识别你提供的实体类类型，拿到对应的反射对象，然后看你的属性是否用到了@TableField注解，
 * 进而再继续确认你是否用到了fill数据，进而再确认你是否用了INSERT或UPDATE或INSERT_UPDATE，如果是的话，那么就去Spring的IOC容器中
 * 扫描出实现了MetaObjectHandler这个接口的实现类，然后执行对应的生成策略方法，
 * 这样就能成功地set指定的值到某个变量中了(一定是有注解声明的变量哈) 因为如果你加了注解，并且也打开了策略，但是你在书写生成策略
 * 的方法中没有去写生成策略(给某个属性set值)，那么也是无法给属性set值成功的哈
 * 还有一点：
 * 定义的接口有个参数是MetaObject类型的，也就是说到时候底层调用你这个方法的时候，会传这么一个实例化对象给你
 * 你具体就要看看这个对象是干什么用的，里面有什么方法，什么属性，因为一般都是一个引用类型的类类型
 * 需要你set一下里面的属性，或提供给你get方法拿到里面的某些属性或是处理逻辑处理后的值啥的。
 * 总之就是这个实例化对象里面都会提供许多方法，去set值或get值，或是提供一些有的没的的方法
 *
 * @since 1.0.0
 */
@Component
public class MyMetaObjectHandler implements MetaObjectHandler {

    // 当注解@TableFiled的属性fill的值为INSERT时的填充策略
    @Override
    public void insertFill(MetaObject metaObject) {
        this.setFieldValByName("createTime", new Date(), metaObject);
        this.setFieldValByName("updateTime", new Date(), metaObject);
    }

    // 当注解@TableFiled的属性fill的值为UPDATE或INSERT_UPDATE时的填充策略
    @Override
    public void updateFill(MetaObject metaObject) {
        this.setFieldValByName("updateTime", new Date(), metaObject);
    }

}
```



### 2.3、乐观锁

> @version（乐观锁）

Java实体类

```java
    @Version
    private Integer version;
```

Configuration类

```java
    /**
     * 注册乐观锁拦截器的bean到Spring容器中，MP执行update的方法时，会识别到是哪个实体类，然后拿到反射，判断字段有version字段的话
     * 会向Spring容器拿我们规定好了的乐观锁拦截器先处理一波（可能是用到了该bean里面的方法）
     *
     * @return
     */
    @Bean
    public OptimisticLockerInterceptor getOptimisticLockerInterceptor() {
        return new OptimisticLockerInterceptor();
    }
```

测试类

```java
    /**
     * 测试乐观锁，
     * 修改完毕后，没及时update，这个时候也被其它线程给update了，那么我慢了一点，还能Update成功吗？
     */
    @Test
    public void versionTest1() {
        QyPlusUser user1 = plusUserMapper.selectById(1354836048880775173L);
        user1.setName("version");

        // 模拟另外一个线程B，在A线程还没update的时候，结果B成功了，A没成功
        // 因为当A执行的时候，判断乐观锁字段版本对不上，所以本次执行update语句失败
        QyPlusUser user2 = plusUserMapper.selectById(1354836048880775173L);
        user2.setName("niubi");
        int row2 = plusUserMapper.updateById(user2);
        System.out.println(row2);

        int row1 = plusUserMapper.updateById(user1);
    }
```



### 2.5、分页插件

```java
    /**
     * 分页插件
     *
     * @return
     */
    @Bean
    public PaginationInterceptor getPaginationInterceptor(){
        PaginationInterceptor paginationInterceptor = new PaginationInterceptor();
        paginationInterceptor.setDialectType("mysql");
        return paginationInterceptor;
    }
```

// 只要加上该插件，则调用相关分页方法时，会执行SQL后面添加limit



### 2.4、逻辑删除

> @TableLogic （逻辑删除）

Java类

```java
    @TableLogic
    private Integer deleted;
```

配置类

```java
    /**
     * 逻辑删除
     *
     * @return
     */
    @Bean
    public ISqlInjector getSqlInjector(){
        return new LogicSqlInjector();
    }
```

配置文件

```yml
mybatis-plus:
  global-config:
    db-config:
      logic-delete-value: 1
      logic-not-delete-value: 0
```

执行delete方法后是一个update语句

```sql
-- update语句
UPDATE qy_plus_user SET deleted=1 WHERE id=? AND deleted=0 

-- select语句 （增加条件deleted = 0，过滤掉deleted=1,被逻辑删除了的数据）
SELECT id,name,age,email,version,deleted,create_time,update_time FROM qy_plus_user WHERE deleted=0 
```



### 2.5、SQL执行效率性能插件

```java
    /**
     * SQL执行效率插件（可以设置SQL执行多少秒后超时、格式化SQL等）
     *
     * @return
     */
    @Bean
    @Profile({"dev", "test"})
    public PerformanceInterceptor getPerformanceInterceptor(){
        PerformanceInterceptor performanceInterceptor = new PerformanceInterceptor();
        performanceInterceptor.setMaxTime(1); //ms 设置sql执行的最大时间，如果超过了则不执行
        performanceInterceptor.setFormat(true); //格式化SQL
        return performanceInterceptor;
    }
```



### 2.6、代码自动生成器

```java
    /**
     * 代码自动生成器 根据数据库的字段，自动生成相关pojo、controller、service、dao
     * 只需你提前建好表，即可自动生成相关代码
     *
     * @param args
     */
    public static void main(String[] args) {
        AutoGenerator mpg = new AutoGenerator();
        // 1.全局配置
        GlobalConfig gc = new GlobalConfig();
        gc.setAuthor("Shen Peihong"); //设置类上面的作者
        String projectPath = System.getProperty("user.dir"); //获取当前项目的路径
        gc.setOutputDir(projectPath + "/src/main/java"); //设置代码输出目录
        gc.setFileOverride(false);// 是否覆盖同名文件，默认是false
        gc.setSwagger2(true); //开启Swagger相关注解
        gc.setOpen(false); //代码运行完毕后，是否打开Windows资源管理器
        gc.setIdType(IdType.ID_WORKER); //主键生成策略使用雪花ID

        mpg.setGlobalConfig(gc);

        // 2.数据源配置
        DataSourceConfig dsc = new DataSourceConfig();
        dsc.setDbType(DbType.MYSQL); //连接数据库的类型
        dsc.setDriverName("com.mysql.cj.jdbc.Driver"); //数据库驱动
        dsc.setUsername("root"); //数据库账号
        dsc.setPassword("root"); //数据库密码
        dsc.setUrl("jdbc:mysql://localhost:3306/db_mybatis?serverTimezone=GMT%2B8&useUnicode=true&characterEncoding=utf8"); //数据库链接

        mpg.setDataSource(dsc);

        // 3.策略配置
        StrategyConfig strategy = new StrategyConfig();
        strategy.setInclude("qy_plus_"); // 需要进行映射的表
        strategy.setTablePrefix("qy_plus_"); // 表名前缀
        strategy.setNaming(NamingStrategy.underline_to_camel);// 表名生成策略
        strategy.setColumnNaming(NamingStrategy.underline_to_camel); // 列名生成策略
        strategy.setLogicDeleteFieldName("deleted"); //逻辑删除字段名
        strategy.setVersionFieldName("version"); //乐观锁字段名
        TableFill createTime = new TableFill("create_time", FieldFill.INSERT);
        TableFill updateTime = new TableFill("update_time", FieldFill.INSERT_UPDATE);
        strategy.setTableFillList(Arrays.asList(createTime, updateTime)); //自动填充值

        mpg.setStrategy(strategy);

        // 4.包配置
        PackageConfig pc = new PackageConfig();
        pc.setParent("com.sph.practice.mybatisplus");
        pc.setModuleName("auto");
        pc.setController("controller");
        pc.setService("service");
        pc.setMapper("mapper");
        pc.setEntity("pojo");
        mpg.setPackageInfo(pc);

        // 5.执行生成
        mpg.execute();

    }
```





## 3、Wrapper

作用：条件构造器（负责构造条件对象）



## 4、BaseMapper

> 1、int insert(T entity);   //单表插入一条数据

```java
    /**
     * int insert(T entity);
     */
    @Test
    public void insertTest() {
        QyPlusUser user = new QyPlusUser("3", 3, "3");
        //允许自己不set进ID，调用insert方法，方法底层会默认使用雪花算法去生成id，并且id也会set到传入的user对象
        //不希望走默认的话，也可以更改传入实体类的主键id生成策略，使用@TableId注解
        int row = plusUserMapper.insert(user); //返回值为影响的行数
        System.out.println(user.getId()); //插入完后会回填主键id的
    }
```



> 2、int deleteById(Serializable id);   //删除某条数据

```java
    /**
     * int deleteById(Serializable id);
     * 删除某条数据（要看具体泛型是什么类型哈，别删错了）
     * 入参声明成Serializable是因为Number类型实现了这个接口，向上转型嗷
     */
    @Test
    public void deleteByIdTest(){
        // 删除某条数据（要看具体泛型是什么类型哈，别删错了）
        // 返回值是影响的条数
        int row = plusUserMapper.deleteById(1354833168517992450L);
    }
```



> 3、int deleteByMap(@Param("cm") Map<String, Object> columnMap);   //删除数据，可以指定条件在Map

```java
    /**
     * int deleteByMap(@Param("cm") Map<String, Object> columnMap);
     * 删除数据，可以在Map添加指定的列与列对应的值
     * 若Map有多个键值对的话，到时候SQL语句是用AND连接条件的
     * 到时候实际是where 指定列名1 = 列值1 AND 指定列名2 = 列值2
     */
    @Test
    public void deleteByMapTest(){
        Map<String, Object> paramMap = Maps.newHashMap();
        // Java类型与数据库字段类型最好匹配(不然存在隐患)
        paramMap.put("name", "3");
        paramMap.put("age", 2);
        int row = plusUserMapper.deleteByMap(paramMap);
        System.out.println(row);
    }
```



> 4、int updateById(@Param("et") T entity);  //更新某条数据(需指定id额)

```java
    /**
     * int updateById(@Param("et") T entity);
     */
    @Test
    public void updateTest(){
        //更新主键id为：1354836048880775171 的数据
        QyPlusUser user = new QyPlusUser("3", 3, "3");
        user.setId(1354836048880775171L);
        user.setName("123321");
        int row = plusUserMapper.updateById(user);
        System.out.println(row);
    }
```



> 5、List<T> selectByMap(@Param("cm") Map<String, Object> columnMap); 

```java
    /**
     * List<T> selectByMap(@Param("cm") Map<String, Object> columnMap);
     */
    @Test
    public void selectByMapTest() {
        // 用map设置条件查询 多个条件用AND连接
        Map<String, Object> paramMap = Maps.newHashMap();
        paramMap.put("email", "3");
        paramMap.put("age", "3");

        List<QyPlusUser> userList = plusUserMapper.selectByMap(paramMap);
        userList.forEach(System.out::println);
    }
```



> 6、List<T> selectList(@Param("ew") Wrapper<T> queryWrapper);

```java
    /**
     * Wrapper是底层接口，具体许多条件构造器的方法封装到抽象类AbstractWrapper
     */
    @Test
    public void queryWrapperTest() {
        // 查找年龄大于17岁，名字是J开头的数据
        QueryWrapper<QyPlusUser> wrapper = new QueryWrapper<>();
        wrapper.gt("age", 17);
        wrapper.likeRight("name", "J");

        List<QyPlusUser> userList = plusUserMapper.selectList(wrapper);
        userList.forEach(System.out::println);
    }
```



> 7、Integer selectCount(@Param("ew") Wrapper<T> queryWrapper);

```java
    /**
     * Wrapper是底层接口，具体许多条件构造器的方法封装到抽象类AbstractWrapper
     */
    @Test
    public void queryWrapperCountTest() {
        // 查找年龄大于17岁，名字是J开头的数据
        QueryWrapper<QyPlusUser> wrapper = new QueryWrapper<>();
        wrapper.gt("age", 17);
        wrapper.likeRight("name", "J");

        Integer row = plusUserMapper.selectCount(wrapper);
        System.out.println(row);
    }
```



