# 一、mysql初级篇

## 1、MYSQL概念理解

### 1.1、数据库设计

需求 实体 实体的属性 实体之间的关系 画E-R图（visio）  三大范式数据库设计表（powerDesigner）

矩形-实体  椭圆-属性  菱形-关系

### 1.2、三大范式

第一范式(1NF)：确保每列的原子性，即每列都是不可再分的最小数据单元(具体问题具体分析，比如中国北京市可以分成两列中国、北京)

第二范式(2NF)：满足第一范式的基础上，每个表(一个表代表一个实体)只描述一件事情，

第三范式(3NF)：满足第二范式的基础上，除主键外的每列和主键都是直接关系，不是间接关系

//**判断实体之间的关系要建立在同一时间下这个条件**



### 1.3、数据库表与字段设计的一些策略

**关系型数据库的三种关系：**

- **一对一**：（一行数据，也即是一条账连接指向对应一条）：学生 与 身份证号

- **一对多**：("一"的表里面的一条数据可以连接指向"多"的表里面多条数据，所以两个表之间的关系是一对多)：母亲 与 孩子

- **多对多**：（A表中一条数据对应B表中多条数据，B表中的一条数据也对应A表中的多条数据，故为关系是多对多 由于两个表设计在一起的话，会产生很多重复的数据，故需要新建一个中间表，两个表的主键都在中间表中作为外键，中间表记录着A表和B表之间的连接指向关系）：学生 与 课程

1.如果每次查询信息表都是查询所有数据，不常用的数据就会影响效率，实际又不用，那我们可以将信息表分割成常用信息表+不常用信息表（他们都有共同的ID，来将两个表连接在一起 可以认为两个表是一对一的关系）

- 常用信息表：ID(p)，姓名，性别，年龄，身高，体重

- 不常用信息表：ID(p)，籍贯，家庭住址，紧急联系人

2.一对多（"一"的表的主键在"多"的表里面当外键，同一个外键的值可以在"多"的表中出现多次，因为两个表之间的关系是一对多关系）

母亲表：ID(p)，名字，年龄，性别

孩子表：ID(p)，名字，年龄，性别，母亲表ID(母亲表的主键)

查询母亲的个人信息的话可以查询母亲表

查询孩子的个人信息的话可以查询孩子表

通过母亲ID找对应的孩子，查询孩子表   通过孩子ID找对应的母亲表 ---》 查询的涉及到两表之间的关系的话，查找"多"的表

3.多对多(表A数据一对多表B数据、表B数据一对多表A数据)

表A <---- 中间表 ---->   表B

![image-20201222175404512](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222175410.png)

### 1.4、=和<=>的区别

对于=操作符，两个值被比较，结果是0(不相等)或者1(相等)

=操作符不可以和NULL比较，但是<=>可以代替is 和NULL比较



### 1.5、mysql常用函数

select 查询列表    查询列表可以是 * 、字段、函数、表达式

**字符函数**：length(str)、concat(str1，' '，str2)、upper(str)、lower(str)、instr(email,'@') 第一次遇到@的索引 、 substr( email，1，instr(email，'@')-1 ) 截取字符串

**数字函数**：round 四舍五入  select round(1.65)

**日期函数**：

- now()：返回当前的日期+时间

- curdate()：返回当前的日期（不包含时间）

- curtime()：返回当前的时间（不包含日期）

**其它函数**：IF(条件表达式，true执行的，false执行的) //类似于三目运算符

**分组函数**：sum(1)求和   avg平均值  max最大值  min最小值  count计算个数  //比如sum相当于把第一列的所有值全部相加 使用这些函数需要传参，参数为某列



## 2、MYSQL数据类型

**1、数值型**

- 整型：tinyint(1)、smallint、mediumint、int、bigint
- 小数：定点数、浮点数

**//补充**

**数值型：**
    **整数：**
            Tinyint （1）(-128~127或0~255，二进制最高位是符号位，后7位是数值位，主要看是有符号还是无符号)
            Smallint （2）
            Mediumint （3）
            Int、Integer （4）
            Bigint（8）
    **小数：**
            定点数：
                DECIMAL（M，D）：最大取值范围与double相同，给定decimal的有效取值范围由M和D决定
            浮点数：
                float（M，D）：4个字节  //M代表数字总位数，D是小数点后面的位数  ，若不写的话，那么按默认值来 float的范围大约精确到7位小数   例子：比方说float(5,2) 插入111.22 可以成功  ，但是如果插入的是1000.1的话，那么最终入库是999.99 临界值（因为超过字段类型规定的范围                    了）  //如果是数值类型的话，超过字段类型的话，会范围最大临界值，如果是字符类型的话，那么会报错 比方是TEXT类型的话
                double（M，D）：8个字节
  **解释M和D代表的意义**
M：整数部位+小数部位
D：小数部位(位数)              
//如果超过范围，则插入临界值 
//M和D都可以省略，特别注意DECIMAL整个定点类型，如果省略的话，那么默认是decimal(10,0) , 如果是float和double，则会根据插入的数值的精度来决定精度

**使用场景：**定点型的精确度较高，如果要求插入数值的精度较高如货币运算等则考虑使用decimal



**2、字符型**

- 较短的文本：char、varchar(可变长)
- 较长的文本：text、blob（较长的二进制数据）



**3、时间类型：(mysql中有三种日期类型)**

- date(yyyy-MM-DD) **2020-05-22**
- time(HH:MM:SS) **11:27:30**

- datetime(YYYY-MM-DD HH:mm:SS) **2020-05-22 11:27:30** //具体格式由Java的格式化类型去指定 Java类型和mysql类型可以相互映射(Java只管和mysql类型保持一致，到时候即可封装入库)

- year(YYYY) 2020
- timestamp(YYYYMMDD NNMMSS) 20200522 112730 (也可以保存年月日时分秒，但是是连起来的邮戳 一般不经常用，只能保存到1970-2037年 唯一的优点：1.跨时区、而且数据被update的时候， 2.该字段会自动更新当前时间)

//Mysql中Date、DateTime、TimeStamp、Time的比较

| 名称      | 显示格式            | 显示范围               | 应用场景                                           | 后台取值                                     |
| --------- | ------------------- | ---------------------- | -------------------------------------------------- | -------------------------------------------- |
| Date      | YYYY-MM-DD          | 1000-01-01到9999-01-01 | 当业务需求中只需要精确到天时，可以用这个时间格式   | @JSONField(format="yyyy-MM-dd")              |
| DateTime  | YYYY-MM-DD HH:mm:ss | 到9999-12-31 23:59:59  | 当业务需求中需要精确到秒时，可以用这个时间格式     | @JSONField(format="yyyy-MM-dd HH:mm:ss")     |
| TimeStamp | YYYY-MM-DD HH:mm:ss | 到2038年               | 当业务需求中需要精确到毫秒时，或者系统用于不同时区 | @JSONFiled(format="yyyy-MM-dd HH:mm:ss:SSS") |
| Time      | HH:mm:ss            | 00:00:00到23:59:59     | 当业务需求中只需要每天的时间，可以用这个时间格式   | @JSONFiled(format="HH:mm:ss")                |

//java定义PO类变量时，使用注解格式化



**关于Mysql中字段值为null被取出时**

1、Jdbc底层的结果集，使用get类型()去接收对应的类型字段，形参可以传int或String类型，int类型的话则对应下标,String类型的话则对应列名

2、当数据库字段为null值被取出时，使用不同java类型去接收，则得到的值都各不相同，不一定全是null当使用getString()使用String类型去接收NULL值时，如果Mysql字段的类型是varchar、char的话，那么取出来是null值，当使用getInt()、getFloat()方法去取值时，当mysql字段是int、float等类型时，返回值是0.



**Java类型和Mysql类型的映射(mybatis有时候需要手动指向类型)**

| java      | mysql               |
| --------- | ------------------- |
| String    | varchar、char、TEXT |
| Long      | int                 |
| util.date | DATETIME            |



## 3、Mysql函数

```txt
Mysql的一些函数：now()：当前日期+时间     curdata()：当前日期     curtime()： 当前时间     pi()：派     sqrt()：求平方根     mod(30,9)：求余数           
round()：四舍五入     round(3.14,1)：四舍五入保留一位小数     floor()：直接舍     ceiling：直接入     
Mysql查询的时候可以对查询的列做一些运算：*     /     div     %     mod     +     -
Mysql字符串操作：
select concat("a","b")    ==>  ab
select left("abc",2)     ==> ab
select length("abc")     ==> 3
select reverse("abc")     ==> cba
select replace("abc","a","11")     ==>11bc
select date_format("2019-07-04","%y/%m/%d")     ==> 2019/07/04

判断一些字符串的值是否为Null（is not null）：select * from user where username is not null;
select * from user where id in (1,2,3);
select * from user where id between 1 and 6

数据库的表的备份（import   export  导入导出）

select 查询列表       查询列表可以是 * 、字段、函数、表达式
字符函数：length(str)、concat(str1，' '，str2)、upper(str)、lower(str)、instr(email,'@') 第一次遇到@的索引 、 substr( email，1，instr(email，'@')-1 ) 截取字符串

数字函数
round 四舍五入   select round(1.65)
日期函数
now():返回当前的日期+时间
curdate():返回当前的日期（不包含时间）
curtime():返回当前的时间（不包含日期）
其它函数
IF(条件表达式，true执行的，false执行的)  //类似于三目运算符
分组函数
sum(1)求和     avg平均值    max最大值    min最小值    count计算个数   //比如sum相当于把第一列的所有值全部相加  使用这些函数需要传参，参数为某列

去重distinct搭配
select distinct username;  //对username字段去重
select sum(distinct salary);  //去重

关于count函数的详细介绍
count(*)：对全部列统计行数(如果有一行不全为null的话，都会加1)
count(1)：对第一列统计行数 （统计不为null的值）

exists函数 (里面加一个查询的SQL语句)  判断是否有查出来结果，返回值是布尔值（0表示无，1表示有）
可以用exists的，也可以用In 用法一样的
select exists(select employee_id from employees where salary=30000)
案例：查询没有女朋友的男神信息
select bo.* from boys bo where bo.id not in (select boyfriend_id from beauty)
```



## 4、DDL语句

### 4.1、库的管理

操作：创建、修改、删除

#### 4.1.1、库的创建

```sql
create database if not exists books;
```

#### 4.1.2、库的修改

```sql
-- 更改数据库的字符集
alter database books character set gbk;
```

#### 4.1.3、库的删除

```sql
-- 库的删除
drop database if exists books;
```



### 4.2、表的管理

操作：创建、修改、删除

#### 4.2.1、关于表的创建 

create table 表名{

   列名 列的类型 [(长度) 约束],

   列名 列的类型 [(长度) 约束],

   ...

   列名 列的类型 [(长度) 约束]

};

**//关于字段常见的约束详解**

含义：对字段的限制，用于限制表中的数据，为了保证表中的数据的准确和可靠性

**分类：六大约束**

  NOT NULL：非空，用于保证该字段的值不能为空 //比如姓名、学号等

  DEFAULT：默认，用于保证该字段有默认值  //比如性别

  主键：用于保证该字段的值具有唯一性 + 非空 //比如主键ID 学号、员工编号、部门ID、学生ID

  UNIQUE：唯一，用于保证该字段的值具有唯一性，可以为空 //比如座位号

  CHECK：检查约束【mysql中不支持】

   FOREIGN KEY：外键，用于限制两个表的关系，用于保证该字段的值必须来自于主表的关联列的值 //在从表添加外键约束，用于引用主表中某列的值

//外键：

- 要求在从表设置外键关系

- 从表的外键列的类型和主表的关联列的类型要求一致或兼容，名称无要求

- 主表的关联列必须是一个key(一般是主键或唯一)

- 插入数据时，先插入主表，再插入从表。

​       删除数据时，先删除从表，再删除主表。



#### 4.2.2、关于表的修改 

1、修改列名

```sql
alter table book change column publishdate pubDate DATETIME;
```

2、修改列的类型或约束

```sql
add table book modify column pubdate timestamp;
```

3、添加新列

```sql
alter table author add column annual double;
```

4、删除列

```sql
alter table author drop column annual;
```

5、修改表名

```sql
alter table author rename to book_author;
```

#### 4.2.3、关于表的删除

```sql
drop table if exists book_author;
```



### 4.3、字段的管理

#### 4.3.1、新增字段(SQL语句)

```sql
-- 新增字段注释以及在哪个字段后面 并且设置默认值
alter table tb_qy_examination_exam_ext 
add column bb tinyint default '0' comment '我是bb' after aa;
```





## 5、DML语句

### 5.1、表的创建

```sql
create table mytable{
   username varchar(20),
   password varchar(20),
   primary key(id);
};
```

### 5.2、表的删除

```sql
drop table mytable;
```

### 5.3、SQL基本增删改查

```sql
-- 增
insert into user (username,password) values("123","123");
-- 方式一：经典的插入
-- 语法： insert into 表名 (列名...) values (值...);    //看字段的类型，如果该字段的非空的话，那么该列一定要插入值，不然会报错的。
-- 1.插入的值的类型要与列的类型一致或兼容
-- 2.不可以为null的列必须插入值，可以为null的列插入Null即可
-- 3.列的顺序可以调换，但是赋的值也要对得上
-- 4.列数和赋的值的个人必须要一致
-- 5.可以省略列名，默认所有列，而且列的顺序和表中列的顺序一致

-- 方式二：
-- insert into 表名 set 列名 = 值，列名 = 值 ；  //这个方式和我们的update的SQL语句有点相似
```

```sql
-- 删
delete from user where id = 1;
-- 语法：delete from 表名 where 筛选条件
```

```sql
-- 改
update user set username = 123 , password = 123 where id = 1;
-- 语法： update 表名 set 列=新值，列=新值....  where 筛选条件 //一定的加where筛选的条件，不然的话就会修改整个数据库的了
```

```sql
-- 查
select * from user where id = 1;
```



### 5.4、SQL语句查询(各种条件)

**where不能和分组group by 结合使用  因为where不可以和聚合函数结合使用 （ Count()、MAX()、MIN()、SUM()、AVG() ）**     **如若需要分组筛选的话 group by 后面结合having字句(having是判断组是否能留下 非0即1)**

**单表查询：**

```sql
-- 查询根据id列降序 
select * from user where id<5 order by id desc;
```

```sql
-- 查询某列，并将该列更换名称 
select username as u_username from user where id = 1;
```

```sql
-- 查询username并且新增一列名为:地址  值为:唐山 
select username,"唐山" as "地址" from user;
```

```sql
-- 模糊查询 
select * from user where username like '%张%';
```

```sql
-- 某个范围内查找 
select * from user where id between 1 and 3;
```

```sql
-- 在列举的值中查找 
select * from user where id in (1,2,3,4);
```

```sql
-- 对性别进行分组查询 
select sex as "性别",group_concat(username) as "用户名" from user group by sex
```

```sql
-- 对性别进行分组查询(添加条件) 
select sex as "性别",group_concat("username") as "用户名"from user group by sex having Count(id) >3;
```

```sql
-- 分组后group_cancat连接的元素按某个字段升序排序，并且改变分隔符为 ; 分号
SELECT GROUP_CONCAT(goodsName ORDER BY price ASC SEPARATOR ';') FROM goods GROUP BY category
```

**多表查询：**

```sql
-- 内连接  （隐式）
-- 在where字句中指定连接条件   
SELECT user.`id` AS id1,user1.`id` AS id2 FROM USER,user1 WHERE user.`username` = user1.`username` 
```

```sql
-- 内连接  （显式）
-- join..on (条件表达式)
select user.'id',user1.'id' from user inner join user1 on user.'username' = user1.'username'
```

```sql
-- 外连接
-- 左外连接
SELECT user.`id`,user1.`id` FROM USER LEFT OUTER JOIN user1 ON user.`username`=user1.`username`
-- 右外连接
SELECT user.`id`,user1.`id` FROM USER RIGHT OUTER JOIN user1 ON user.`username`=user1.`username`
```



**浅谈多表查询(笛卡尔乘积、内连接、外连接)**

```txt
1.笛卡尔乘积查询：select * from dept,emp
将两张表的记录进行一个相乘的操作查询出来的结果就是笛卡尔积查询，如果左表有n条记录，有表有m条记录，笛卡尔积查询有n*m条记录，
其中往往包含了很多错误的数据，所以这种查询方式并不常用
2.内连接查询：select * from dept,emp where dept.id = emp.dept_id （隐式）或 select * from dept inner join emp on dept.id = emp.dept_id（显式）
查询的是左边表和右边表都能找到对应记录的记录(就是再笛卡尔乘积的基础上去增加条件，找到交集连接起来)
3.左外连接查询：select * from dept left join emp on dept.id = emp.dept_id
在内连接的基础上增加左边表有而右边表没有的记录（所以左边有记录显示，右边的表记录都是null  总记录行数等于左边行数）
4.右外连接查询：select * from dept right join emp on dept.id = emp.dept_id
在内连接的基础上增加右边表有而左边表没有的记录（所以右边有记录显示，左边的表记录都是null  总记录行数等于右边行数）  
5.全外连接：select * from dept full join emp on dept.id = emp.dept_id （mysql不支持全外连接）
但是可以用union关键字模拟全外连接：(相当于内连接 + 左不null右null + 左null右不null的情况)
select * from dept left join emp on dept.id = emp.dept_id
union
select * from emp right join emp on dept.id = emp.dept_id
6.三张表的连接查询(使用inner join on 内连接的话，那么on的作用其实和where的作用差不多)
select * from (school s inner join teacher t on s.s_id = t.s_id) inner join children c on c.c_id = 1
格式：from (表1 inner join 表2 on 条件) inner join 表3 on 条件
```

![image-20201213235812431](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201213235814.png)

```sql
-- 查询所在列的去重数据
select distinct username,password from user;
```

带子查询的删除语句(Sno 外键)

```sql
-- 多表连接的：删除计算机系学生的选课课程信息
delete from SC where Sno in (select Sno from Student where Sdept = 'CS')
```

```sql
-- 查询表后面跟limit是代表限制查询前几行
select * from user limit 4
```

```sql
-- (删除前10条 查询第11-20)  常用于分页查询
select * from user limit 10,10
```

```sql
-- 安全等于
select * from user where bonus is null 等价于 select * frm user where bonus <=> null
```



### 5.5、关于多表查询的分类

内连接：

- 等值连接

- 非等值连接

- 自连接

外连接：

- 左外连接

- 右外连接

- 全外连接

交叉连接



### 5.6、分页查询

```txt
应用场景：当要显示的数据，一页显示不全，需要分页提交sql请求
语法：
    select 查询列表    7
    from 表1    1
    [(join type) join 表2    2
    on 连接条件    3
    where 筛选条件    4
    group by 分组字段    5
    having 分组后的筛选    6       
    order by 排序的字段]    8
    limit [offset]，size    (MYSQL索引是从1开始的，但是这里比较特殊，这里的起始索引从0开始)    9
    
    
//所以一般不看select 先看from 再根据后面多表连接后对连接完的结果进行on连接条件(两个表得以连接的条件，通过ID) ，然后再where筛选条件(不符合的行直接就被去掉)，然后再根据当前行进行分组(若不进行分组的话，那么默认就是把当前的行数划分到了一组里面)  

//关于左连接：on后面的条件创建一张虚拟表，左边表是全部数据，右边表会根据on后面的条件进行筛选
//结论：筛选右表的条件和左右表关联的条件，写在on中，筛选左表的条件写在where中

例子：pageSelect(page,size)    --->   select * from user limit (page-1)*size , size  // +1起始索引,查几条

#进阶9：联合查询 (分别查询多个表的信息)
特点：
1.要求多条查询语句的查询列数是一致的
2.要求多条查询语句的查询的每一列的类型和顺序最好一致
3.union关键字默认去重，如果使用union all 可以包含重复项
/*
union 联合 和合并：将多条查询语句的结果合并成一个结果
语法：
查询语句1
union
查询语句2
union
......
*/
```



## 6、事务

概述：事务由单独单元的一个或多个SQL语句组成，在这个单元中，每个MYSQL语句是相互依赖的。

### 6.1、事务的acid属性

- 原子性（Atomicity）：事务中的操作要么都发生，要么都不发生
- 一致性（Consistency）：结果一定要相同，A有1000，B有1000， 无论如何转账  最后余额都是2000
- 隔离性（Isolation）：执行程序中可能多个事务并发执行，隔离指一个事务执行隔离其它事务 (隔离级别)
- 持久性（Durability）：事务一旦提交，它对数据库中数据的改变就是永久性的，不可逆转(回滚)的，除非再新加事务去影响



### 6.2、事务的创建

**隐式事务：事务没有明显的开启和结束的标记**

比如insert update delete语句（一条SQL当成是一个事务）//一条SQL语句如果没有关闭自动提交事务的话，那么默认情况下，执行完一条语句相当于提交一个事务

//可以自己在Mysql使用 show variable like "%autocommit%"查看是否是on 开启的



**显式事务：两条或两条SQL语句当成一组SQL执行**

前提：必须先设置自动提交功能为禁用(set autocommit=0) //因为如果没有关闭自动提交的话，那么执行完一条增删改语句的话，就相当于执行完一个事务了

开启事务的语句；

update 表 set 张三丰的余额=500 where name='张三丰'
update 表 set 郭襄的余额=1500 where name = '郭襄' 
结束事务的语句；



MYSQL启用事务步骤

步骤1：开启事务

**set autocommit=0;**
**start transaction;//可选的**

步骤2：编写事务中的sql语句(select insert update delete)
语句1；
语句2；
...

步骤3：结束事务
**commit;//提交事务**
**rollback;//回滚事务**

若发生事务锁死，只能查找出来，强制kill掉锁住的事务。

避免事务死锁的方法





对于同时运行的多个事务，当这些事务访问数据库中相同的数据时，必须采取必要的隔离机制（类似于多线程访问同一资源时，需要设置同步锁）

同时运行多个事务时，若没有采用隔离级别的话，那么可能出现以下的问题：

**1.脏读：**事务T1读取了事务T2更新的数据**(但还没提交事务)**，但是后来事务T2又回滚了，所以T1读取的内容就是**临时且无效**的。
//小乔想给张飞赚50，但是转成了500，张飞刚好看到了500，此时小乔还没提交事务，就赶紧回滚事务，并且改成了转账50，并提交事务，此时张飞再次刷新看到了50，所以刚刚看到的500就是脏读。
**2.不可重复读**：对于两个事务T1，T2，  T1读取了一个字段，然后T2更新了该字段.之后，T1再次读取同一个字段，值就不同了.
//小乔转账500给张飞，但是没有提交，此时张飞看到了500，但是小乔后面又回滚事务，重新修改成了50并提交事务，此时张飞再次刷新就又变成了50。
3.幻读：对于两个事务T1，T2，  T1从一个表中读取了一个字段，然后T2在该表中插入了一些新的行.之后，如果T1再次读取同一个表，就会多出几行。



**数据库提供的4种事务隔离级别：**

1.READ UNCOMMITTED（读未提交数据）：允许事务读取未被其他事务提交的变更，脏读、不可重复度和幻读的问题都会出现
**2.READ COMMITED（读已提交数据）**：只允许事务读取已经被其它事务提交的变更，可以避免脏读，但不可重复度和幻读问题仍然可能出现
3.REPEATABLE READ（可重复读） ：可以避免脏读和不可重复读，但幻读的问题仍然存在  **//默认的隔离级别**
4.SERIALALIZABLE（串行化）：所有并发问题都可以避免，但性能十分低下
**//隔离级别就是解决当事务并发(SQL语句并发)时的问题。**



视图：一种虚拟存在的表  （相当于封装了一段Select的SQL语句作为视图）
视图是一张虚拟表，而存储过程相当于封装了函数（一组预先编译好的SQL语句的集合，理解成批处理语句）



## 7、系统变量和会话变量

### 7.1、系统变量

说明：变量由系统提供的，不是用户定义，属于服务器层面

常用的系统变量：

| 序号 | 系统变量name           | value    | 备注                                           |
| ---- | ---------------------- | -------- | ---------------------------------------------- |
| 1    | character_set_database | utf8     | 数据库的字符集编码格式                         |
| 2    | character_Set_server   | utf8     | mysql服务端字符集编码格式                      |
| 3    | log_bin                | ON       |                                                |
| 4    | storage_engine         | InnoDB   | 存储引擎使用的是InnoDB                         |
| 5    | slow_query_log         | ON       | 慢查询日志开关                                 |
| 6    | long_query_time        | 2.00     | 设置慢SQL的时间(执行超过这个时间就算是慢SQL了) |
| 6    | slow_query_log_files   | slow.log | 记录日志的文件名                               |
| 8    | log_slow_queries       |          |                                                |

使用的语法：
**1、查看所有的系统变量**
show global | session variables;

**2、查看满足条件的部分系统变量**
show global | [session] variables like '%char%';    //不声明是什么类型的变量的话，默认是session的

![image-20201213210827070](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201213210828.png)

**比如安装了mysql后，就要先修改一下数据库的字符集编码格式和mysql服务端的编码格式：**

- character_set_database：数据库的编码格式，若不修改的话，那么查询库里面字段的值如果是中文可能会乱码
- character_set_server：mysql服务端的编码格式，若不修改，可能看到某些中文会出现乱码呢。



**有两种方式可以对mysql的系统变量进行修改：**

- 直接通过命令的方式，在Mysql中修改系统变量

```bash
#但是通过命令行修改的话，下次mysql重启一下，就又按照mysql的配置文件恢复到默认值了，只有改配置文件才能保证重启也生效
set global character_set_database = 'utf8';

#开启慢SQL查询
set global slow_query_log=1
```

- 通过修改mysql的配置文件my.cnf的方式来修改系统变量，来保证重启mysql时，系统变量也不会失效



3、查看指定的某个系统变量的值
select @@global | [session].系统变量名;  //例：查看数据库的隔离级别   select @@tx_isolation;

**4、为某个系统变量赋值**
方式一：
set global | [session] 系统变量名 = 值;



## 8、查看global status

```bash
#1、查看mysql有多少条慢SQL
show global status like '%slow_queries%'
```



## 9、my.cnf相关配置

```bash
#1、data目录
datadir = /wxqyh/component/mysql/data
```





## 10、初级篇补充

0、一般有很多数据库（分库），

//使用USE wxqyh可以进入某个库中  。

//使用该命令查看当前数据库的字符集 SHOW VARIABLES LIKE ‘%char%’  。 

//在当前数据库 select database()查看当前数据库名称  

//show index from stuinfo; //查看表的索引

//desc stuinfo; //查看表结构(比方查看表的字段约束)

**//show databases; //查看详细的数据库**    SHOW TABLES WHERE tables_in_**wxqyh_test** LIKE "%tb_qy_%" //查询表进行模糊查询 黑体表示数据库名

**//show tables; //查看当前数据库的详细表**

//explain table; 查看表的索引情况

//SHOW VARIABLES LIKE 'time_zone' //查看当前时区 默认是东八区



```txt
1、 +  ：mysql中的加号只能做数值运算符  如果是 '33'+3，那么会将字符串转化成数值型，最终是36. 如果是'john' +3 ，那么是3，因为转化不成功就是0
2、如果要连接两个字符串的话，可以使用CONCAT(str1,str2,...)函数，一般进行分组后，分组后，一个组里面只会显示第一条数据，如果是想要把隐藏起来的数据连接的话，可以使用GROUP_CONCAT()函数进行连接
3、CONCAT(null,id)：拼接完得null 如果拼接的字段为null 那么拼接完的结果就是null
4、IFNULL（字段，默认值） //如果该字段为null的话，那么就给它设置一个默认值 默认值的类型和字段的类型一致即可
5、where后面的筛选条件可以分三类：条件表达式、逻辑表达式、模糊查询(like in between )
条件表达式筛选：
    条件运算符：>  <  =  !=  <>  <=>  >=  <=

逻辑表达式筛选
    逻辑运算符：and  or  not    (Java中： &&   ||   ! )

模糊查询 
    like  // %张% 传进来时就要拼接成%%的形式了  sql语句可以直接把:keywork写上， 传入参数判断 keywork不为null 那么值拼接上%keywork%，如果为null 那么value为%%，则当作没有
    between 1 and 3  //闭区间范围 一般只能是数值类型的 当然也可以是 id>=1 and id <=3  哪种方式都行
    in("张三","李四")  //集合,当然自己喜欢也可以用OR， DQDP框架，占位符用 id in (:id)  参数传入list或者数组类型都行   当然，如果自己喜欢的话，也可以自己去拼接成String类型的
    is null 或者有 is not null //当字段为null时

//Java中为了避免使用SQL语句拼接影响可读性以及SQL注入，所以使用占位符
6、 where id = 1 和where id = "1" //识别到id是int类型，所以会将字符型的'1'转化成1
7、order by：order by salary asc,employee_id desc  //如果都升序 那么可以写在一起  order by salary,employee_id asc    如果不写升序或降序的话，默认是升序
8、函数的分类：1、单行函数    2、分组函数(又叫统计函数、聚合函数、组函数)
共同点：都是有一个返回值
不同点：1、单行函数是传入一个值   2、分组函数是传入许多个值
常见的单行函数：字符函数（参数传入一个字符类型  比如LENGTH('john') ）、数学函数、日期函数、其他函数、流程控制函数
```



2、聚合函数汇总 sum、avg、max、min、count(字段)//count(字段)的话是查询该组这么多行中该字段非空的个数

//使用了聚合函数，那么每个分组中最终只有一行



**3、distinct和group by的区别**，基本上distinct能实现的，group by都能实现

group by分组查询的作用：一般情况下，我们若不分组查询的话，那么查询完后，默认是1组，分组查询的话，可以针对某个字段去分组，那么该字段相同的划分到同一组中。分组完毕后，还可以使用having 对每个组进行二次筛选比方说：

group by last_name having count(*) >2  //意思就是根据姓氏去分组，然后对每个分组的总条数（行）还要大于2



4、count(*)个count(1)的区别

在INNODB存储引擎下，两者效率差不多， //作用是统计行数 



**5、使用到分组函数(聚合函数)一同查询的字段要求一定要是group by 后的字段，不然没意义**



```sql
6、
SELECT * FROM goods WHERE COUNT(*)>2  //错 where后面只能够过滤表中字段，不可以使用分组函数
SELECT * FROM goods GROUP BY category HAVING COUNT(*)>2  //对  条件使用分组函数只需要在分组查询中使用
```



**7、筛选条件的两种可能性**

```sql
SELECT * FROM goods GROUP BY num HAVING num >2  //  重要：能用分组前筛选的，就优先考虑使用分组前筛选
SELECT *FROM goods WHERE num>2 GROUP BY num  //关于对行增加筛选条件的话，上面的和下面的结果一样，因为可以先筛选后分组，也可以先分组后对每个分组中进行字段条件的筛选
```



**8、分组查询前筛选和分组后筛选**

分组查询中的筛选条件分为两类

|                | **数据源**     | **位置**           | **关键字** |
| -------------- | -------------- | ------------------ | ---------- |
| **分组前筛选** | 原始表         | group by子句的前面 | where      |
| **分组后筛选** | 分组后的结果集 | group by子句的后面 | having     |

//注：分组函数做条件肯定放在having子句中
   注：能用分组前筛选的，就优先考虑使用分组前筛选



9、分组查询使用group by 后面不一定非要字段，也可以是单行函数
比如：SELECT * FROM goods GROUP BY LENGTH(num)

10、分组查询，多个字段进行分组 （原则就是：分完组，在现有分组的基础上，继续分组）
#分组后再分组，也就是原来分3组，后面你再根据字段再进一步分组的话，后面的分组字段不一致的，再1分为n继续拆
SELECT * FROM goods GROUP BY category,num



11、union

应用场景：要查询的结果来自于多个表，且多个表没有直接的连接关系，但查询的信息一致时

特点：

- 要求多条的查询语句的查询列数是一致的！

- 要求多条查询语句的拆线呢的每一列的类型和顺序最好一致

- union关键字默认去重，如果使用union all可以包含重复项



**有父子关系的表的设计（文件夹、部门）**

```txt
1、概述
常规设计字段：文件夹ID，父文件夹ID，文件夹全路径ID（FULL_ID）、文件夹名、创建者
//比方课件归属在某个文件夹下，那么课件表需要有文件夹ID，以及全路径文件夹ID （全路径ID代表整棵树从根节点到叶子节点都有路可循）
设置文件夹全路径ID的好处，每次插入insert语句的时候，都去拼接全路径ID，那么到时候如果我们要查找某个文件夹下及其子子孙孙文件夹下有多少个课件（入参外层文件夹type_ID，然后去分类表找到其全路径FULL_ID(从而获得从根节点到当前节点的整个前缀了)，然后通过sql语句，full_id like :fullId% 模糊查询即可）

2、像部门表这种的话，不单单需要设置parent_id，还要设置full_name这一字段，不单单是为了展示，还为了其它业务的查询父父父部门或子子子部门的可扩展性，为什么不设置full_id这种字段呢，是因为一个id可能32或36位，假设一个id是32位，那么加上间隔符，总共就是33位了，那么100个层级的话就是33*100-1了，假设你使用varchar的话，直接data too long 

3、查找父父父部门 + 子子子部门
3.1、已有full_name
父父父部门：需要对入参full_name利用endWiths进行切割，切割成一个个父父父部门的全路径，到时候直接IN一下就找到父父父部门了。
子子子部门：向下查比较简单，直接Like一下即可 (fulle_name like "入参的full_name%" )

3.2、没有full_name，只有parent_id
可能要根据org_id先查找全部部门先，然后转成Map
已有条件：只有当前的部门id
父父父部门：写一个递归方法，通过当前部门id查找到其parent_id，进而才知道部门数据
子子子部门：写一个递归方法，去匹配parent_id = 入参部门id ，递归结束的条件要想好，判断没有往下一级了就回溯了

```



## 11、循环插入百万数据

```sql
-- 1.定义一个函数
DELIMITER $$
CREATE FUNCTION mock_data()
RETURNS INT
BEGIN
    DECLARE num INT DEFAULT 1000000;
    DECLARE i INT DEFAULT 0;
    WHILE i < num DO
	INSERT INTO tb_qy_alicloud_flow_info (id,media_id) VALUE (REPLACE(UUID(), "-", ""), "123"); 
	SET i = i + 1;
    END WHILE;
    RETURN i;	
END;

-- 2.执行函数
SELECT mock_data();

-- 注：若执行定义函数报 This function has none of DETERMINISTIC, NO SQL, or READS SQL DATA in its de 的错误
-- 则执行以下语句
set global log_bin_trust_function_creators=TRUE;
```







# 二、mysql高级篇

## 1、安装mysql

### 1.1、docker安装

**linux上安装mysql，供windows访问步骤**

```txt
1、先使用docker下载mysql镜像 ： docker pull mysql
2、将mysql镜像运行到容器里边 ：docker run -idt --name=localMysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root mysql
3、然后进入容器 ： docker exec -it localMysql /bin/bash 
4、到mysql容器中登录mysql：mysql -hlocalhost -uroot -proot
```

**mysql的配置文件**

- windows的配置文件：my.ini文件

- linux的配置文件：**/etc/my.cnf文件**

```txt
log-bin：主从复制
log-error：错误日志
log：慢SQL日志
frm文件：存放表结构
myd文件：存放表数据 （myisam）
myi文件：存放表索引
```



### 1.2、传统安装

#### 1.2.1、下载并解压

```shell
tar -xvf mysql-8.0.23-linux-glibc2.12-x86_64.tar.xz
解压完重命名一下目录，不然太长了
```



#### 1.2.2、创建用户组一级用户合密码并授权

```shell
groupadd mysql
useradd -g mysql mysql
```

```shell
授权用户 （如：下列配置my.cnf 时指定的目录都需给mysql 用户授权）
chown -R mysql.mysql /data/mysql/mysql-8.0.23
```



#### 1.2.3、初始化基础信息

```shell
在mysql目录下创建 data 数据存储目录
 mkdir data 
切换到bin 目录下
 cd bin
初始化基础信息 切记切记切记mysql8 一定要在初始化时设置 不区分大小写，不然后续修改和删除重装没区别
初始化后在原始my.con 下lower_case_table_names = 1 是无效的，所以一定要在初始化时加上 --lower-case-table-names=1
 
 ./mysqld --user=mysql --basedir=/data/mysql/mysql-8.0.23 --datadir=/data/mysql/mysql-8.0.23/data/ --initialize --lower-case-table-names=1
```

**敲完命令后，要把密码先记好**

![在这里插入图片描述](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210428132604.png)



#### 1.2.4、编辑my.cnf文件

如果启动初始化基本信息的后台进程服务后，没有my.cnf的话，自行去/etc/目录下新增my.cnf文件

```mysql
[mysqld]
# 安装目录
basedir = /data/mysql/mysql-8.0.23
# data目录
datadir = /data/mysql/mysql-8.0.23/data
# mysql错误日志，安装过程中有什么报错，可以看这个日志
log-error = /data/mysql/mysql-8.0.23/data/error.log
# 服务器编码
character-set-server = UTF8
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
#设置是否区分大小写（初始化后此参数在这里也必须存在）
lower_case_table_names=1
```



#### 1.2.5、添加mysqld服务到系统，授权以及添加服务

```txt
进入主目录
 cd /data/mysql/mysql-8.0.23
添加mysqld服务到系统
 cp -a ./support-files/mysql.server /etc/init.d/mysql
授权以及添加服务
 chmod +x /etc/init.d/mysql
 chkconfig --add mysql
```



#### 1.2.6、启动

```shell
service mysql start
```



#### 1.2.7、若data目录无权限的话

```shell
chown -R mysql:mysql /data/mysql/mysql-8.0.23/data
```



#### 1.2.8、建立服务同步连接

作用：因为我们每次要在linux连接mysql服务端都要用mysql脚本，但是这个脚本是放在安装目录的bin目录下，那我们就需要去这个目录下打命令才可以，如何像docker那样实现在任何目录敲打命令呢？答案就是把脚本放在 **/usr/bin** 目录下。

```shell
# 软链接，映射文件（类似挂载）
ln -s /data/mysql/mysql-8.0.23/bin/mysql /usr/bin
```



**可能出现如下内容**

![image-20210428134144172](https://cdn.jsdelivr.net/gh/sphshenpeihong/pic1/img/20210428134145.png)



**需要安装libtinfo.so.5依赖**

```shell
sudo dnf install ncurses.x86_64
```

**安装完毕后，再次建立同步连接**

```shell
sudo ln -s /usr/lib64/libtinfo.so.6.1 /usr/lib64/libtinfo.so.5
```



#### 1.2.9、连接服务器并修改密码

```shell
#进入mysql 控台
mysql mysql -uroot -p
#修改密码
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '你的密码';
#刷新权限
flush privileges;
```



#### 1.2.10、外网可能无法访问，配置外网连接授权

```shell
#选择mysql数据库
 use mysql;
 #修改root 用户的连接地址现在 localhost 为本机 也可指定固定ip 此处 % 开启所有ip访问
 update user set host='%' where user='root';
 #刷新权限
 flush privileges;
```



#### 1.2.11、检查数据库字符集

```shell
show global variables like '%character_set_data%';

#但是通过命令行修改的话，下次mysql重启一下，就又按照mysql的配置文件恢复到默认值了，只有改配置文件才能保证重启也生效
set global character_set_database = 'utf8';

```









## 2、mysql存储引擎

mysql存储引擎：MyISAM、InnoDB

![image-20201222175646328](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222175647.png)

```txt
1、连接层
       最上层是一些客户端和连接服务，包含本地sock通信和大多数基于客户端/服务端工具实现的类似于tcp/ip的通信。主要完成一些类似于连接处理、授权认证、及相关的安全方案。在该层上引入了线程池的概念，为通过认证安全接入的客户端提供线程。同样在该层上可以实现基于SSL的安全链接。服务器也会为安全接入的每个客户端验证它所具有的操作权限。

2、服务层
       第二层架构主要完成大多少的核心服务功能，如SQL接口，并完成缓存的查询，SQL的分析和优化及部分内置函数的执行。所有跨存储引擎的功能也在这一层实现，如过程、函数等。在该层，服务器会解析查询并创建相应的内部解析树，并对其完成相应的优化如确定擦汗寻表的顺序，是否利用索引等，最后生成相应的执行操作。如果是select语句，服务器还会查询内部的缓存。如果缓存空间足够大，这样在解决大量读操作的环境中额能够很好的提升系统的性能。

3、引擎层
       存储引擎层，存储引擎真正的负责了mysql中数据的存储和提取，服务器通过API与存储引擎进行通信，不通的存储引擎具有的功能不通，这样我们可以根据自己的实际需要进行选取。

4、存储层
       数据存储层，主要是将数据存储在运行于裸设备的文件系统之上，并完成与存储引擎的交互
```

![image-20201222175722508](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222175723.png)

**InnoDB是行锁，操作时只锁某一行，不对其它行有影响**

多个事务操作同一行数据时，后来的事务处于阻塞等待状态，这样可以避免了脏读等数据一致性的问题。后来的事务可以操作其他行数据，解决了表锁高并发性能低的问题



## 3、分析SQL性能

Q：为什么会出现 性能下降SQL慢、执行时间长、等待时间长

- 查询语句写得烂

- 索引失效

- 关联查询太多join（设计缺陷或不得已的需求）

- 服务器调优各个参数设置（缓冲、线程数等）



## 4、详解索引

```txt
定义：索引（Index）是帮助Mysql高效获取数据的数据结构（索引是数据结构）//简单理解成：排好序的快速查找数据结构
说人话：给一个表的某个字段新建索引，其实就是新增加一颗B+树，排好序的树，到时候我们通过这个字段去查询的时候，匹配到索引的话，那么会去B+数查找，查找速度就会变得很快
分类：单值索引、复合索引、唯一索引
索引的存储：一般来说索引本身也很大，不可能全部存储在内存中，因此索引往往以索引文件的形式存储在磁盘上(存储在myi文件中)
//我们平常所说的索引，如果没有特别指定，都是指B树（多路搜索数，并不一定要二叉的）结构组织的索引。其中聚集索引，符合索引，前缀索引，唯一索引默认都是B+树索引，统称索引。当然，除了B+数这种类型的索引之外，还有hash索引index等。
```

### 4.1、索引的基础语法

#### 4.1.1、创建索引

```sql
-- 创建表的索引的两种语法
1、
CREATE [unique] index indexName ON mytable(columnname(length)) 例：create index idx_user_nameEmail on user(name,email)
2、
ALTER mytable ADD [unique] index [indexName] ON (columnname(length)) 例：alter user add index ids_user_nameEmail on (name,email)
//四种案例添加索引
ALTER TABLE table_name ADD PRIMARY KEY (column_list)：该语句添加一个主键，这意味着索引值必须是唯一的，且不能为NULL。
ALTER TABLE table_name ADD UNIQUE index_name(column_list)：这条语句创建索引的值必须是唯一的（除了NULL外，NULL可能会出现多次） //身份证号
ALTER TABLE table_name ADD INDEX index_name(column_list)：添加普通索引，索引值可出现多次。
ALTER TABLE table_name ADD FULLTEXT index_name(column_list)：该语句指定了索引为FULLTEXT，用于全文索引。
```

#### 4.1.2、删除索引

```sql
DROP index [indexName] ON mytable 例：drop index idx_nameEmail on user
```

#### 4.1.3、查看索引

```sql
SHOW index FROM table_name
```

![image-20201222175747684](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222175748.png)

**详解show index from table查看表索引的每一列代表什么意思**

```txt
1、Table：表的名称
2、Noin_unique：如果索引不能包括重复词，则为0.如果可以，则为1
3、Key_name：索引的名称
4、Seq_in_index：索引中的列序号，从1开始
5、Column_name：列名称
6、Collation：列以什么方式存储在索引中。在Mysql中，有值'A'(升序) 或NULL(无分类)。
7、Cardinality：索引中唯一值的数目的估计值。
8、Sub_part：如果列只是被部分地编入索引，则为被编入索引的字符的数目。如果整列被编入索引，则为NULL。
9、Packed：指示关键字如果被压缩，如果没有被压缩，则为NULL
10、Null：如果列含有NULL，则含有YES。如果没有，则该列含有NO
11、Index_type：用过的索引方法（BTREE、FULLTEXT、HASH、RTREE）
12、Comment：多种评注
```



### 4.2、索引结构(BTree)

![image-20201222175813723](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222175815.png)

**【初始化介绍】**

一颗b+树，浅蓝色的块我们称之为一个磁盘块，可以看到每个磁盘块包含几个数据项（深蓝色所示）和指针（黄色所示），如果磁盘块1包含数据项17和35，包含指针P1、P2、P3（当前磁盘块的指针指向下一个磁盘块），
P1表示小于17的磁盘块，P2表示在17和35之间的磁盘块，P3表示大于35的磁盘块。
真实的数据存在于叶子节点即3、5、9、10、13、15、28、29、36、60、75、79、90、99
非叶子节点则不存储真实的数据，只存储指引搜索方向的数据项，如17、35并不真实存在于数据表中。
//给一张表建立了索引(B树)，那么索引会存放在磁盘中，myi文件中，那么当一条select语句过来，如果where后面有索引命中的话，那么会先将磁盘中的磁盘块通过IO操作给加载到内存当中（B树中每一个节点都是一个磁盘块，然后是以链表的形式，通过指针去指向下一个磁盘块），所以深度不宜太深，因为每次往下一层，都会和硬盘发生一次IO操作。



**Q：哪些情况下需要创建索引？ 索引=检索+排序**
**1、主键自动建立唯一索引 （通过主键查找速度非常快）**
**2、频繁作为查询条件的字段应该创建索引（比方通过银行卡号查询）**
**3、查询中与其它表关联的字段，外键关系建立索引**
**4、频繁更新的字段不适合创建索引（因为每次更新不单单是更新了记录，还会更新索引） //像一些经常要执行+1 -1操作的字段，都不可以去建立索引，因为该值更新后，也会去更新索引树**
**5、Where条件里用不到的字段不创建索引（浪费硬盘空间）**
6、单键/**组合**索引的选择，who?（在高并发下倾向创建组合索引）
**7、查询中排序的字段，排序字段若通过索引去访问将大大提高排序速度（比方说按创建时间字段排序）**
**8、查询中统计或分组字段 （因为分组也需要去先排序，才能按某个字段进行分组**



**Q：哪些情况下不需要创建索引？**

1、表记录太少
2、经常增删改的表（因为提高了查询速度，同时却会降低更新表的速度，如对表进行INSERT、UPDATE和DELETE。因为更新表时，Mysql不仅要保存数据，还要保存一下索引文件）
3、数据重复且分布平均的表字段，因此应该只为最经常查询和最经常排序的数据列建立索引。（注意，如果某个数据列包含许多重复的内容，为它建立索引就没有太大的实际效果）。
//索引的选择性事指索引列中不同值的数目与表中记录数的比。如果一个表中有2000条记录，表索引列有1980个不同的值，那么这个索引的选择性就是1980/2000=0.99，一个索引的选择性越接近于1，这个索引的效率就越高。



**Q：哪些情况下索引无法命中？**

1、使用like：虽然该字段建立了索引，但是你是使用like的话，那么索引无法命中。
2、使用SQL函数：虽然该字段建立了使用，但是使用函数对字段进行操作的话，索引也是无法命中的。
3、使用or**(如果or里面所有字段都是建立了索引，那么索引才可以命中)**：当or条件中有未建立索引的列才失效，or的字段全建索引才会命中索引
4、类型不一致：如果列是字符串类型，传入条件是必须用引号引起来的，传入int类型的话，虽然会进行类型强转，但是也会导致索引失效
5、普通索引的不等于不会走索引：//特别的：如果是主键，则还是会走索引。   如果是主键或索引是证书类型，则还是会走索引
6、索引条件为索引，则select字段必须页是索引字段，否则无法命中



**Q：查看加锁情况**

```sql
show open tables where in_use > 0
```



**Q：sql语句使用order by是怎么个排法，以及如何提高order by的速度（但order by 的列不是索引列的话）？**

```txt
我们查询SQL时，是从磁盘读取数据到内存当中，从磁盘读取查询需要的所有列，按照order by列在buffer对它们进行排序，然后扫描排序后的列表进行输出，它的速度更快一些，避免了第二次读取数据。并且把随机IO变成了顺序IO，但是它会使用更多的空间，因为它把每一行都保存在内存中了。
因为order by 影响了效率的话，可以尝试更改mysql的sort_buffer_size参数，调大缓冲区

总结：
1、使用Order by 就一定不要使用select *
2、可以适当尝试提高sort_buffer_size的值
3、尝试提高max_length_for_sort_Data的值
//order by和where一样，也会按照最左原则去命中索引。
```



**Q：group by关键字优化**

```txt
1、group by实质是先排序后进行分组，也是遵照索引建的最佳左前缀原则
2、同样，如果Group by后面不是索引列的话，看看能否适当调大max_lengthfor_sort_data、sort_buffer_size参数的值
3、能用where的话，就尽量不要去用having了
```





## 5、性能分析

**Mysql Query Optimizer**

```txt
第二层服务层中的Mysql Query Optimizer（查询优化器）：
1、Mysql中有专门负责优化SELECT语句的优化器模块，主要功能：通过计算分析系统中收集到的统计信息，为客户端请求的Queury提供他认为最优的执行计划（他认为最优的数据检索方式，但不见得是DBA认为是最优的，这部分最耗费时间）
2、当客户端向Mysql请求一条Query，命令解析器模块完成请求分类，区别出是SELECT并转发给Mysql Query Optimizer时，Mysql Query Optimizer首先会对整条Query进行优化，处理掉一些常量表达式的预算，直接换算成常量值。并对Query中的查询条件进行简化和转换，如去掉一些无用或显而易见的条件、结构调整等。然后分析Query中的Hint信息（如果有），看显示Hint信息是否可以完全确定该Query的执行计划。如果没有Hint或Hint信息还不足以完全确定执行计划，则会读取所涉及对象的统计信息，根据Query进行写相应的计算分析，然后再得出最后的执行计划。
```

**Mysql常见瓶颈**

```txt
CPU：CPU在饱和的时候一般发生在数据装入内存或从磁盘上读取数据时候
IO：磁盘I/O瓶颈发生再装入数据远大于内存容量的时候
服务器硬件的性能瓶颈：top、free、iostat和vmstat来查看系统的性能状态
```

### 5.1、explain

使用explain关键字可以模拟优化器执行SQL查询语句，从而知道Mysql是如何处理你的SQL语句的。分析你的查询语句或是表结构的性能瓶颈

![image-20201222175840650](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222175841.png)

**Q：explain能干嘛?**

A：可以知道以下的信息

- 表的读取顺序（看Id，id大的先执行，相等的按顺序执行）

- 数据读取操作的操作类型（看select_type）

- 哪些索引可以使用

- 哪些索引被实际使用

- 表之间的引用

- 每张表有多少行被优化器查询



**详解解释explain 查询SQL语句后，得出的每一列代表什么意思**

1、id：表的执行顺序，id大的表先执行
2、select_type：SIMPLE(简单查询)、PRIMARY（最外层查询）、SUBQUERY（子查询）、DERIVED（子查询衍生表）、UNION、UNION RESULT
3、table：当前加载的行是加载哪张表
4、partitions：
**5、type**（显示查询使用了何种类型）：从最好到最坏 **system > const > eq_ref > ref > range > index > ALL**

- system：表只有一行记录(等于系统表)，这是const类型的特列，平时不会出现，这个也可以忽略不计（相当于一张表里面只有一条数据）

- const：表示通过索引一次就找到了，const用于比较primary key或者unique索引。因为只匹配一行数据，所以很快，如将主键置于where列表中，Mysql就能将改查询转换成一个常量（主键查询）

- eq_ref：唯一性索引扫描，对于每个索引键，表中只有一条记录与之匹配。常见于主键或唯一索引扫描（主键查询）

- ref：唯一性索引扫描，返回匹配某个单独值的所有行，本质上也是一种索引访问，它返回所有匹配某个单独值的行，然而，它可能会找到多个符合条件的行，所以他应该属于查找和扫描的混合体（相当于通过org_id查询）

- range：只检索给定范围的行，使用一个索引来选择行。key列显示使用了哪个索引，一般就是在你的where语句中出现了between、<、>、in等的查询，这种范围扫描索引扫描比全表扫描要好。（相当于用IN）

- index：这通过比ALL快，因为索引文件通常比数据文件小。（也就是说虽然all和Index都是读全表，但index是从索引中读取的，而all是从硬盘中读的）

- ALL：将遍历全表以找到匹配的行

6、possible_keys：显示可能应用在这张表中的索引，一个或多个。查询涉及到的字段上若存在索引，则该索引将被列出，但不一定被查询实际使用
7、key_len：标识索引中使用的字节数，  //对于不同条件，但查出同样的查询结果，精度越少越好
8、ref：显示索引的哪一列被使用
9、rows：根据表统计信息及索引选用情况，大致估算出找到所需的记录**所需读取的行数**（越小越好）
10、extra：额外信息



### 5.2、索引优化

**索引单表优化**
单表查询，通过加索引，然后通过explain分析select语句 （要避免type=all的情况）

**索引多表优化**
左连接，则建索引在右表的连接字段，因为左连接是左边所有表的数据都要有的，所以会全表扫描，而就需要给右边建立索引，加快搜索了。 

**索引三表优化**
左连接时，左边表不建索引，右边的表建索引 （小表驱动大表，也就是左边的表数据小，则不加索引也没什么影响）

//小表驱动大表，一般左连接的话，小表的写在左边，右表的条件可以加索引



**【优化总结口诀】**

全值匹配我最爱，最左前缀要遵守；

带头大哥不能死，中间兄弟不能断；

索引列上少计算，范围之后全失效；

LIKE百分写最右，覆盖索引不写星；



### 5.3、索引失效如何避免

1、全值匹配我最爱
2、最佳左前缀法则  //如果索引了多列，要遵守最左前缀法则，指的是查询从索引的最左前列开始并且**不跳过索引中的列  （从左边开始匹配，匹配上几个，那几个的索引有效，后面匹配不上直接无效了。）**
3、不在索引列上做任何操作**（计算、函数、(自动or手动)类型转换），**会导致索引失效而转向全表扫描  //加了索引的字段不要去使用函数或者过多的修改
4、存储引擎不能使用索引中范围条件右边的列   //加了索引的字段最好使用 "=" 如果使用> <的话，那么排在后面的索引就全部会失效
5、尽量使用覆盖索引（只访问索引的查询(索引列和查询列一致)），减少 select *
6、mysql在使用不等于（!=或者 <> ）的时候无法使用索引会导致全表扫描
7、is null , is not null 也无法使用索引
8、like以通配符开头（'%abc...'）mysql索引失效会变成全表扫描的操作  //问题：解决like '%字符串%'时索引不被使用的方法   //非要使用两个%%的话，也可以采用覆盖索引的方式，使得索引生效，就是select的东西必须是主键或索引字段，掺杂别的不是索引的字段的话，也会导致索引失效的。
9、字符串不加单引号索引失效
10、少用or，用它来连接时会索引失效
11、小总结

![image-20201222175909681](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222175910.png)

//最后一条写错了，是a、b、c都可以用



## 6、锁理论

概述：锁是计算机协调多个进程或线程并发访问某一资源的机制

​		在数据库中，除传统的计算资源（如CPU、RAM、I/O等）的争用以外，数据也是一种供许多用户共享的资源。如何保证数据并发访问的一致性、有效性是所有数据库必须解决的一个问题，锁冲突也是影响数据库并发访问性能的一个重要因素。从这个角度来说，锁对数据库而言显得尤其重要，也更加复杂。

**MyISAM的读/写锁（共享锁/排它锁）**

- 读锁（共享锁）：针对同一份数据，多个读操作可以同时进行而不会互相影响

- 写锁（排它锁）：**当前写操作没有完成前，它会阻断其他写和读。**

//结论：读锁会阻塞写，但是不会堵塞读。而写锁则会把读和写都堵塞。





**InnoDB存储引擎（行锁）**

读某一行的时候，上读锁，不同客户端都可以随便读
session1去update某一行时，对某一行上写锁，其它客户端可以读，session1读到的自己是更改后了的，但是session2读的数据是不准确的(脏读)，并且session2不可以update，一update的话就会阻塞状态，需要等session1释放先。

**//索引失效可能导致行锁变表锁**



## 7、主从复制

**主从复制**

概述：slave会从master读取binlog来进行数据同步
三步骤+原理图

![image-20201222175927362](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222175929.png)



## 8、linux安装Mysql，起不来

原因：是因为mysql默认得安装在/usr/local/mysql/目录下，如果不是安装在这个目录，得改mysqld文件

1、mysql.server文件 cp 到/etc/init.d/目录下，并且重命名成mysqld

2、修改Mysqld文件里面的basedir和basedirbin。由默认改成我们自己安装的目录





## 9、mysql安装目录结构

8.1、bin目录



8.2、data目录



8.3、include目录



8.4、lib目录



8.5、share目录



8.6、my.cnf文件



## 10、mysqldump(数据导入导出)

### 10.1、导出指定数据库

```shell
mysqldump -u root -p security_center > security_center.sql

# 如上面的命令显示什么COLUME_NAME的话，则用下面的命令
# 因为新版的mysqldump默认启用了一个新标志，通过 --column-statistics=0来禁用它

./mysqldump --column-statistics=0 -u root -p security_center > /data2/shenpeihong/security_center.sql

```



### 10.2、导入指定数据库

```shell
# 先连接mysql服务端，然后创建数据库

# use 进入指定数据库

# 使用source命令开始导入数据

```







# 三、Mysql应急抢救篇



## 1、mysql服务器---CPU高的相关处理机制

**1、分析CPU可能高的原因**

- 可能是大量耗时长的SQL线程正在执行。



### 1.1、紧急的话，直接杀SQL

```mysql
#查看mysql服务器中正在运行的所有线程情况  不加full则只列出前100条，加了full列出全部
show full processlist;

#查出符合条件的SQL线程，并拼接杀死SQL语句
select concat('kill ',id,';') from information_schema.processlist where info<>'NULL' and time>50;
```

![image-20201213191048342](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201213191050.png)

- **id**：SQL线程的唯一标识id（执行kill语句的时候很有用）
- user：显示当前用户，若非root，就代表当前所连接的人不是用root账号登陆，这个命令就只显示你权限范围内的sql语句
- **host**：显示这个语句从哪个ip的哪个端口发上发出的。可以用来追踪处问题语句的用户（一般是某台内网服务器的地址）
- **db**：显示这个进程目前连接的是哪个数据库。
- command：显示当前连接的执行的命令，一般是休眠（sleep）、查询（query）、连接（Connect）
- time：此状态持续的时间，单位是秒
- state：显示使用当前连接的SQL语句的状态  *
- **info**：显示这个SQL，长度有限，一般显示出来的SQL就显示不全，但是这是一个判断问题SQL语句的重要依据



### 1.2、后期优化Mysql服务器

1、慢查询的开启并捕获

2、explain + 慢SQL分析抓到的慢SQL

3、show profile查询SQL在Mysql服务器里面的执行细节和生命周期情况

4、SQL数据库服务器的参数调优



## 2、mysqldumpslow

日志分析工具命令：mysqldumpslow ，可以结合各种属性组合查询想要的结果，可以排序或其它。



## 3、show profiles;

**1.打印出执行的每条SQL以及对应的时间**

```sql
show profiles;
```

![image-20201215211156818](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201215211158.png)



**2.查询指定某条SQL的执行全过程（包括CPU和IO的使用情况）**

```sql
show profile cpu,block io for query 42;
```

![image-20201215212835866](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201215212837.png)

//第一列的Status最好不要出现以下四种情况：

- converting HEAP to MyISAM
- Creating tmp table
- Copying to tmp table on disk
- locked



## 4、常用的sql语句

```bash
#1、查看当前表哪些被锁住了
show open tables;



```







