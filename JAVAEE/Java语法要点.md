# 一、Java语法要点

## 1、JavaSE

### 1.1、JDK常用基础

#### 1.1.1、Java中各个类型所占字节

只有8种基本类型可以算字节数，其他引用类型都是由java虚拟机决定的

**数值型：**

- byte：1字节
- short：2字节
- int：4字节
- long 8字节

**浮点型：**

- float：4字节 //单精度浮点型 有小数点的
- double：8字节 //双精度浮点型 有小数点的    0.0 这个数字默认的double

**字符型：**

- char：2字节

**布尔型：**

- boolean：1字节



#### 1.1.2、Java修饰符

常用的修饰符有：private、default、protected、public、static、abstract、final

**普通修饰符：public、protected、默认(default)、private**

- public：该类与非该类均可访问（整个项目都可以访问）
- protected：同一个包中的类+继承该类的子类可以访问
- 默认(default可不写)：同包(比如同一个service的
- package中 ，不是一整个模块包)中的类private：只有当前类可以访问

//以上普通修饰符只能修饰成员变量，不能用来修饰局部变量



**特殊修饰符：static、final、abstract**

//方法如果是static声明，那么属于静态方法，需要使用类名调用。若非static声明，那么使用实例方法，需要使用实例对象调用



#### **1.1.3、foreach循环（增强for循环）**

```	java
    /**
     * 增强for循环
     */
    @Test
    public void test(){
        List<String> list = new ArrayList<String>();
        list.add("123");
        list.add("456");
        for(String str : list){  //从下表为0开始 遍历到整个列表结束
            System.out.println(str);
        }
    }
```



#### 1.1.4、**final**关键字

- final类不能被继承，没有子类，final类中的方法默认是final的。
- final方法不能被子类覆盖(也即是final方法不能被重写),但final方法可以被继承。
- final成员变量表示常量，只能被赋值一次，赋值后值不再改变 (也即是指向一个内存区域A后，不会再指向别的内存区域，就是死死地指向A，A的内容更改了也无所谓)
- final不能用于修饰构造方法。



#### 1.1.5、interface接口

- 类是单继承的，接口是多继承的

- 接口不可以去实现接口，但接口可以继承接口(使用extends关键字)

- 所有接口的成员变量默认都是public static final

- 所有接口的方法默认都是public abstract

- 抽象类实现接口，无论实不实现接口里的方法都可以。

- 具体的类实现接口的话，就必须要实现接口中所有的方法。

  

#### 1.1.6、String类

特点：String的字符串创建出来的时候会在内存种开辟空间，内存种字符串不能重复，所以String类型遇到字符串相同时不用重新开辟内存，直接指向即可

1. **public String[] split(String regex);** //根据给定正则表达式的匹配拆分此字符串

``` java
    /**
     * String类的方法：public String[] split(String regex)
     */
    @Test
    public void test(){
        String citys = "上海|北京|广州|深圳";
        String[] strArray = citys.split("\\|");
        for (String str : strArray) {
            System.out.println(str);
            /*
                上海
                北京
                广州
                深圳
             */
        }
    }
```

2. public String[] concat(String str); //将制定字符串连接到此字符串的结尾  //注：concat()方法只能接字符串，而+号可以接其它类型(实际会转换成字符串类型)

``` java
    /**
     * String类的方法：public String concat(String str)
     */
    @Test
    public void test(){
        String str  = "hello,";
        String concat = str.concat("world!");
        System.out.println(concat);
        /*
            hello,world!
         */
    }
```

3. public String trim(); //可以去除字符串前面或后面的所有空格

``` java
    /**
     * String类的方法：public String trim()
     */
    @Test
    public void test(){
        String str = " hello,world! ";
        String trim = str.trim();
        System.out.println(trim);
        /*
            hello,world!
         */
    }
```

4. public byte[] getBytes(String charsetName)； //将String编码成字节数组  arg1：编码格式

``` java
    /**
     * String类的方法：public byte[] getBytes(String charsetName)
     */
    @Test
    public void test() throws UnsupportedEncodingException {
        String str = "hello,world!";
        byte[] bytes = str.getBytes("utf-8");
        System.out.println(bytes);
        /*
            [B@6b57696f
         */
    }
```

5. public int indexOf(String str); //返回指定字符串第一次匹配的字符串的下标

``` java
    /**
     * String类的方法：public int indexOf(String str)
     */
    @Test
    public void test() {
        String str = "hello,world";
        int index = str.indexOf("wor");//下标从0开始计算
        System.out.println(index);
        /*
            6
         */
    }
```

6. public int lastIndexOf(String str); //返回指定字符串最后一次出现的索引

``` java
    /**
     * String类的方法：public int lastIndexOf(String str) + public String substring(int beginIndex)
     */
    @Test
    public void test() {
        String str = "images.png";
        int lastIndex = str.lastIndexOf(".png");
        //当下标不为-1代表已匹配上字符串
        if (-1 != lastIndex){
            //已获得后缀起始索引，则通过subString(beginIndex)或subString(beginIndex, endIndex)方法可以截取字符串
            String suffix = str.substring(lastIndex);
            System.out.println(suffix);
            /*
                .png
             */
        }else{
            System.out.println("匹配不上");
        }
    }
```

7. public static String format(String format, Object... args); //输出字符串，第一个参数可以%s或其它格式，后面的参数传入该值

``` java
    /**
     * String类的方法：public static String format(String format, Object... args)
     */
    @Test
    public void test() {
        String username = "zhangsan";
        String password = "123456";
        String format = String.format("账号：%s , 密码：%s", username, password);
        System.out.println(format);
        /*
            账号：zhangsan , 密码：123456
         */
    }
```

8. public boolean startsWith(String prefix); //判断字符串开头是否和参数的字符串相同  一般判断开头是否可以匹配上

```java
    /**
     * String类的方法：public boolean startsWith(String prefix)
     */
    @Test
    public void test() {
        String name = "张大地";
        boolean flag = name.startsWith("张");
        if (flag){
            System.out.println("开头已匹配上");
        }else{
            System.out.println("开头匹配不上");
        }
    }
```

9. public boolean endsWith(String suffix); //判断后缀是否匹配上

``` java
    /**
     * String类的方法：public boolean endsWith(String suffix)
     */
    @Test
    public void test() {
        //一般判断文件类型可以使用String类的 endsWith(suffix str)方法和lastIndexOf(suffix str)方法
        String name = "image.png";
        boolean flag = name.endsWith("png");
        if (flag){
            System.out.println("后缀已匹配上");
        }else{
            System.out.println("匹配不上");
        }
    }
```

10. public String toLowerCase(); //将所有字符串转换成小写的

``` java
    /**
     * String类的方法：public String toLowerCase()
     */
    @Test
    public void test() {
        String str = "Hello";
        String lowerCase = str.toLowerCase();
        System.out.println(lowerCase);
        /*
            hello
         */
    }
```

11. public String toUpperCase(); //将所有字符串转换成大写的

``` java
    /**
     * String类的方法：public String toUpperCase()
     */
    @Test
    public void test() {
        String str = "Hello";
        String UpperCase = str.toUpperCase();
        System.out.println(UpperCase);
        /*
            HELLO
         */
    }
```

12. public static String valueOf(long l); //将各种基本类型转成String类型，同理 使用Integer等基本类型的包装类也可以将String类型转成基本类型的包装类类型 比如：Integer.valueOf(10); //Integer转String

```java
    /**
     * String类的方法：public static String valueOf(long l)
     */
    @Test
    public void test() {
        //String的静态方法valueOf支持将各种数值类型转换成String类型
        String value = String.valueOf(10L);
        System.out.println(value);
        /*
            10
         */
    }
```



#### 1.1.7、StringUtils类

1. public static boolean isEmpty(CharSequence cs); //当字符串为null或length=0时，返回true

``` java
    /**
     * StringUtils类的方法：public static boolean isEmpty(CharSequence cs)
     */
    @Test
    public void test() {
        String str = "test";
        if (StringUtils.isEmpty(str)){
            System.out.println("该字符串为null或为空串");
        }
    }
```

2. public static boolean isNotEmpty(CharSequence cs); //当前字符串既不为null，也不为空串

``` java
    /**
     * StringUtils类的方法：public static boolean isNotEmpty(CharSequence cs)
     */
    @Test
    public void test() {
        String str = "test";
        if (StringUtils.isNotEmpty(str)){
            System.out.println("当前字符串既不为null，也不为空串");
        }
    }
```

3. public static boolean isBlank(CharSequence cs); //当前字符串为null 或 长度为0(空串) 或 存在全为空白符 

``` java
    /**
     * StringUtils类的方法：public static boolean isBlank(CharSequence cs)
     */
    @Test
    public void test() {
        String str = "test";
        if (StringUtils.isBlank(str)){
            System.out.println("当前字符串为null 或 长度为0(空串) 或 全为空白符 ");
        }
    }
```

4. public static String trim(String str); //去掉前后两端的空格，用法和String的trim()差不多

``` java
    /**
     * StringUtils类的方法：public static String trim(String str)
     */
    @Test
    public void test() {
        String str = " test ";
        String trim = StringUtils.trim(str);//去掉前后两端的空格，用法和String的trim()差不多
        System.out.println(trim);
        /*
            test
         */
    }
```

5. public static boolean equalsIgnoreCase(CharSequence str1, CharSequence str2); //判断是否相等，无论大小写

```java
    /**
     * StringUtils类的方法：public static boolean equalsIgnoreCase(CharSequence str1, CharSequence str2)
     */
    @Test
    public void test() {
        String str1 = "test";
        String str2 = "Test";
        if (StringUtils.equalsIgnoreCase(str1, str2)) {
            System.out.println("已匹配上");//判断是否相等，无论大小写
        } else {
            System.out.println("未匹配上");
        }
    }
```

6. public static boolean isAnyEmpty(CharSequence... css); //存在至少一个字符串为null 或 字符串长度为0

```java
    /**
     * StringUtils类的方法：public static boolean isAnyEmpty(CharSequence... css)
     */
    @Test
    public void test() {
        String str1 = "test";
        String str2 = null;
        if (StringUtils.isAnyEmpty(str1, str2)) {
            System.out.println("存在至少一个字符串为null 或 字符串长度为0");
        } else {
            System.out.println("所有字符串都不为null 并且 字符串长度都大于0");
        }
    }
```



#### 1.1.8、System类

特点：提供对应的一些系统(当前虚拟机)属性信息和系统操作 下面介绍System类的静态方法 

1. public static native long currentTimeMillis(); //获得当前时间与1970年1月1号00：00的差值(ms) 称为时间戳

```java
    /**
     * System类的方法：public static native long currentTimeMillis()
     */
    @Test
    public void test() {
        long current = System.currentTimeMillis();
        System.out.println(current);//获得当前时间与1970年1月1号00：00的差值(ms) 称为时间戳
        /*
            1603176213798
         */
    }
```

2. public static void exit(int status); //结束正在运行的Java程序

``` java
    /**
     * System类的方法：public static void exit(int status)
     */
    @Test
    public void test() throws InterruptedException {
        //开启线程 子线程睡眠1秒，子线程手动结束当前正在运行的Java程序，不让主线程执行了
        new Thread(() -> {
            System.out.println("子线程打印");
            System.exit(0);//提前结束该方法的执行
        }).start(); //使用代理模式，记得开启线程
        Thread.sleep(1000);
        System.out.println("主线程打印");
        /*
            子线程打印
         */
    }
```

3. public static String getProperty(String key)； //获取系统相关的一些值

```java
    /**
     * 获取当前项目所在的目录路径
     */
    @Test
    public void test20() {
        String projectPath = System.getProperty("user.dir");
        System.out.println(projectPath);
    }
```





#### 1.1.9、URLEncoder

1. public static String encode(String s, String enc); //URL如果含有中文或特殊符号( : / ?)的话，需要使用编码方法加密转码

```java
    /**
     * URLEncoder类的方法：public static String encode(String s, String enc)
     */
    @Test
    public void test() throws UnsupportedEncodingException {
        String html = "https://www.baidu.com/error.html?msg=大哥你牛";
        String encode = URLEncoder.encode(html, "UTF-8");
        System.out.println(encode);//URL如果含有中文或特殊符号( : / ?)的话，需要使用编码方法加密转码
        /*
            https%3A%2F%2Fwww.baidu.com%2Ferror.html%3Fmsg%3D%E5%A4%A7%E5%93%A5%E4%BD%A0%E7%89%9B
         */
    }
```



#### 1.1.10、URLDecoder

1. public static String decode(String s, String enc)； //URL解密

```java
    /**
     * URLDecoder类的方法：public static String decode(String s, String enc)
     */
    @Test
    public void test() throws UnsupportedEncodingException {
        String encode = "https%3A%2F%2Fwww.baidu.com%2Ferror.html%3Fmsg%3D%E5%A4%A7%E5%93%A5%E4%BD%A0%E7%89%9B";
        String decode = URLDecoder.decode(encode, "UTF-8");//URL加解密所用字符编码格式需要使用同一个
        System.out.println(decode);
        /*
            https://www.baidu.com/error.html?msg=大哥你牛
         */
    }
```



#### 1.1.11、Java代码执行顺序

探究：静态块、构造块、静态方法、构造函数的执行顺序

![image-20201222193454474](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222193455.png)

//运行父类的main方法，那么首先static的会先执行，父类的static块就会被先执行，然后执行main方法，main方法调用到子类的构造方法，那么会先去执行子类的static块，因为子类的构造方法第一行会有隐式的super()，所以会去执行父类的构造方法，既然执行父类的构造方法，那么会先去执行构造快，父类执行完后才去执行子类的构造方法，同样执行构造方法前也会去先执行子类的构造块。



#### 1.1.12、Properties类

特点：Properties类继承自Hashtable类并且实现了Map接口，使用键值对保存属性集。（Key和Value都是字符串类型的）

1. 使用Properties类去接收.properties配置文件的值

```java
    /**
     * Properties类
     */
    @Test
    public void test() throws IOException {
        Properties properties = new Properties();
        //获取指向classes目录
        InputStream is = this.getClass().getClassLoader().getResourceAsStream("jdbc/jdbc.properties");
        properties.load(is);//指向输入流地址
        String url = properties.getProperty("url");
        System.out.println(url);
        /*
            urlTest
         */
    }
```



#### 1.1.13、BigDecimal 类

1. public BigDecimal add(BigDecimal augend); //加法

```java
    /**
     * BigDecimal类的方法：public BigDecimal add(BigDecimal augend)
     */
    @Test
    public void test() {
        //加法计算
        BigDecimal num = new BigDecimal("1.124");
        BigDecimal num1 = new BigDecimal("1.3644222");
        BigDecimal add = num.add(num1);
        System.out.println(add);
        /*
            2.4884222
         */
    }
```

2. public BigDecimal subtract(BigDecimal subtrahend); //减法

```java
    /**
     * BigDecimal类的方法：public BigDecimal subtract(BigDecimal subtrahend)
     */
    @Test
    public void test() {
        //减法计算
        BigDecimal num = new BigDecimal("1.124");
        BigDecimal num1 = new BigDecimal("1.3644222");
        BigDecimal subtract = num.subtract(num1);
        System.out.println(subtract);
        /*
            -0.2404222
         */
    }
```

3. public BigDecimal multiply(BigDecimal multiplicand); //乘法

```java
    /**
     * BigDecimal类的方法：public BigDecimal multiply(BigDecimal multiplicand)
     */
    @Test
    public void test() {
        BigDecimal num = new BigDecimal("1.234");
        BigDecimal num1 = new BigDecimal("2");
        BigDecimal multiply = num.multiply(num1);
        System.out.println(multiply);
        /*
            2.468
         */
    }
```

4. public BigDecimal divide(BigDecimal divisor, int scale, int roundingMode); //除法

```java
    /**
     * BigDecimal类的方法：public BigDecimal divide(BigDecimal divisor, int scale, int roundingMode)
     */
    @Test
    public void test() {
        BigDecimal num = new BigDecimal("1.234");
        BigDecimal num1 = new BigDecimal("2");
        //arg1：除数
        //arg2：保留几位小数
        //arg3：使用的方式
        //  BigDecimal.ROUND_HALF_UP：四舍五入
        //  BigDecimal.ROUND_UP：向上取整
        //  BigDecimal.ROUND_DOWN：向下取整
        BigDecimal divide = num.divide(num1, 1, BigDecimal.ROUND_HALF_UP);//取一位小数，四舍五入
        System.out.println(divide);
        /*
            0.6
         */
    }
```



#### 1.1.14、List、Set、Queue、Map

**一、结构特点**

1.List和Set是存储单列数据的集合(祖先接口是Collection)，Map是存储键值对这样的双列数据的集合；

2.List中存储的数据是有顺序的，并且值允许重复；

   Set中存储的数据是无序的，并且值是不允许重复的，但元素在集合中的位置是由元素的hashcode决定的，即位置是固定的(位置固定是相对内部而言，对于用户来说set中的元素还是无序的)

   Map中存储的数据是无序的，它的键是不允许重复的，但是值是允许重复的；



**二、实现类**

**1.List接口有三个实现类(ArrayList、LinkList、Vector)**

​	ArrayList是基于数组实现，非线程安全，效率高，增删慢，查找快

**Q：ArrayList和LinkList的区别?**

A：前者用数组存储（查找快）、后者用链表存储（增删快）



**2.Set接口有两个实现类HashSet不允许集合中有重复的值**



**3.Map接口有四个实现类(HashMap、HashTable、LinkedHashMap、TreeMap)**

​	HashMap是基于hash表的Map接口实现，非线程安全，高效，支持null值和null键

**Q：HashMap与HashTable的区别？**

A：前者线程不安全，后者线程安全

Q：**HashMap与LinkedHashMap的区别？**

A：前者是无序的，后者是有序的(若想要按顺序存储的话，使用后者)

//**ConcurrentHashMap（多线程执行中专用Map）**



**三、三大集合(List、Set、Queue)和Map的常用的方法。**

Collection元素 只是遍历查询每个元素的话用foreach 如果是在遍历过程中想删除的话可以用迭代器(生成一个迭代器是拷贝引用，指向一一对应的)和普通for循环

**1.关于List的一些常用的方法**

```java
List<String> list = new ArrayList<String>(); //初始化容量为10的list
```

```java
List<String> list = new ArrayList<String>(20); //构造方法传int类型的话，是指定容量大小
```

```java
list.add("123"); //向集合中追加元素
```

```java
list.add(1,"b"); //向集合的指定位置中追加元素
```

```java
list.addAll(list1); //向集合追加一个列表
```

```java
list.addAll(4,list1); //向集合的指定位置中追加列表
```

```java
list.size(); //输出列表的长度大小
```

```java
list.get(0); //根据元素下标来取集合中的元素
```

```java
list.remove(7); //根据集合中元素下标位置来删除元素
```

```java
list.isEmpty(); //列表是否为空
```

```java
list.get(1).isEmpty(); //列表中的某个值是否为空（前提是需要泛型是String才可以这样）
```

```java
list.clear(); //清除列表的所有值
```

```java
list.contains(object o); //列表是否包含该值
```

```java
list.containsAll(Collection<T>); //list若包含实参，则为true
```

```java
list.iterator(); //返回值是Iterator <T> 类型   作用是我们可以来遍历list列表 因为Iterator接口里面有封装了next()、hasNext()方法、remove()方法
	boolean hasNext()：//判断容器内是否还有可供访问的元素
    Object next() ：//返回迭代器刚越过的元素的引用，返回值是Object,需要强制成自己想要的类型  
    void remove()：//删除迭代器刚越过的元素    
```

```java
list.sort((v1,v2)->v1-v2); //升序排序 ArrayList子类提供的sort方法
```

迭代器小demo：

```java
    /**
     * Iterator类：Iterator<E> iterator();
     */
    @Test
    public void test() {
        List<String> list = Lists.newArrayList();
        list.add("111");
        list.add("222");
        Iterator<String> iterator = list.iterator();
        while (iterator.hasNext()) {
            if ("111".equals(iterator.next())) {
                iterator.remove();
            }
        }
        System.out.println(list);
        /*
            [222]
         */
        //使用了迭代器，那么就相当于使用的是引用地址，当迭代器元素改变时，同时也会改变原list
    }
```

**小练习:给一个String[]数组，然后数组有许多组字符串，要求删掉某个字符串，不给索引的哦**

方法1：可能要删除的这个值，该数组有许多个，所以可以先定义一个变量count记录要删的个数，到时候新建一个数组就是.length-count，然后遍历旧数组，判断符合规则的就加到新数组(需要定义一个临时变量index呢)。

**方法2：先把数组使用Arrays.asList(strArray)转变成list集合，然后直接使用list.contains("值"),查到了不能直接list.remove();因为我们前面是使用Arrays.asList()这个方法，它不是使用ArrayList这个类去实例化的，所以没有remove方法，所以我们得再定义一个使用new ArrayList<String>(list)实例化出对象，然后再remove**

方法3：使用StringBuffer或者StringBuilder创建一个对象，然后判断数组满足规则的，就用append追加（最后是String类型的呢）



**2.关于set的一些常用的方法**

set方法基本和list的方法差不多。//Set没有get方法,因为是无序放入的，想取里面的值，需要使用iterator()或者是toArray()取操作数组



**3.Queue的常用方法**

一般要求不高的情况下，甚至是多线程中的生产者-消费者模式的情况下，推荐使用ArrayBlockingQueue（基于数组的阻塞队列）

```java
package com.sph.practice.test.sebase.listAndQueueAndSet;

import org.junit.Test;

import java.util.concurrent.ArrayBlockingQueue;
import java.util.concurrent.BlockingQueue;
import java.util.concurrent.atomic.AtomicInteger;

/**
 * Created by Shen Peihong on 2021/1/22
 * Description: 线程中的生产者-消费者模式
 *
 * @since 1.0.0
 */
public class ArrayBlockingQueueTest {

    //put方法放值，take方法取值
    private static BlockingQueue<Integer> queue = new ArrayBlockingQueue(1);

    // 多线程的计数变量类型需要使用原子类，原子类的底层机制思想是CAS（CompareAndSwap）
    // 而自增的具体操作使用的是自旋锁
    private static AtomicInteger integer = new AtomicInteger();

    //生产者
    class ProductThread extends Thread {
        @Override
        public void run() {
            while (true) {
                try {
                    // 添加一个元素 如果队列满，则阻塞
                    queue.put(integer.incrementAndGet());
                    Thread.sleep(1000);
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }
    }

    //消费者1
    class ConsumeThread1 extends Thread {
        @Override
        public void run() {
            while (true) {
                try {
                    System.out.println("阻塞1？");
                    // 移除并返回队列头部的元素 如果队列为空，则阻塞
                    Integer val = queue.take();
                    System.out.println("消费者1进行取值：" + val);
                    Thread.sleep(5000);
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }
    }

    //消费者2
    class ConsumeThread2 extends Thread {
        @Override
        public void run() {
            while (true) {
                try {
                    System.out.println("阻塞2？");
                    // 移除并返回队列头部的元素 如果队列为空，则阻塞
                    Integer val = queue.take();
                    System.out.println("消费者2进行取值：" + val);
                    Thread.sleep(5000);
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }
    }

    /**
     *
     */
    @Test
    public void test() throws InterruptedException {
        //开启生产者-消费者线程
        new ProductThread().start();
        new ConsumeThread1().start();
        new ConsumeThread2().start();
        Thread.sleep(1000000);
    }
    
}


```





**4.关于map的一些常用的方法**

```java
Map<String,String> map = new HashMap<String,String>();
```

```java
map.put("李白","123"); //在map中添加一对key和value的键值对数据
```

```java
map.get("李白"); //通过键来获取值
```

```java
map.size(); 获取map的长度
```

```java
Boolean isFlag = map.containsKey("李白"); //查找的key是否在map中
```

```java
map.containsValue("123"); //查找的value是否在map中
```

```java
map.hashCode(); //获取对应的哈希值
```

```java
map.keySet(); //取map的键给Set集合，由Set集合去输出值
```

```java
map.entrySet(); //取map的映射关系 到时候遍历的时候 可以使用getKey getvalue()逐个获取
```

```java
map.values(); //取出map中所有的值 用Collection类型或者其他集合类型去接收
```

```java
map.putAll(map1); //给map集合赋值一个集合
```

```java
map.remove("李白"); //移除一对键值对
```

```java
map.computeIfAbsent(key,k->new HashMap<>()); //jdk提供的取map中某个key的值，获取完不需要判断是否为null，该方法获取的key为null时，自动new一个map出来，然后把前面的key作为第一条数据
```

```java
String username = map.getOrDefault("username","defaultValue")；  //获得该key的值，若无则给默认值
```

**Q：关于computeIfAbsent(key, k -> "123") 和 getOrDefault(key, "123")的区别?**

- computeIfAbsent()这个API是左边的key获取为null时（使用map的key获取到值为null可能因为本身没有这个key，也可能该key对应的value本来就是null），才会去给该key对应的值赋上右边的值 //这个API不单单可以获取key所对应的值，但key不存在时，还可以将这个新的键值对添加到map当中 

- getOrDefault(); 这个API是当获得map中某个key，本身map中没有这个key时，才会去给这个新key赋上初始值 //这个API只是获取值，并不会给原来的Map增添上新的键值对



**Map初始化常量值使用双括号**

//此种方式是匿名内部类的声明方式

//第一个括号代表创建HashMap的匿名内部类

//第二个括号代表书写匿名内部类的{} 构造块，这样的话

```java
    /**
     * Map初始化常量值使用双括号
     */
    @Test
    public void test() {
        Map<String, Object> map = new HashMap<String, Object>() {
            {
                put("username", "zhangsan");
                put("password", "123456");
            }
        };
        for (Map.Entry<String, Object> entry : map.entrySet()) {
            System.out.println("当前的key为：" + entry.getKey() + "| 所对应的value为：" + entry.getValue());
        }
        /*
            当前的key为：password| 所对应的value为：123456
            当前的key为：username| 所对应的value为：zhangsan
         */
    }
```



#### 1.1.15、Java方法的return

一般方法中return语句最后执行， 如果void类型中有出现return; 则相当于循环中break;一样 跳出方法结束方法。

若方法有返回值，则执行完return value;也会结束方法

//综上，方法遇到return时，标志一个方法执行结束，不会往下再走。



#### 1.1.16、反射

Class getAll = User.class;  //通过反射获取到.class对象 其实就等同于可以获取这个类里面的任何的东西了

class class = JDBCUtils.class //通过反射获得.class对象 (一个类在被加载的过程当中会在JVM中获得该类的Class对象)

Class对象不能通过new去创建 Class对象只能通过JVM去创建



以下是**获取**Class对象(是到JVM获取 而不是创建，创建是JVM中创建的)的方式：Class对象由JVM虚拟机创建，只能从JVM获取

![image-20201222173851765](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222173853.png)



#### 1.1.17、PO、VO、DTO对象

//注：需要标明实现序列化接口，必要时还需要实现克隆接口

- PO：持久化对象。与数据库字段映射
- VO：业务对象。业务逻辑所需要的对象，往往是许多张表的字段组成
- DTO：远程调用接收对象



#### 1.1.18、Date(日期)

概述：实例化出来是一个日期对象，代表某个时间

1. public boolean after(Date when); //若当调用此方法的Date对象在指定日期之后返回true,否则返回false

```java
    /**
     * Date类的方法：public boolean after(Date when) 
     */
    @Test
    public void test() {
        Date now = new Date();
        long currentTimeMillis = System.currentTimeMillis();
        long aMinuteAgo = currentTimeMillis - (60 * 1000);//一分钟前的时间戳
        //当前时间是否在 "一分钟前" 之后？ 答案是：true
        boolean after = now.after(new Date(aMinuteAgo));//若当调用此方法的Date对象在指定日期之后返回true,否则返回false
        System.out.println(after);
        /*
            true
         */
    }
```

2. public boolean before(Date when); //若当调用此方法的Date对象在指定日期之前返回true,否则返回false

```java
    /**
     * Date类的方法：public boolean before(Date when)
     */
    @Test
    public void test() {
        Date now = new Date();
        long currentTimeMillis = System.currentTimeMillis();
        long aMinuteAgo = currentTimeMillis - (60 * 1000);//一分钟前的时间戳
        //当前时间是否在 "一分钟前" 前？ 答案是：false
        boolean before = now.before(new Date(aMinuteAgo));//若当调用此方法的Date对象在指定日期之后返回true,否则返回false
        System.out.println(before);
        /*
            false
         */
    }
```

3. public int compareTo(Date anotherDate); //比较当调用此方法的Date对象和指定日期 两者相等时候返回0 调用对象在指定日期之前则返回负数 调用对象在指定日期之后则返回正数

```java
    /**
     * Date类的方法：public int compareTo(Date anotherDate)
     */
    @Test
    public void test() {
        Date now = new Date();
        long currentTimeMillis = System.currentTimeMillis();
        long aMinuteAgo = currentTimeMillis - (60 * 1000);//一分钟前的时间戳
        //比较当调用此方法的Date对象和指定日期两者相等时候返回0 调用对象在指定日期之前则返回负数 调用对象在指定日期之后则返回正数
        int compare = now.compareTo(new Date(aMinuteAgo));
        System.out.println(compare);
        /*
            1
         */
    }
```



#### 1.1.19、Calendar(日历)

概述：实例化出来是一个日历对象，代表某一个日期 //Calendar类提供了需要放啊，可以知道该日期对象星期几，当月第几天，当年的第几天等各种详细信息

1. public final Date getTime(); //获得当前日期

```java
    /**
     * Calendar类的方法：public final Date getTime()
     */
    @Test
    public void test() {
        Calendar instance = Calendar.getInstance();
        Date now = instance.getTime();
        System.out.println(now);
        /*
            Tue Oct 20 22:47:33 CST 2020
         */
    }
```



#### 1.1.20、SimpleDateFormat

1. public final String format(Date date); //使用SimpleDateFormat格式化日期

```java
    /**
     * SimpleDateFormat类的方法：public final String format(Date date)
     */
    @Test
    public void test() {
        Date now = new Date();
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        String formatTime = sdf.format(now);
        System.out.println(formatTime);
        /*
            2020-10-20 22:50:21
         */
    }
```



#### 1.1.21、枚举类

1. 枚举类 

定义：使用enum定义的枚举类默认继承了java.lang.Enum，而不是继承Object类，枚举类可以实现一个或多个接口

特点：枚举类的所有实例都必须放在第一行展示，不需使用new关键字，不需显式调用构造器，自动添加public static final修饰

注意：

- 使用enum定义、非抽象的枚举类默认使用final修饰，不可以被继承。

- 枚举类的构造器只能是私有的。



2. 外部使用枚举类的方式：

- 外部想调用枚举类的枚举常量对象的话，直接枚举类名.枚举常量对象名(枚举类的构造方法名)

- 外部想通过调用枚举类的构造方法来调用里面的变量的话(每个方法名字不同) 枚举类名.构造方法名.get变量名



3. 常用的用途：

- 利用枚举类去定义DAO层的SQL语句

- 用于抛异常的code + desc



4. 枚举父类Enum自带方法

- values()：获取所有枚举类对象



#### 1.1.22、异常类

1. **异常类的关系**

Throwable：包含 Error + Exception



2. **Java面向对象之异常的基础概念**

2.1 throw和throws的区别：

位置不同：

- throws用在函数上，后面跟的是异常类，可以跟多个

- throw用在函数内，后面跟的是异常对象。

功能不同：

- throws用来声明异常，让调用者知道该功能又可能出现的问题，并由调用者给出预先的处理方式。

- throw抛出具体问题的对象。语句执行到throw共嗯那个就结束了，跳转到调用者。并将具体的问题对象抛给调用者

//注：throw语句独立存在，下面不要定义其他语句，因为也执行不到throw下面的语句，因为和return一样，不会再往下走了。



#### 1.1.23、HttpUrlConnection

```java
private static String getResult(String urlStr, String content, String encoding) {
		URL url = null;
		HttpURLConnection connection = null;
		try {
			url = new URL(urlStr);
			connection = (HttpURLConnection) url.openConnection();// 新建连接实例
			connection.setConnectTimeout(2000);// 设置连接超时时间，单位毫秒
			connection.setReadTimeout(2000);// 设置读取数据超时时间，单位毫秒
			connection.setDoOutput(true);// 是否打开输出流 true|false
			connection.setDoInput(true);// 是否打开输入流true|false
			connection.setRequestMethod("POST");// 提交方法POST|GET
			connection.setUseCaches(false);// 是否缓存true|false
			connection.connect();// 打开连接端口
			DataOutputStream out = new DataOutputStream(
					connection.getOutputStream());// 打开输出流往对端服务器写数据
			out.writeBytes(content);// 写数据,也就是提交你的表单 name=xxx&pwd=xxx
			out.flush();// 刷新
			out.close();// 关闭输出流
			BufferedReader reader = new BufferedReader(new InputStreamReader(
					connection.getInputStream(), encoding));// 往对端写完数据对端服务器返回数据
			// ,以BufferedReader流来读取
			StringBuffer buffer = new StringBuffer();
			String line = "";
			while ((line = reader.readLine()) != null) {
				buffer.append(line);
			}
			reader.close();
			return buffer.toString();
		} catch (IOException e) {
			e.printStackTrace();
		} finally {
			if (connection != null) {
				connection.disconnect();// 关闭连接
			}
		}
		return null;
	}
```



**HttpClinet**---比HttpUrlConnection(jdk自带)的类效率高一点点



#### 1.1.24、Arrays类

```java
List<String> list =  Arrays.asList(String类型的数组) //将String[]类型数组转化为 List 类型
//由于返回值是List类型，Set和List新建的时候调用构造方法的形参是Collection类型， 所以Arrays.asList(数组)的返回值可以作为构造方法的实参      sort() : 对数组进行排序
```



#### 1.1.25、MultipartFile文件上传

该类提供的API：

```java
    /**
     * 使用MultipartFile文件上传工具类的接口
     * @param file
     */
	@RequestMapping("/test1.do")
    public void test1(MultipartFile file) {
        FileOutputStream fos = null;
        try {
            //获得文件大小，单位是字节 ：  7126
            System.out.println("file.getSize()" + "：" + file.getSize());
            //获得文件原名(带后缀) ：  hrmanagement.html
            System.out.println("file.getOriginalFilename()" + "：" + file.getOriginalFilename());
            //获得文件的字节数组
            System.out.println("file.getBytes()" + "：" + file.getBytes());
            //获得请求头类型 ： text/html
            System.out.println("file.getContentType()" + "：" + file.getContentType());
            //获得文件输入流
            System.out.println("file.getInputStream()" + "：" + file.getInputStream());
            //将该文件写入磁盘的话，指向的磁盘路径一定要指定某个未存在的文件，这样到时候才会新建一个新文件呢
            file.transferTo(new File("D:\\temp\\IO流练习demo\\" + file.getOriginalFilename()));
            
            //文件上传的第二种方式，获取字节数组，然后自己通过IO流自己输出
            fos = new FileOutputStream("D:\\temp\\IO流练习demo\\copy" + file.getOriginalFilename());
            fos.write(file.getBytes());
            fos.flush();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                if (fos != null) fos.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```



#### 1.1.26、抽象类

好处简介：抽象类里面可以定义抽象方法，让继承该抽象类的子类去实现，比接口好的优点是抽象类里面可以定义成员变量或者封装一些方法，然后也可以去调用自己定义的抽象方法(最终是会去调实现类实现的方法)。

优点：

- 可以直接在抽象类的构造器里面写一些必执行代码，因为子类使用构造器实例化时，第一行都是会去执行super();
- 可以定义成员变量和封装方法，供子类直接调用（一般子类不重写）
- 当前抽象类定义的构造器或方法都可以去直接调用其定义的抽象方法（到时候是直接调实现类重写的方法）
- Springmvc的@RequestMapping可以穿透，访问的路径子类没有的话，也会去访问父类的路径
- 抽象父类里面直接this.getClass()，是返回调用当前方法的子类Class对象呢。

> 父类如何去判断子类的class对象或某个变量呢？

抽象父类可以定义一两个成员变量：Class类型和String类型的funcCode，然后定义两个set这两个值的方法，其子类一定要在重写中去set进去。



#### 1.1.27、NumberUtils

1、public static boolean isNumber(String str)

```java
    /**
     * NumberUtils.isNumber()
     * 判断传入的String类型的参数是否是一个数字来的
     */
    @Test
    public void test1(){
        String str = "23123213.12";
        System.out.println(NumberUtils.isNumber(str));
        /*
            true
         */
    }
```



2、public static boolean isDigits(String str)

```java
    /**
     * NumberUtils.isDigits()
     * 判断传入的String类型的参数是否每个字符都为数字
     */
    @Test
    public void test2(){
        String str = "23123213.12";
        System.out.println(NumberUtils.isDigits(str));
        /*
            false
         */
    }
```



#### 1.1.28、equals()和contains()

```java
// Object类写了一个equals方法(比较的是两个对象的地址值)，有一些类重写了equals方法，改变了定义相等的方式，比方Long、Integer、Double、Float都是用拆箱后的值去对比，并不是对比地址值。 而String是一个一个字符去比较，对比全部吻合才认为是true。

//1、List的contains()方法底层还是用equals去对比的，类比equals的判定机制
//2、Set的contains()方法底层是用map的containsKey() map底层判断去重也是使用了equals的。contains()底层也是用equals。 所以关键还是利用equals方法的
```





### 1.2、JDK8新特性

#### 1.2.1、快速理解泛型

**快速理解泛型(接口和类都可以声明泛型)的两种额外写法<？ extends T>和<? super T>**

在Java泛型中存在通配符的概念

- <? **extends** T>：上界通配符 即泛型最高只能是T

- <? super T>：下界通配符 即泛型最低只能是T



**泛型方法**

```java
    /**
     * 定义一个泛型方法，入参必须是Class<T> 返回值是T的话，必须使用<T>进行声明
     * @param c 入参Class对象
     * @param <T> 声明一个泛型
     * @return
     */
    private <T extends User> T reflectMethod(Class<T> c){
        try {
            T t = c.newInstance();
            return t;
        } catch (Exception e) {
            System.out.println("进行实例化失败");
        }
        return null;
    }
```

//作用：方法参数不指定某个类型，可以传任何类型的反射对象，出参是反射对象的实例化对象。 （**返回泛型的话，该泛型方法必须使用<T>声明**）



#### 1.2.2、函数式接口

定义：有且只有一个抽象方法的接口，称之为函数式接口

特点：**@FunctionalInterface** //接口使用该注解的话，那么该接口只能有且只有一个抽象方法

优点：延迟加载

使用场景：一般可以作为**方法的形参**和**返回值类型**

优化结果：当我们调用的方法中，参数有函数式接口的话，可以不用匿名内部类，而是使用lamda表达式的形式 (凡是可以用到匿名内部类的地方都可以使用lamda表达式替换掉)

投机取巧：我们需要实现接口的抽象方法，那么需要看该方法的头部，具体是什么参数，要返回什么值的类型，如果需要返回值的话，那么我们在编写lambda表达式的时候 得return值呢 （一行代码或者是方法引用的形式的话，那么return可以省略）

原来是：

```java
new Runnable(){
            @Override
            public void run() {

            }
        }
```

使用lamda表达式现在可以简写成：

() -> { 方法体 } //省略了new 接口，

- 因为整个是方法的参数，所以可以省略不写new 接口。
- 因为接口里面定义了方法，所以该方法头部省略，我们直接把代码写到花括号中即可，花括号中就是方法体



**下面实战几个常见的函数式接口(jdk1.8提供的)**

- Runnable接口 ：线程

- Supplier<T>接口  ：生产类型补给接口

- Consumer<T>接口 ：消费类型接口

- Predicate<T>接口 ：过滤类型接口

- Function<T,R>接口 ：转换类型接口



**1、Runnable接口(线程接口)**

//书写一个函数式接口作为形参的方法(开启线程)

```java
    //提供一个开启线程的方法 函数式接口作形参
    public static void runnableTest(Runnable runnable){
        new Thread(runnable).start();
    }
```

//调用含有函数式接口作为形参的方法，不使用匿名内部类的形式，而是使用简写lambda表达式

```java
    //lambda表达式可以简写，省略许多元素 这里省略了方法体的大括号以及方法体中执行语句的分号(前提是方法体的执行语句只有1句时才可以省略分号)
    @Test
    public void executeTest3(){
        //() 括号是方法的括号，具体里面需不需要写参数，还要看定义的函数式接口里面的方法是否有形参
        runnableTest(()-> System.out.println("简写lambda表达式"));
        runnableTest(()->System.out.println("方法体只有一句代码，所以省略语句后面的分号"));
    }
```



**2、Supplier接口** (生产接口，一些工具类方法可以使用Supplier接口来实现) 

作用：用来获取一个泛型参数指定类型的对象数据

示例：返回String类型

```java
    //生产接口 提供String类型数据
    public static String supplierTest(Supplier<String> supplier){
        //调用接口里面的get方法，接口也是.class类型，实例对象可以调用里面的变量或方法
        //而是接口里面的方法的实现具体由调用方去处理
        return supplier.get();
    }

    //调用生产接口Supplier<T>
    @Test
    public void executeSupplier(){
        //由于函数式接口里面定义的方法有返回值，所以实现方法的时候也需要return
        //由于只有一行代码，所以花括号、分号、return 都可以省略不写
        String response = supplierTest(()->"OK"); //一般实现方法的方法体中不是输出语句的话，那么可以确定是省略了return的
        //这里调用完是有返回值类型的
        System.out.println(response);
    }
```



示例：返回Integer类型

```java
    //定义Supplier是Integer类型
    public Integer supplierInteger(Supplier<Integer> supplier){
        return supplier.get();
    }

    //调用含有Supplier<T>函数式接口的方法
    @Test
    public void executeSupplierInteger(){
        Integer min = 9;
        Integer max = 10;
        //返回两个数的最大值
        Integer maxNum = supplierInteger(() ->max > min ? max : min);//省略了{} ;  return
        System.out.println("两个数的最大值为："+maxNum);
    }
```



**3、Consumer接口**

Consumer<T>接口则正好与Supplier接口相反，它不是生产一个数据，而是消费(使用)一个数据

```java
    //调用接口中的方法 实现的话由调用该方法的地方去执行实现方法的代码
    public void consumerTest(String name , Consumer<String> consumer){  //这里一般是封装好了的方法，里面会去使用函数式接口对象去调用里面的抽象方法进行操作
        //调用接口里面的方法，返回值是void类型
        consumer.accept(name);
    }

    @Test
    public void executeConsumer(){
        //我们只是实现这个方法而已，书写方法体即可，到时候参数传什么过来，具体由创建该接口对象，然后去执行该方法去传递参数呢
        //只有一行代码的话，省略方法体{} 省略分号;  省略方法形参的括号()  省略方法形参的类型
        consumerTest("赵丽颖",name-> System.out.println(name));//打印传递的参数
        //我们主要是用了这个封装的方法，需要传递lambda表达式，书写实现类
    }
```



**4、Predicate<T>接口**

有时候我们需要对某种类型的数据进行判断，从而得到一个boolean值结果，这时可以使用Predicate<T>接口

```java
package com.sph.practice.test.jdk8.lambda;

import org.junit.Test;

import java.util.function.Predicate;

/**
 * Created by Shen Peihong on 2020/5/17 22:01
 * Description:Predicate<T> 有时候我们需要对某种类型的数据进行判断，从而得到一个boolean值结果，这时可以使用Predicate<T>接口
 * 一般就是return 条件(符合返回true 不符合返回false)
 * @since 1.0.0
 */
public class PredicateTest {

    public static void predicateTest(Predicate<String> predicate){
        boolean test = predicate.test("helloworld");
        System.out.println("字符串的长度是否大于5呢？答案是："+test);
    }

    @Test
    public void executePredicate(){
        predicateTest( s->s.length() > 5); //接口中方法的实现，前面s的方法的形参，判断s的长度是否大于5
    }

}
```



**5、Function<T,R>接口**

Function<T,R>接口用来根据一个类型的数据得到另一个类型的数据，由T类型转换成R类型 **（我们可以获取参数的值，然后书写实现类，返回一个我们想要的值）**

利用提供了抽象方法：R apply(T t);

```java
    //Function<T,R>接口，T类型转换成R类型，实现类需要我们自己写
    public static void applyTest(String t,Function<String,Integer> function){
        Integer apply = function.apply(t);
        if (apply instanceof Integer) System.out.println("我是Integer类型");
        else System.out.println("我是其它类型");
    }

    @Test
    public void executeApply(){
        applyTest("111",(String s)->{
            return Integer.parseInt(s);
        });
    }
```



#### 1.2.3、lambda表达式

**下面是一些stream流的常用方法**

1. Stream<T> filter(Predicate<? super T> predicate);//过滤留下满足条件的元素

```java
    //stream().filter()方法
    public static void main(String[] args) {
        List<String> list = new ArrayList<String>();
        list.add("张三");
        list.add("张4");
        list.add("张5");
        list.add("张6");
        list.add("李9");
        //过滤出list中姓张的元素，不姓张则去除 使用Stream<T>接口提供的filter方法实现即可 filter方法形参是(Predicate<T>接口)
        //filter(Predicate<T> predicate)方法，循环遍历集合流中的数据，形参是Predicate<T>接口，实现接口里面的test方法
        //把我们想要进行判断的代码写在实现方法test中，到时候符合条件的话会返回true
        //而filter方法的实现类，恰好就是遍历集合流时，判断形参为true的话，才会留下当前元素。false的话，就会remove掉当前元素
        System.out.println("过滤前："+list);
        //注：由于方法的参数是函数式接口，我们只需要知道里面抽象方法的用途，然后去根据抽象方法的返回值，参数进行编写代码
        List<String> collect = list.stream().filter((String s) -> {
            return s.startsWith("张");
        }).collect(Collectors.toList());
        //调用Stream<T>接口里面的方法去对集合流进行操作后，最后需要调用collect(Collectors.toList())去将stream流转化成List
        System.out.println("过滤后："+collect);
    }
```



2. void forEach(Consumer<? super T> action); //可以对遍历到的当前元素进行消费(操作) 

```java
    //stream().foreach()方法
    @Test
    public void foreachTest(){
        List<String> list = new ArrayList<String>();
        list.add("张三");
        list.add("张4");
        list.add("张5");
        list.add("张6");
        list.add("李9");
        list.stream();
        list.stream().forEach(s->System.out.println(s)); //foreach遍历执行操作当前元素

    }
```



3. <R> Stream<R> map(Function<? super T, ? extends R> mapper); //将A类型转换成B类型 

```java
    //stream().map()方法
    @Test
    public void mapTest(){
        List<String> list = new ArrayList<String>();
        list.add("1");
        list.add("2");
        list.add("3");
        list.add("4");
        list.add("5");
        //map形参是Apply类型，接口的作用就是映射(将A类型转换成B类型)
        Stream<Integer> stream = list.stream().map((String s) -> {
            return Integer.parseInt(s);
        });
        stream.forEach(i->System.out.println(i)); //forEach遍历
    }
```



4. Stream<T> peek(Consumer<? super T> action); //遍历，类似于foreach

```java
    /**
     * peek()区别于foreach：
     * 1、这个不是终结方法，可以再继续流式处理
     * 2、这个有返回值，返回处理后的值
     */
    @Test
    public void test1(){
        List<AccountPO> list = new ArrayList<AccountPO>(){
            {
                add(new AccountPO(1, "zhangsan", "zhangsan"));
            }
        };
        //使用peek修改其中的元素username
        System.out.println("打印处理前的list");
        list.forEach(System.out::println);
        /*
            AccountPO(id=1, username=zhangsan, password=zhangsan, num=null)
         */
        List<AccountPO> afterList = list.stream().peek(po -> po.setUsername("xiesi")).collect(Collectors.toList());
        System.out.println("打印list本身");
        /*
            AccountPO(id=1, username=xiesi, password=zhangsan, num=null)
         */
        list.forEach(System.out::println);
        System.out.println("打印peek后的返回值afterList");
        /*
            AccountPO(id=1, username=xiesi, password=zhangsan, num=null)
         */
        afterList.forEach(System.out::println);
    }
```



5. <R> Stream<R> flatMap(Function<? super T, ? extends Stream<? extends R>> mapper);

```java
    /**
     * flatMap()扁平化流式处理
     * 专门处理Stream<<List<泛型>> 或 Stream<String[]数组>类型的呢。
     * 处理思想：将大流中的小流全部转换成全体同级的大流，也即是Stream<List<String>>转换成Stream<String>的过程，数组也是类似。
     * 谨记：需要时刻关注流式处理方法的返回值呢。流式处理只针对结果，不针对之前的过程
     */
    @Test
    public void test21(){
        //封装多个Aclass类型多个List
        List<AClass> aClassList1 = new ArrayList<AClass>() {
            {
                add(new AClass(1,"yi","yi"));
                add(new AClass(2,"er","er"));
                add(new AClass(3,"san","san"));
            }
        };
        List<AClass> aClassList2 = new ArrayList<AClass>() {
            {
                add(new AClass(4,"si","si"));
                add(new AClass(5,"wu","wu"));
                add(new AClass(6,"liu","liu"));
            }
        };


        List<ListContainer> list = new ArrayList<ListContainer>() {
            {
                add(new ListContainer(aClassList1));
                add(new ListContainer(aClassList2));
            }
        };
        //当id=1的话，将用户名改成"shenpeihong"
        //引用类型的话，也会同步改变值
        List<AClass> collect = list.stream().map(k -> k.getLst()).flatMap(Collection::stream).peek(k -> {
            if (k.getId()==1){
                k.setUsername("shenpeihong");
            }
        }).collect(Collectors.toList());
        System.out.println(collect);
        /*
            [AClass(id=1, username=shenpeihong, password=yi),
             AClass(id=2, username=er, password=er),
              AClass(id=3, username=san, password=san),
               AClass(id=4, username=si, password=si),
                AClass(id=5, username=wu, password=wu),
                 AClass(id=6, username=liu, password=liu)]
         */
        System.out.println("======");
        System.out.println(aClassList1);
        /*
            [AClass(id=1, username=shenpeihong, password=yi),
             AClass(id=2, username=er, password=er),
              AClass(id=3, username=san, password=san)]
         */
    }
```



6. boolean allMatch(Predicate<? super T> predicate);

```java
    /**
     * allMatch 遍历的元素全部都匹配条件
     */
    @Test
    public void test1(){
        List<String> list = new ArrayList<String>() {
            {
                add("123");
                add("456");
            }
        };
        //遍历的数据是否长度都为3
        boolean allMatch = list.stream().allMatch(k -> k.length() == 3);
        System.out.println(allMatch);
        /*
            true
         */
    }
```



7. boolean anyMatch(Predicate<? super T> predicate);

```java
    /**
     * anyMatch 遍历的元素存在局部满足条件
     */
    @Test
    public void test2(){
        List<String> list = new ArrayList<String>() {
            {
                add("123");
                add("456666");
            }
        };
        //遍历的数据是否局部满足长度为3
        boolean anyMatch = list.stream().anyMatch(k -> k.length() == 3);
        System.out.println(anyMatch);
        /*
            true
         */
    }
```



8. boolean noneMatch(Predicate<? super T> predicate);

```java
    /**
     * noneMatch 遍历的元素全都不满足条件
     */
    @Test
    public void test3(){
        List<String> list = new ArrayList<String>() {
            {
                add("1234");
                add("456666");
            }
        };
        //遍历的数据是否局部满足长度为3
        boolean noneMatch = list.stream().noneMatch(k -> k.length() == 3);
        System.out.println(noneMatch);
        /*
            true
         */
    }
```



9. Optional<T> findFirst();

```java
    /**
     * findFirst 返回流式处理完后的第一个元素
     */
    @Test
    public void test4() {
        List<String> list = new ArrayList<String>() {
            {
                add("123");
                add("456");
            }
        };
        Optional<String> first = list.stream().filter(k -> k.length() == 3).findFirst();
        if (first.isPresent()) {
            System.out.println(first.get());
        }
    }
```



10. Optional<T> findAny();

```java
    /**
     * findAny 返回流式处理后的任意一个元素
     */
    @Test
    public void test5(){
        List<String> list = new ArrayList<String>() {
            {
                add("621");
                add("000");
                add("123");
                add("456");
                add("789");
            }
        };
        Optional<String> findAny = list.stream().filter(k -> k.length() == 3).findAny();
        if (findAny.isPresent()) {
            System.out.println(findAny.get());
        }
    }
```





#### 1.2.4、方法引用

**使用方法引用的前提：**对象存在，方法也存在 则可以使用双冒号来代替冗余的lambda表达式 (省略实现抽象方法的形参括号、准备调用的方法的括号())  //如果lambda要表达的函数方案已经存在于某个方法的实现中，那么则可以通过双冒号来引用该方法作为lambda的替代者

特点：

- 双冒号::为引用运算符，而它所在的表达式被称为**方法引用**。  （使用双冒号::的时机：我们要在lambda表达式的实现接口方法的方法体中调用方法。同时该方法的对象或类都已知）

- lambda中传递的参数 一定是方法引用中的那个方法可以接收的类型，否则会抛出异常 （因为使用了方法引用，省略了方法形参，以及方法的括号和参数，到时候要能方法形参和调用方法的参数能自动映射呢）



**对比lambda表达式和方法引用（方法引用是对lambda表达式的优化）**

**1、**

Lambda表达式写法：s->System.out.println(s);

方法引用写法：System.out::println



**2、**

//testPO po = new testPO();

**Lambda表达式写法：map( (po)->po.getUsername() )**

**方法引用写法：map(po::getUsername) //PO类是已经存在了的，而类中的get方法也是已经存在了的，故可以使用方法引用的方式取映射**

**注1：**使用方法引用的方式去使用构造方法去创建对象使用方法引用的方式去调用普通方法的4种方式。

- 使用对象po::方法  //指定的对象调用方法

- 使用this::方法 //使用当前类的对象去调用方法

- 使用super::方法 //使用父类对象的方法

- 使用Math::abs //使用静态类去调用方法

**注2：**使用方法引用的方式去使用构造方法去创建对象

使用类名::new的格式表示 //有时候我们的函数式接口的抽象方法需要返回某个类的对象的话，那么我们可以使用类名::new 去返回一个对象 （前提是构造方法存在，而且方法形参要和构造方法的参数对得上）



**3、类如果里面有静态方法的话，那么前面就不需要使用类的实例了**

方法引用写法：method( -10,**Math::abs** )



**4、方法引用也支持当前类的父类super去调用父类的方法**

方法引用写法：method( **super::sayHello** );



#### 1.2.5、Optional类的常规使用

常规使用

```java
    /**
     * Optional类的常规使用
     */
    @Test
    public void test() {
        String test = null;
        String str = Optional.ofNullable(test).orElse("123");
        System.out.println(str);
        /*
            123
         */
    }
```



#### 1.2.6、Collectors类方法详解

1. Collector<T, ?, List<T>> toList(); //转ArrayList类型

```java
    /**
     * Collectors类的方法：public static <T>
     *     Collector<T, ?, List<T>> toList()
     */
    @Test
    public void test() {
        User u1 = new User("zhangsan", "123");
        User u2 = new User("lisi", "456");
        List<User> userList = Lists.newArrayList(u1, u2);
        //累计名字属性到一个List中
        List<String> collect = userList.stream().map(User::getUsername).collect(Collectors.toList());
        System.out.println(collect);
        /*
            [zhangsan, lisi]
         */
    }
```

2. Collector<T, ?, Set<T>> toSet(); //转HashSet类型

```java
    /**
     * Collectors类的方法：public static <T>
     *     Collector<T, ?, Set<T>> toSet()
     */
    @Test
    public void test() {
        User u1 = new User("zhangsan", "123");
        User u2 = new User("lisi", "456");
        User u3 = new User("zhangsan", "666");
        List<User> userList = Lists.newArrayList(u1, u2, u3);
        //累计名字属性到一个Set中
        Set<String> collect = userList.stream().map(User::getUsername).collect(Collectors.toSet());
        System.out.println(collect);
        /*
            [zhangsan, lisi]
         */
    }
```

3. Collector<T, ?, C> toCollection(Supplier<C> collectionFactory); //转换成想要实现类的类型

```java
    /**
     * Collectors类的方法：public static <T, C extends Collection<T>>
     *     Collector<T, ?, C> toCollection(Supplier<C> collectionFactory)
     */
    @Test
    public void test() {
        User u1 = new User("zhangsan", "123");
        User u2 = new User("lisi", "456");
        User u3 = new User("zhangsan", "666");
        List<User> userList = Lists.newArrayList(u1, u2, u3);
        //累积名字属性到一个TreeSet中 想要其它实现类的类型也可以，左边的TreeSet可以向上转型成Set
        Set<String> collect = userList.stream().map(User::getUsername).collect(Collectors.toCollection(TreeSet::new));
        System.out.println(collect);
        /*
            [zhangsan, lisi]
         */
    }
```

4. public static Collector<CharSequence, ?, String> joining(CharSequence delimiter); //拼接字符串

```java
    /**
     * Collectors类的方法：public static Collector<CharSequence, ?, String> joining(CharSequence delimiter)
     */
    @Test
    public void test() {
        User u1 = new User("zhangsan", "123");
        User u2 = new User("lisi", "456");
        User u3 = new User("zhangsan", "666");
        List<User> userList = Lists.newArrayList(u1, u2, u3);
        //也可以使用StringUtils.join方法连接   （将元素都转为String类型，并串联它们，中间的分隔符是逗号）
        String join = userList.stream().map(User::getUsername).collect(Collectors.joining("|"));
        System.out.println(join);
        /*
            zhangsan|lisi|zhangsan
         */
    }
```

5. public static Collector<CharSequence, ?, String> joining(CharSequence delimiter,
                                                            CharSequence prefix,
                                                            CharSequence suffix)

```java
    /**
     * Collectors类的方法：public static Collector<CharSequence, ?, String> joining(CharSequence delimiter,
     *                                                              CharSequence prefix,
     *                                                              CharSequence suffix)
     */
    @Test
    public void test() {
        User u1 = new User("zhangsan", "123");
        User u2 = new User("lisi", "456");
        List<User> userList = Lists.newArrayList(u1, u2);
        //Collectors.joining(分隔符，前缀，后缀)   有时候使用In拼接SQL语句可以用到
        String join = userList.stream().map(User::getUsername).collect(Collectors.joining(",","[","]"));
        System.out.println(join);
        /*
            [zhangsan,lisi]
         */
    }
```

6. summingInt(ToIntFunction<? super T> mapper); //计算某个字段求和

```java
    /**
     * Collectors类的方法：public static <T> Collector<T, ?, Integer>
     *     summingInt(ToIntFunction<? super T> mapper)
     */
    @Test
    public void test() {
        User u1 = new User(1, "zhangsan", "123", -1, 12);
        User u2 = new User(2, "lisi", "456", -1, 13);
        List<User> userList = Lists.newArrayList(u1, u2);
        //计算某个字段求和
        Integer sum = userList.stream().collect(Collectors.summingInt(User::getScore));
        System.out.println(sum);
        /*
            25
         */
    }
```

7. public static <T, K> Collector<T, ?, Map<K, List<T>>>
   groupingBy(Function<? super T, ? extends K> classifier) //将List按泛型中的某个属性分组

```java
    /**
     * Collectors类的方法：public static <T, K> Collector<T, ?, Map<K, List<T>>>
     *     groupingBy(Function<? super T, ? extends K> classifier)
     */
    @Test
    public void test() {
        User u1 = new User(1, "zhangsan", "123", -1, 12);
        User u2 = new User(2, "lisi", "456", -1, 13);
        User u3 = new User(3, "wangwu", "789", -2, 22);
        List<User> userList = Lists.newArrayList(u1, u2, u3);
        //将List按泛型中的某个属性分组  （默认是全部List一组 类似mysql的分组）
        Map<Integer, List<User>> groupMap = userList.stream().collect(Collectors.groupingBy(User::getParentId));
        for (Map.Entry<Integer, List<User>> entry : groupMap.entrySet()) {
            //打印各自分组，打印时带上map的key
            System.out.println("当前分组号为：" + entry.getKey() + "| 对应的成员分别有：" + entry.getValue());
        }
        /*
            当前分组号为：-1| 对应的成员分别有：[User{id=1, username='zhangsan', password='123', parentId=-1, score=12}, User{id=2, username='lisi', password='456', parentId=-1, score=13}]
            当前分组号为：-2| 对应的成员分别有：[User{id=3, username='wangwu', password='789', parentId=-2, score=22}]
         */
    }
```

8. Collector<T, ?, Map<K, D>> groupingBy(Function<? super T, ? extends K> classifier,
                                         Collector<? super T, A, D> downstream); 

//1个参数：按给定元素分组   2个参数：按给定元素分组，并指定返回值的类型 

```java
    /**
     * Collectors类的方法：public static <T, K, A, D>
     *     Collector<T, ?, Map<K, D>> groupingBy(Function<? super T, ? extends K> classifier,
     *                                           Collector<? super T, A, D> downstream)
     */
    @Test
    public void test() {
        User u1 = new User(1, "zhangsan", "123", -1, 12);
        User u2 = new User(2, "lisi", "456", -1, 13);
        User u3 = new User(3, "wangwu", "789", -2, 22);
        List<User> userList = Lists.newArrayList(u1, u2, u3);
        //将Set按泛型中的某个属性分组  （默认是全部HashSet一组 类似mysql的分组）
        Map<Integer, Set<User>> groupMap = userList.stream().collect(Collectors.groupingBy(User::getParentId, Collectors.toSet()));
        for (Map.Entry<Integer, Set<User>> entry : groupMap.entrySet()) {
            //打印各自分组，打印时带上map的key
            System.out.println("当前分组号为：" + entry.getKey() + "| 对应的成员分别有：" + entry.getValue());
        }
        /*
            当前分组号为：-1| 对应的成员分别有：[User{id=1, username='zhangsan', password='123', parentId=-1, score=12}, User{id=2, username='lisi', password='456', parentId=-1, score=13}]
            当前分组号为：-2| 对应的成员分别有：[User{id=3, username='wangwu', password='789', parentId=-2, score=22}]
         */
    }
```

9. public static <T, K, D, A, M extends Map<K, D>>
   Collector<T, ?, M> groupingBy(Function<? super T, ? extends K> classifier,
                                 Supplier<M> mapFactory,
                                 Collector<? super T, A, D> downstream); 

//3个参数 arg1:按条件分组 arg2:指定返回值Map的类型 arg3:指定Map的value的类型   (一般情况下就是HashMap和ArrayList类型  所以一般就是使用一个参数的即可)

```java
    /**
     * Collectors类的方法：public static <T, K, D, A, M extends Map<K, D>>
     *     Collector<T, ?, M> groupingBy(Function<? super T, ? extends K> classifier,
     *                                   Supplier<M> mapFactory,
     *                                   Collector<? super T, A, D> downstream)
     */
    @Test
    public void test() {
        User u1 = new User(1, "zhangsan", "123", -1, 12);
        User u2 = new User(2, "lisi", "456", -1, 13);
        User u3 = new User(3, "wangwu", "789", -2, 22);
        List<User> userList = Lists.newArrayList(u1, u2, u3);
        //3个参数 arg1:按条件分组 arg2:指定返回值Map的类型 arg3:指定Map的value的类型   (一般情况下就是HashMap和ArrayList类型  所以一般就是使用一个参数的即可)
        Map<Integer, Set<User>> groupMap = userList
                .stream()
                .collect(Collectors.groupingBy(User::getParentId, LinkedHashMap::new, Collectors.toSet()));
        for (Map.Entry<Integer, Set<User>> entry : groupMap.entrySet()) {
            //打印各自分组，打印时带上map的key
            System.out.println("当前分组号为：" + entry.getKey() + "| 对应的成员分别有：" + entry.getValue());
        }
        /*
            当前分组号为：-1| 对应的成员分别有：[User{id=1, username='zhangsan', password='123', parentId=-1, score=12}, User{id=2, username='lisi', password='456', parentId=-1, score=13}]
            当前分组号为：-2| 对应的成员分别有：[User{id=3, username='wangwu', password='789', parentId=-2, score=22}]
         */
    }
```

10. public static <T, K, A, D>
    Collector<T, ?, Map<K, D>> groupingBy(Function<? super T, ? extends K> classifier,
                                          Collector<? super T, A, D> downstream); //分组求和

```java
    /**
     * Collectors类的方法：public static <T, K, A, D>
     *     Collector<T, ?, Map<K, D>> groupingBy(Function<? super T, ? extends K> classifier,
     *                                           Collector<? super T, A, D> downstream)
     */
    @Test
    public void test() {
        User u1 = new User(1, "zhangsan", "123", -1, 12);
        User u2 = new User(2, "lisi", "456", -1, 13);
        User u3 = new User(3, "wangwu", "789", -2, 22);
        List<User> userList = Lists.newArrayList(u1, u2, u3);
        //求出各部门的分数总和
        Map<Integer, Integer> groupMap = userList
                .stream()
                .collect(Collectors.groupingBy(User::getParentId, Collectors.summingInt(User::getScore)));
        for (Map.Entry<Integer, Integer> entry : groupMap.entrySet()) {
            System.out.println("当前部门序号为：" + entry.getKey() + "| 分数总和为：" + entry.getValue());
        }
        /*
            当前部门序号为：-1| 分数总和为：25
            当前部门序号为：-2| 分数总和为：22
         */
    }
```



11. public static <T, K, A, D>
    Collector<T, ?, Map<K, D>> groupingBy(Function<? super T, ? extends K> classifier,
                                          Collector<? super T, A, D> downstream) //二重分组

```java
    /**
     * 对流式处理后的数据进行二重分组
     */
    @Test
    public void test9(){
        //准备一批测试数据
        //两场考试
        String exam1 = this.getUUID();
        String exam2 = this.getUUID();
        //两份试卷
        String testpaper1 = this.getUUID();
        String testpaper2 = this.getUUID();
        //三道题目
        String section1 = this.getUUID();
        String section2 = this.getUUID();
        String section3 = this.getUUID();

        //初始化相关VO类信息
        LearnonlineVO VO1 = new LearnonlineVO(1, exam1, "考试1号", testpaper1, "试卷1号", section1, "题目1号");
        LearnonlineVO VO2 = new LearnonlineVO(2, exam1, "考试1号", testpaper1, "试卷1号", section2, "题目2号");
        LearnonlineVO VO3 = new LearnonlineVO(3, exam2, "考试2号", testpaper2, "试卷2号", section1, "题目1号");
        LearnonlineVO VO4 = new LearnonlineVO(4, exam2, "考试2号", testpaper1, "试卷1号", section1, "题目1号");
        LearnonlineVO VO5 = new LearnonlineVO(5, exam1, "考试1号", testpaper2, "试卷2号", section3, "题目3号");

        List<LearnonlineVO> list = new ArrayList<LearnonlineVO>() {
            {
                add(VO1);
                add(VO2);
                add(VO3);
                add(VO4);
                add(VO5);
            }
        };
        //二重分组(先对考试ID分组，再对Map的Value再进一步对试卷ID进行分组)
        Map<String, Map<String, List<LearnonlineVO>>> collect = list.stream().collect(
                Collectors.groupingBy(LearnonlineVO::getExamId, Collectors.groupingBy(LearnonlineVO::getTestpaperId))
        );
    }
```





11. public static <T>
    Collector<T, ?, Map<Boolean, List<T>>> partitioningBy(Predicate<? super T> predicate);

//按照分数及格与否对学生进行分组

```java
    /**
     * Collectors类的方法：public static <T>
     *     Collector<T, ?, Map<Boolean, List<T>>> partitioningBy(Predicate<? super T> predicate)
     */
    @Test
    public void test() {
        User u1 = new User(1, "zhangsan", "123", -1, 12);
        User u2 = new User(2, "lisi", "456", -1, 13);
        User u3 = new User(3, "wangwu", "789", -2, 22);
        List<User> userList = Lists.newArrayList(u1, u2, u3);
        //按照分数线及格与否进行分组
        Map<Boolean, List<User>> groupMap = userList
                .stream()
                .collect(Collectors.partitioningBy(s -> s.getScore() > 20));
        for (Map.Entry<Boolean, List<User>> entry : groupMap.entrySet()) {
            System.out.println("当前组为：" + entry.getKey() + "| 组员分别有：" + entry.getValue());
        }
        /*
            当前组为：false| 组员分别有：[User{id=1, username='zhangsan', password='123', parentId=-1, score=12}, User{id=2, username='lisi', password='456', parentId=-1, score=13}]
            当前组为：true| 组员分别有：[User{id=3, username='wangwu', password='789', parentId=-2, score=22}]
         */
    }
```

12. public static <T, D, A>
    Collector<T, ?, Map<Boolean, D>> partitioningBy(Predicate<? super T> predicate,
                                                    Collector<? super T, A, D> downstream); 

//arg1：True或False过滤条件 arg2：指定Map的value的类型

```java
    /**
     * Collectors类的方法：public static <T, D, A>
     *     Collector<T, ?, Map<Boolean, D>> partitioningBy(Predicate<? super T> predicate,
     *                                                     Collector<? super T, A, D> downstream)
     */
    @Test
    public void test() {
        User u1 = new User(1, "zhangsan", "123", -1, 12);
        User u2 = new User(2, "lisi", "456", -1, 13);
        User u3 = new User(3, "wangwu", "789", -2, 22);
        List<User> userList = Lists.newArrayList(u1, u2, u3);
        //arg1：True或False过滤条件 arg2：指定Map的value的类型
        Map<Boolean, Set<User>> groupMap = userList
                .stream()
                .collect(Collectors.partitioningBy(s -> s.getScore() > 20, Collectors.toSet()));
        for (Map.Entry<Boolean, Set<User>> entry : groupMap.entrySet()) {
            System.out.println("当前组为：" + entry.getKey() + "| 组员分别有：" + entry.getValue());
        }
        /*
            当前组为：false| 组员分别有：[User{id=1, username='zhangsan', password='123', parentId=-1, score=12}, User{id=2, username='lisi', password='456', parentId=-1, score=13}]
            当前组为：true| 组员分别有：[User{id=3, username='wangwu', password='789', parentId=-2, score=22}]
         */
    }
```

13. public static <T>
    Collector<T, ?, IntSummaryStatistics> summarizingInt(ToIntFunction<? super T> mapper);

//汇总list的数量、和、最大值、最小值、平均值 （仅针对List泛型是数值类型的）

```java
    /**
     * Collectors类的方法：public static <T>
     *     Collector<T, ?, IntSummaryStatistics> summarizingInt(ToIntFunction<? super T> mapper)
     */
    @Test
    public void test() {
        User u1 = new User(1, "zhangsan", "123", -1, 12);
        User u2 = new User(2, "lisi", "456", -1, 13);
        User u3 = new User(3, "wangwu", "789", -2, 22);
        List<User> userList = Lists.newArrayList(u1, u2, u3);
        //汇总list的数量、和、最大值、最小值、平均值 （仅针对List泛型是数值类型的）
        IntSummaryStatistics collect = userList
                .stream()
                .map(User::getScore)
                .collect(Collectors.summarizingInt(Integer::valueOf));
        System.out.println(collect);
        /*
            IntSummaryStatistics{count=3, sum=47, min=12, average=15.666667, max=22}
         */
    }
```

14. public static <T, K, U>
    Collector<T, ?, Map<K,U>> toMap(Function<? super T, ? extends K> keyMapper,
                                    Function<? super T, ? extends U> valueMapper,
                                    BinaryOperator<U> mergeFunction);

//arg1:key生成器 arg2:value生成器 arg3:当key相同时，指定取oldValue还是newValue

```java
    /**
     * Collectors类的方法：public static <T, K, U>
     *     Collector<T, ?, Map<K,U>> toMap(Function<? super T, ? extends K> keyMapper,
     *                                     Function<? super T, ? extends U> valueMapper,
     *                                     BinaryOperator<U> mergeFunction)
     */
    @Test
    public void test() {
        User u1 = new User(1, "zhangsan", "123", -1, 12);
        User u2 = new User(2, "lisi", "456", -1, 13);
        User u3 = new User(3, "wangwu", "789", -2, 22);
        List<User> userList = Lists.newArrayList(u1, u2, u3);
        //arg1:key生成器 arg2:value生成器 arg3:当key相同时，指定取oldValue还是newValue
        Map<Integer, User> map = userList
                .stream()
                .collect(Collectors.toMap(User::getId, e -> e, (oldValue, newValue) -> newValue));
        for (Map.Entry<Integer, User> entry : map.entrySet()) {
            System.out.println("当前key为：" + entry.getKey() + "| 对应的value为：" + entry.getValue());
        }
        /*
            当前key为：1| 对应的value为：User{id=1, username='zhangsan', password='123', parentId=-1, score=12}
            当前key为：2| 对应的value为：User{id=2, username='lisi', password='456', parentId=-1, score=13}
            当前key为：3| 对应的value为：User{id=3, username='wangwu', password='789', parentId=-2, score=22}
         */
    }
```



**//总结Collectors工具类 与 Collector接口 与 collect()总结方法之间的联系**

```java
    //collect()是终结方法，形参支持接受Collector接口类型
    //故可以对经过流式处理的数据进行各种操作，比如如果是数值型的话，支持求和，求最值，平均值等
    //如果是类类型的话，也支持进行各种条件进行分组，也支持分组过后进行分组求和或求最值，平均值等
    //其中类类型也支持转成Map
```





#### 1.2.7、JDK8日期-时间类

1. LocalDate

```java
    /**
     * LocalDate类
     */
    @Test
    public void test() {
        //获取当前日期 直接经过格式化的
        LocalDate now = LocalDate.now(); //结果：2020-06-17
        //自己设置的日期
        LocalDate of = LocalDate.of(2020, 6, 17);//结果：2020-06-17
        //获取日期的年
        int year = now.get(ChronoField.YEAR);//获取年份，结果：2020 （推荐使用）
        int month = now.get(ChronoField.MONTH_OF_YEAR); //获取月份，结果：6 （推荐使用）
        int day = now.get(ChronoField.DAY_OF_MONTH);//获取每月中的第几天，结果：17 （推荐使用）
        int i = now.get(ChronoField.DAY_OF_WEEK);//获取星期几，结果：3
        System.out.println("年份：" + year);
        System.out.println("月份：" + month);
        System.out.println("第：" + day + " 号");
        System.out.println("星期" + i);
        /*
            年份：2020
            月份：10
            第：21 号
            星期3
         */
    }


    /**
     * 计算开通单位的使用状态
     *
     * @param startTime 开始日期
     * @param endTime   结束日期
     * @return
     * @author Shen Peihong
     * @date 2021/1/5
     */
    public static Integer getState(String startTime, String endTime) {
        Integer state;
        LocalDate now = LocalDate.now();
        if (now.isBefore(LocalDate.parse(startTime))) {
            state = UnitConstantUtil.STATE_NOT_START;
        } else if (now.isAfter(LocalDate.parse(endTime))) {
            state = UnitConstantUtil.STATE_EXPIRED;
        } else {
            state = UnitConstantUtil.STATE_USING;
        }
        return state;
    }


```

2. LocalTime

```java
    /**
     * LocalTime类
     */
    @Test
    public void test() {
        LocalTime now = LocalTime.now(); //获取当前时间 结果：18:16:41.525
        LocalTime of = LocalTime.of(18, 16, 01); //设置时间 结果：18:16:01
        int hour = of.get(ChronoField.HOUR_OF_DAY); //小时 结果：18
        int minute = of.get(ChronoField.MINUTE_OF_HOUR); //分钟 结果：16
        int second = of.get(ChronoField.SECOND_OF_MINUTE); //秒 结果：1
        System.out.println("当前时间为：" + now);
        System.out.println("小时：" + hour);
        System.out.println("分钟：" + minute);
        System.out.println("秒：" + second);
        /*
            当前时间为：19:59:37.146
            小时：18
            分钟：16
            秒：1
         */
    }
```

3. LocalDateTime

```java
    /**
     * LocalDateTime类 + DateTimeFormatter类格式化
     */
    @Test
    public void test() {
        LocalDateTime now = LocalDateTime.now(); //获取当前日期+时间 结果：2020-06-17T18:21:37.098
        LocalDateTime of = LocalDateTime.of(2020, Month.JUNE, 17, 18, 21, 36); //自定义时间带T 需要格式化
        DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy-MM-dd hh:mm:ss", Locale.CHINESE); //格式化对象
        String format = LocalDateTime.now().format(dtf);//当前时间 进行格式化  //这个不是24小时制的，要具体后面看（备注）
    }
```

4. Instant（时间戳）

```java
    /**
     * Instant 时间戳
     */
    @Test
    public void test() {
        long epochSecond = Instant.now().getEpochSecond(); //获得时间戳精确到秒
        long toEpochMilli = Instant.now().toEpochMilli(); //获得时间戳精确到毫秒 等同 System.currentMillis();
    }
```

5. LocalDateTime转Date类型

```java
    /**
     * LocalDateTime转Date类型
     */
    @Test
    public void test() {
        //自定义某个时间
        LocalDateTime localDateTime = LocalDateTime.of(2020, 10, 22, 22, 17, 00);
        Date date = Date.from(localDateTime.atZone(ZoneId.systemDefault()).toInstant());
    }
```



### 1.3、IO流

#### 1.3.1、IO流的具体业务场景

IO流(输入流/输出流)可以应用到文件上传/下载、网络编程传输等

**Q：为何内存与硬盘的数据传输需要用到IO流呢？传输过程又为何需要使用字节？用其它Java类型可以吗？**

A1：大到两台服务器，小到两块硬件，它们之间都是需要有通道才能进行数据传输。

A2：因为我们的计算机是只懂二进制数据，所以转换成字节是通用类型(1个字节=8个位) //序列化与反序列化

A3：只要你使用的输入/输出流里面封装了序列化与反序列化操作，那么就可以，比如DataOutputStream类。

字节流的抽象父类：

- InputStream
- OutputStream

字符流的抽象父类：

- Reader
- Writer

#### 1.3.2、IO流的代码demo

1、文件输出流

````java
    /**
     * 输出字节数据到D盘文件中
     */
    @Test
    public void test1() {
        File file = null;
        OutputStream fos = null;
        OutputStream bos = null;
        try {
            //指向该路径的某个文件或文件夹，若无该文件/文件夹会新建一个 （此时管道已建好）
            file = new File("D:\\temp\\IO流练习demo\\file1.txt");
            //获取输出流对象
            fos = new FileOutputStream(file);
            //获取输出流的缓冲输出流对象 （相当于嵌套一层）
            bos = new BufferedOutputStream(fos);
            byte[] bytes = "输出数据".getBytes();
            //将数据写入缓冲流
            bos.write(bytes);
            //刷新缓冲流，若这里不刷新的话，那么缓冲流会等满的时候才会往指定文件传输
            bos.flush();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                //数据传输完毕需要关闭管道
                if (bos != null) bos.close();
                if (fos != null) bos.close();
                if (file != null) bos.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
````

2、文件输入流

```java
    /**
     * 输入流读取D盘文件的数据
     */
    @Test
    public void test2(){
        File file = null;
        InputStream fis = null;
        InputStream bis = null;
        try {
            //指向文件
            file = new File("D:\\temp\\IO流练习demo\\input2.txt");
            //获取文件输入流
            fis = new FileInputStream(file);
            //获取文件输入缓冲流
            bis = new BufferedInputStream(fis);
            //定义字节数组待接收输入字节数据
            byte[] bytes = new byte[1024];
            //读取输入字节数据
            int length = bis.read(bytes);
            if (length!=0){
                //字节数组转String类型
                String str = new String(bytes, 0, length);
                System.out.println(str);
                /*
                    输入数据
                 */
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                if (bis!=null) bis.close();
                if (fis!=null) fis.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```

3、对象输出流(处理流/封装流)

```java
    /**
     * 使用处理流(处理序列化问题)，不需要再通过转字节数组了
     * ObjectOutputStream
     */
    @Test
    public void test3() {
        OutputStream fos = null;
        ObjectOutputStream oos = null;
        try {
            //获取文件输出流对象
            fos = new FileOutputStream("D:\\temp\\IO流练习demo\\objectOutputStream.txt");
            //获取处理流对象（对象输出流 相当于内层嵌套了文件输出流）
            oos = new ObjectOutputStream(fos);
            User user = new User("zhangsan", "123456");
            //写入对象
            oos.writeObject(user);
            //写入的时候，在txt文件呈现的也是二进制数据来的，只不过传输过程我们使用了处理流(封装流)
            oos.flush();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                if (oos != null) oos.close();
                if (fos != null) fos.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```

4、对象输入流（处理流）

```java
    /**
     * 使用对象输入流（处理流来的）
     */
    @Test
    public void test4() {
        FileInputStream fis = null;
        ObjectInputStream ois = null;
        try {
            fis = new FileInputStream("D:\\temp\\IO流练习demo\\objectOutputStream.txt");
            ois = new ObjectInputStream(fis);
            User user = (User) ois.readObject();
            System.out.println(user);
            /*
                User{id=null, username='zhangsan', password='123456', parentId=null, score=null}
             */
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                if (ois != null) ois.close();
                if (fis != null) fis.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```

5、输出字符流

```java
    /**
     * 输出字符流
     */
    @Test
    public void test5(){
        FileWriter fileWriter = null;
        BufferedWriter bufferedWriter = null;
        try {
            //指向文件字符输出流
            fileWriter = new FileWriter("D:\\temp\\IO流练习demo\\writer.txt");
            //使用文件字符缓冲输出流
            bufferedWriter = new BufferedWriter(fileWriter);
            //待写入的字符串
            String str = "张三的歌";
            bufferedWriter.write(str, 0, str.length());
            //刷新缓冲区
            bufferedWriter.flush();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                if (bufferedWriter != null) bufferedWriter.close();
                if (fileWriter != null) fileWriter.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```

6、输入字符流

```java
    /**
     * 输入字符流
     */
    @Test
    public void test6() {
        FileReader fileReader = null;
        BufferedReader bufferedReader = null;
        try {
            fileReader = new FileReader("D:\\temp\\IO流练习demo\\writer.txt");
            bufferedReader = new BufferedReader(fileReader);
            String str = null;
            int i = 0;
            //读取字符对象只有获取一行的api，所以需要用while循环遍历取出来
            while ((str = bufferedReader.readLine()) != null) {
                System.out.println("当前行号为：" + (++i) + "，内容为：" + str);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                if (bufferedReader != null) bufferedReader.close();
                if (fileReader != null) fileReader.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```



#### 1.3.3、POI

1、POI导出demo，如下图所示

![image-20201222174013637](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222174015.png)

```java
    /**
     * 需求一、导出1行1列的数据到D盘的某个文件下  (有样式)
     *
     */
    @Test
    public void test2() {
        FileOutputStream fos = null;
        Workbook workbook = null;
        try {
            fos = new FileOutputStream(new File("D:\\temp\\IO流练习demo\\POI\\poi2.xlsx"));
            workbook = new XSSFWorkbook();
            Sheet sheet = workbook.createSheet("sheet1");
            //导出表头
            this.exportTitle(sheet);
            //处理将要导出的list数据
            this.exportHandleData(sheet, this.getPOITestData(), "子账户");
            //合并单元格指定开始行，结束行， 开始列，结束列
            CellRangeAddress cellRangeAddress = new CellRangeAddress(1, 3, 0, 0);
            sheet.addMergedRegion(cellRangeAddress);
            //将工作簿输出到文件输出流指定的文件地址
            workbook.write(fos);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                if (fos != null) {
                    fos.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    /**
     * 提供POI相关的初始数据
     * @return
     */
    private List<POIVO> getPOITestData() {
        return new ArrayList<POIVO>() {
            {
                add(new POIVO("1", "12", "123", "123"));
                add(new POIVO("2", "45", "456", "456"));
                add(new POIVO("3", "78", "789", "789"));
            }
        };
    }

    /**
     * 导出表头
     * @param sheet 指定表
     */
    private void exportTitle(Sheet sheet){
        Row writeRow = sheet.createRow(0);
        int columnIndex = 0;
        writeRow.createCell(columnIndex++).setCellValue("模块");
        writeRow.createCell(columnIndex++).setCellValue("序号");
        writeRow.createCell(columnIndex++).setCellValue("标题");
        writeRow.createCell(columnIndex++).setCellValue("账号");
        writeRow.createCell(columnIndex++).setCellValue("密码");
    }

    //处理将要导出的List数据

    /**
     * 处理将要导出的List数据
     * @param sheet 待处理的表
     * @param VOList 待导出的数据
     * @param module 模块名
     */
    private void exportHandleData(Sheet sheet, List<POIVO> VOList, String module){
        //遍历list的数据，填充每一行数据
        int rowIndex = 1, columnIndex = 1;
        //设置模块名(待合并单元格)
        for (POIVO vo : VOList) {
            Row writeRow = sheet.createRow(rowIndex);
            writeRow.createCell(0).setCellValue(module);
            writeRow.createCell(columnIndex++).setCellValue(vo.getId());
            writeRow.createCell(columnIndex++).setCellValue(vo.getTitle());
            writeRow.createCell(columnIndex++).setCellValue(vo.getUsername());
            writeRow.createCell(columnIndex++).setCellValue(vo.getPassword());
            rowIndex += 1;
            columnIndex = 1;
        }
    }
```

//POI常用的一些方法

```java
    /**
     * 汇总一些POI常用的方法
     */
    @Test
    public void commonUseMethod(){
        SXSSFWorkbook workbook = new SXSSFWorkbook();
        Sheet sheet = workbook.getSheetAt(0);
        int firstRowNum = sheet.getFirstRowNum();//获得sheet表第一行的行数
        int lastRowNum = sheet.getLastRowNum();//获得sheet表最后一行的行数
        int lastColumnNum = sheet.getPhysicalNumberOfRows();//获得sheet表最后一列的列数
    }
```



### 1.4、NIO

#### 1.4.1、NIO基本概念





### 1.5、注解与反射

#### 1.5.1、元注解

作用：负责注解其它注解，相当于约束或声明其它注解的。

**@Target：**用于描述注解的使用范围（即描述的注解可以用在什么地方，比如方法上或类上或构造方法上）

![image-20201222174130088](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222174131.png)

**@Retention：**标识需要在什么级别保存该注解信息，用于描述注解的生命周期 （SOURCE < CLASS < **RUNTIME**）

@Document：说明该注解将被包含在javadoc中

@Inherited：说明子类可以继承父类中的该注解



#### 1.5.2、自定义注解

```java
//生命周期
@Retention(RetentionPolicy.RUNTIME)
//作用范围
@Target({ElementType.TYPE,ElementType.METHOD})
@interface MyAnnotation{
    //别名
    @AliasFor("name")
    String value();

    @AliasFor("value")
    String name() default "12323";//添加默认值，不然使用时必须指定

    String[] list() default {"123","456"};
}
```



#### 1.5.3、反射

```java
    /**
     * 通过反射获取构造方法、成员变量、方法、变量
     */
    @Test
    public void test1() throws Exception {
        Class c1 = UserPO.class;
        //Class对象实例化对象的话，只支持空参构造方法
        UserPO u1 = (UserPO) c1.newInstance();

        //获取Class对象中指定的构造方法对象，加了Declared说明private的也可以获取到
        Constructor constructor1 = c1.getDeclaredConstructor(String.class);
        //虽然声明成private的可以获取到，但是需要设置可接触才能使用
        constructor1.setAccessible(true);
        //实例化对象，需设置初始值
        UserPO u2 = (UserPO) constructor1.newInstance("123");
        System.out.println(u2);
        /*
            UserPO{id='123', username='null', password='null'}
         */

        //获取Class对象中的变量
        Field id = c1.getDeclaredField("id");
        //凡是获取到的对象是private的，要使用之前都要设置成可接触的
        id.setAccessible(true);
        UserPO u3 = new UserPO();
        //可以给指定对象赋值
        id.set(u3, "666");
        System.out.println(u3);
        /*
            UserPO{id='666', username='null', password='null'}
         */

        //获取Class中的toString方法
        Method method = c1.getDeclaredMethod("toString", null);
        UserPO u4 = new UserPO("1", "123", "456");
        String invoke = (String) method.invoke(u4, null);
        System.out.println(invoke);
        /*
            UserPO{id='1', username='123', password='456'}
         */

        //获取某个变量的注解
        Field fieldId = c1.getDeclaredField("id");
        fieldId.setAccessible(true);
        JSONField annotation = fieldId.getAnnotation(JSONField.class);
        System.out.println(annotation);
        /*
            @com.alibaba.fastjson.annotation.JSONField(format=, label=, jsonDirect=false, unwr。。。
         */
        
        //获取注解的值
        Field field = c1.getDeclaredField("username");
        field.setAccessible(true);
        //判断是否存在该注解
        if (field.isAnnotationPresent(MyAnnotation.class)){
            MyAnnotation myAnnotation = field.getAnnotation(MyAnnotation.class);
            //获取注解指定变量的值
            String s = myAnnotation.seldDefine();
            System.out.println(s);
            /*
                123123
             */
        }
    }
```



#### 1.5.4、泛型

**定义：**即"参数化"类型。就是将类型由原来的具体的类型参数化，类似于方法中的变量参数，此时类型也定义成参数形式（可以称之为类型形参），然后在使用/调用时传入具体的类型（类型实参）。

**常用的泛型变量：**

- E：元素（Element），多用于java集合框架
- K：关键字（Key）
- N：数字（Number）
- T：类型（Type）
- V：值（Value）

**泛型的意义：**

- 适用于多种数据类型执行相同的代码（代码复用）
- 泛型中的类型在使用时指定，不需要强制类型转换（类型安全，编译器会检查类型）



**泛型形参与Object类型形参的区别：**如果泛型不使用上/下界通配符的话，那么方法参数 泛型形参和Object类型没啥区别

> 获取方法形参或返回值的参数泛型部分

```java
    //定义一个方法
	public void invodeMethod(Map<String, UserPO> map, String username, List<String> list){

    }

    /**
     * 试试看Method对象的 getParameterTypes和getGenericParameterTypes的区别
     * 两个都是获取方法参数的类型，但是第二个是连泛型都能给获取到
     */
    @Test
    public void test4() throws Exception {
        Method method = ReflectTest.class.getDeclaredMethod("invodeMethod", Map.class, String.class, List.class);
        Type[] genericParameterTypes = method.getGenericParameterTypes();
        for (Type genericParameterType : genericParameterTypes) {
            if (genericParameterType instanceof ParameterizedType) {
                ParameterizedType type = (ParameterizedType) genericParameterType;
                //获得泛型里面的类型
                Type[] arguments = type.getActualTypeArguments();
                for (Type argument : arguments) {
                    System.out.println(argument);
                }
            }
        }
    }
```





### 1.6、JVM虚拟机

![image-20201222174521861](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222174523.png)



#### 1.6.1、从.java到.class细节

**编译与翻译的区别：**

- 相同点：都是一种语言格式转换成另一种语言格式
- 不同点：翻译是语言转语言，而编译是一种文件格式转化成另一种文件格式

//.java -> .class 是先使用编译器进行编译的。Java的编译器只识别基于栈的指令集架构，所以.java会先转成栈指令(我们也可以使用javap -v XXX.class文件进行反编译，看栈指令)  .class就已经是二进制文件了，在idea或文本工具打开都是经过处理美化过的了。

**Q：如何查看.class真实的二进制文件？**

**A：**可以在linux环境使用hexdump指令，用来查看"二进制文件"的十六进制编码：**hexdump  XXX.class文件**





#### 1.6.2、类加载机制全过程

![image-20201222174457804](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201226232658.png)

//类从被加载到虚拟机内存中开始，到卸载出内存为止，它的整个生命周期包括：加载、验证、准备、解析、初始化、使用、卸载七个阶段，它们的顺序如下图

其中类的加载的过程就包括了加载、验证、准备、解析、**初始化**五个阶段

**一、加载：**加载过程就是负责找到二进制字节码(.class文件)

//注：.class文件是二进制文件，为啥导进文本编辑工具会乱码，而导进IDEA不会乱码，是因为IDEA反编译过了，所有文件导入文本编辑工具乱码的都是二进制文件，反之即是文本文件

1.通过一个类的全限定名(类名、类所在的包名)来获取定义此类的二进制字节流。

2.将这个字节流锁代表的静态存储结构转化为方法区运行时的数据结构。

3.在内存中生成一个代表这个类的 java.lang.Class对象，作为方法区这个类的各种数据的访问入口

4.加载class文件的方式：

- 从本地系统中直接加载

- 通过网络下载class文件(引入一个.class)

- 从zip，jar等归档文件中加载class文件
- 从专有数据库中提取class文件
- 从Java源文件动态编译为class文件



**二、链接**：链接过程负责对二进制字节码的格式进行校验，初始化装载类中的静态变量以及解析类中调用的接口、类

![image-20201227015159110](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201227015200.png)

**1、验证(链接的第一步)**：确保class文件的字节流中包含的信息符合当前虚拟机的要求

- 文件格式验证，验证字节流是否符合class文件格式的规范

- 元数据验证，对字节码描述的信息进行语义分析，以保证其符合Java语言规范的要求

- 字节码验证：通过数据流和控制流分析，确定程序语义是合法的、符合逻辑的。

- 符号引用验证：确保解析动作能正确执行



**2、准备：**准备阶段是正式为类变量(static成员变量)分配内存并设置类变量默认值(零值)的阶段，这些变量所使用的的内存都将在方法区中进行分配

- **只对static修饰的静态变量进行内存分配，赋默认值（零值）。**

- **对final的静态字面值常量直接赋初始值。（不会等到初始化阶段才去初始化了，直接这里就显式初始化了）**



**3、解析**：将常量池中的符号引用替换为直接引用（内存地址）的过程

符号引用：用一组符号来描述目标，可以是任何形式的字面量。

直接引用：可以是直接指向目标的指针、相对偏移量或是一个能间接定位到目标的句柄，入指向方法区某个类的一个指针。

Q：什么是符号引用？

A：在Java中，一个Java类将会编译成一个class文件，在编译时Java类并不知道所引用的类的实际地址，因此只能使用符号引用来代替。

//例：org.simple.Perple类引用了org.simple.Language类，在编译时Perple类并不知道Language类的实际内存地址，因此只能使用符号org.simple.Language(假如是这个)来暂时表示Language类的地址



**三、类初始化**

![image-20201227144603775](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201227144604.png)

类初始化时机：只有当对类的主动使用的时候才会导致类的初始化，类的主动使用包括以下六种：

- 创建类的实例，也就是new的方式

- 访问某个类或接口的静态变量，或者对该静态变量赋值

- 调用类的静态方法

- 反射（如 Class.forName("com.shenpeihong.Test")）

- 初始化某个类的子类，则其父类也会被初始化

- Java虚拟机启动时，标记该类自启动



#### 1.6.3、类加载器的分类

![image-20201227150218693](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201227150219.png)

![image-20201227155228541](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201227155230.png)

//对应的加载器只会去加载指定目录下的.class文件，比方最顶层的Bootstrap ClassLoader会去加载java、javax、sun名字开头的包的.class文件呢。



#### 1.6.4、获取类加载器的方式

![image-20201227153033995](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201227153215.png)

```java
    /**
     * 获取类加载器对象
     */
    @Test
    public void test(){
        //1、通过.class对象去获取加载该类的类加载器
        ClassLoader classLoader1 = this.getClass().getClassLoader();
        System.out.println(classLoader1);
        /*
            sun.misc.Launcher$AppClassLoader@18b4aac2
         */

        //2、通过当前线程对象去获取类加载器
        ClassLoader classLoader2 = Thread.currentThread().getContextClassLoader();
        System.out.println(classLoader2);
        /*
            sun.misc.Launcher$AppClassLoader@18b4aac2
         */

        //3、通过类提供的静态方法直接获取
        ClassLoader classLoader3 = ClassLoader.getSystemClassLoader();
        System.out.println(classLoader3);
        /*
            sun.misc.Launcher$AppClassLoader@18b4aac2
         */
    }
```



#### 1.6.5、双亲委派机制

![image-20201227154831328](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201227154832.png)

**1、双亲委派的工作原理：**

1.如果一个类加载器收到类加载请求，它并不会自己先去加载，而是把这个请求委托给父类的加载器去执行。

2.如果父类加载器还存在其父类加载，则进一步向上委托，依次递归，请求最终将到达顶层的启动类加载器

3.如果父类加载器可以完成类加载任务，就成功返回；倘若父类加载器无法完成此加载任务，子加载器才会尝试自己去加载。



**2、双亲委派机制的优势：**

1.避免类的重复加载：当父类已经加载了该类时，就没有必要子ClassLoader再加载一次。

2.防止核心API库被随意篡改：假设通过网络传递一个名为java.lang.Integer的类，通过双亲委派模式传递到启动类加载器，而启动类加载器在核心Java API发现这个名字的类，发现该类被加载，便不会重新加载。



#### 1.6.6、沙箱安全机制

![image-20201227160114826](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201227160117.png)



#### 1.6.7、运行时数据区

![image-20201220163336505](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201220163337.png)

![image-20201227163500973](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201227163503.png)

//运行一个进程可以占用多个端口，比方tomcat进程启动的时候，有一些端口是监听http请求，有一些端口是监听关闭tomcat的（具体配置可以看tomcat的 server.xml的配置）

**1.程序计数器**

特点：总是记录着下一条将要执行的指令的地址 （线程私有）



**2.方法区**

属于共享内存区域，存储已被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。

详细描述：static区，包含静态变量和静态成员方法，常量池（String字符串和final修饰的常量值等）。所以如果静态成员变量的值或者常量池的值(String类型可被修改)被修改了直接就会反映到其它类的对象中。   不用创建对象，类加载时初始化后就可以使用。线程安全



**3.堆区**

对于绝大多数应用来说，这块区域是JVM所管理的内存中最大的一块。线程共享，主要是存放对象实例和数组。内部会划分出多个线程私有的分配缓冲区（Thread Local Allocation Buffer，TLAB）。可以位于物理上不连续的空间，但是逻辑上要连续。

详细扩展：存放非静态成员变量，类new出来的对象 User u = new User(); //创建出来的u对象放在堆，而new User()对象的指向放在栈（类实例化的时候才创建堆区） //堆主要存放Java在运行过程中New出来的对象，凡是通过new生成的对象都存放在堆中（除了static变量），对于堆中的对象生命周期的管理由Java虚拟机的垃圾回收机制GC进行回收和统一管理。

常见问题：OOM异常，如果堆中没有内存完成实例分配，并且堆也无法再扩展时，抛出该异常。



**4.Java虚拟机栈区**

线程私有，生命周期与线程一致。描述的是Java方法执行的内存模型：每个方法在执行时都会创建一个**栈帧（Stack Frame）用于存储局部变量表、操作数栈、动态链接、方法出口等信息**。每一个方法从调用直至执行结束，就对应着一个栈帧从虚拟机栈中入栈到出栈的过程

//发生StackOverflowError错误是因为线程请求的栈深度大于虚拟机所允许的深度



**5.本地方法栈**

区别于Java虚拟机栈的是，Java虚拟机栈是为虚拟机执行Java方法（也就是字节码）服务，而本地方法栈则为虚拟机使用到的Native方法服务。也会有StackOveflowError 和 OOM异常。



**总结：**

1.方法区：使用static声明的方法和变量 一开始就直接加载到JVM的方法区中，是被线程共享的。

2.堆区：类实例化的时候才创建堆区，使用new创建的对象的引用(user)和非静态成员变量存在堆中，对象和非静态成员变量由垃圾回收机制GC回收和管理。

3.栈区：局部变量，即调用某个方法时，才生成对应的栈区（分配内存），然后里面的局部变量存放在栈区中，方法执行完毕后释放栈区对应的内存。

4.想要手动干预操作运行时数据区的话，需要执行Runtime里面的方法，把相对应的操作都封装到了RunTime这个类里面了，这个类构造器私有化，直接用饿汉式单例，类初始化的时候就直接去创建当前类的对象，并且提供一个访问对象的方法。





#### 1.6.8、类装载方式有两种

1.隐式装载：程序在运行过程中当碰到通过New等方式生成对象时，隐式调用类装载器加载对应的类到JVM中

2.显式装载，通过class.forName()等方法，显式加载需要的类



#### 1.6.9、变量的生命周期、存储位置

```java
public class C{
private List<String> list = new ArrayList<String>();(1) //堆
private static List<String> list = new ArrayList<String>();(2)    //方法区
public void m(){
	List<String> list = new ArrayList<String>();(3)    //栈
}
```

(1)类成员变量：成员变量就是方法外部，类的内部定义的变量， 其作用于受限定符限定，Private只能在本类中使用，protected子类可用，public任何地方都可用

​	生命周期：从对象创建开始到对象销毁结束

(2)类变量是指类的static变量，用类名可以直接访问

生命周期：从加载完类开始，直到销毁类后结束

(3)局部变量：局部变量是指定义在方法或代码块中的变量，局部变量必须初始化，在方法或代码块内有效，之外则无效，方法执行开始入栈时创建，执行完毕出栈时销毁(形参是局部变量，局部变量的数据存在于栈内存中)

生命周期：栈内存中的局部变量随着方法的消失而消失



#### 1.6.10、关于String字符串

​		其对象的引用都是存储在栈中的，如果是编译期已经创建好(直接用双引号定义的)就存储在常量池中，如果是运行期(new出来的) 才能确定的就存储在堆中。**在常量池中永远只有一份，在堆中有多份**。对于通过new产生一个字符串(假设为"china")时，会先去常量池中查找是否已经有了"china"对象，如果没有则在常量池中创建一个此字符串对象，然后堆中再创建一个常量池中此"china"对象的拷贝对象。这就是有道面试题：String s = new("xyz");产生几个对象?一个或两个，如果常量池中没有"xyz"的话，那就是两个(先常量池创建一个，再去堆再创建一个)

对于基础类型的变量和常量：**变量和引用存储在栈中，常量存储在常量池中**





### 1.7、网络编程

#### 1.7.1、常用网络名词及概念

**1、网络IP：**

- 外网IP：A类、B类、C类、D类、E类
- 内网IP：虚拟IP 10  172  192

//Java识别IP地址是用InetAddress类对象来识别的，所以我们不要误以为直接规定String ip = "192.168.xx.xx"就可以，需要用InetAddress ip = InetAddress.getByName("192.168.xx.xx");

**2、IP与端口**

通过IP定位计算机，通过port定位哪个软件

//注：端口号是不能重复的，端口号是一个数字

**3、Java代码获取某个IP对象**

```java
    /**
     * Java代码获取某个IP对象
     */
    @Test
    public void test() throws UnknownHostException {
        //也可以根据主机名(计算机名)构建InetAddress对象
        InetAddress ip = InetAddress.getByName("LAPTOP-OSAFAJBF");
        //也可以根据ip地址(字符串)构建InetAddress对象
        InetAddress ip1 = InetAddress.getByName("192.168.11.125");
    }
```

**4、Socket套接字**

在程序中我们通过Socket进行通信，在使用Socket通信的时候，需要指定所说的几个条件(ip、port、协议)

数据发送分为两步：

- 第一步是监听（等待数据发送过来），用来接收数据。需要指定监听的端口号

- 第二步是发送，需要指定发送到哪个计算机（IP地址），需要指定发送到这个计算机的哪个端口

**Socket中分为客户端和服务器端**

发送端一般为客户端，接收端为服务器端

一般情况下我们会有多个客户端，一个服务器端 （使用DatagramSocket类进行通信）

**5、单向聊天室**

思想：写一个发送线程和一个接收线程，线程内死循环(即开启线程后一直执行，不停下来)

```java
package com.sph.practice.test.markdown.internet;

import java.io.IOException;
import java.io.InputStream;
import java.net.ServerSocket;
import java.net.Socket;

/**
 * Created by Shen Peihong on 2020/10/22 22:39
 * Description:接收端
 *
 * @since 1.0.0
 */
public class AcceptThread implements Runnable {
    @Override
    public void run() {
        ServerSocket ss = null;
        Socket accept = null;
        InputStream input = null;
        try {
            ss = new ServerSocket(7878);
            //由于是TCP传输，所以需要建立与发送端的连接，执行到这里时，接收端此时会一直等待发送端的连接
            accept = ss.accept();
            while (true){
                //传输数据格式是IO流，所以获取输入流接收数据
                input = accept.getInputStream();
                //定义字节数组接收
                byte[] buf = new byte[1024];
                int length = input.read(buf);
                String str = new String(buf, 0, length);
                System.out.println("接收端已接收到数据 = " + accept.getInetAddress() + ":" + str);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                //先打开后释放
                if (input != null) {
                    input.close();
                }
                if (accept != null){
                    accept.close();
                }
                if (ss != null){
                    ss.close();
                }
            } catch (Exception e) {
                System.out.println("关闭连接对象出错");
            }

        }
    }
}

```

```java
package com.sph.practice.test.markdown.internet;

import java.io.IOException;
import java.io.OutputStream;
import java.net.Socket;
import java.util.Scanner;

/**
 * Created by Shen Peihong on 2020/10/22 22:39
 * Description:发送端
 *
 * @since 1.0.0
 */
public class SendThread implements Runnable {
    @Override
    public void run() {
        Socket socket = null;
        OutputStream output = null;
        try {
            //地址指向接收端
            socket = new Socket("127.0.0.1", 7878);
            //数据传输需要创建流管道
            output = socket.getOutputStream();
            Scanner scanner = new Scanner(System.in);//获取控制台输入，敲回车则为一行
            while (true){
                String line = scanner.nextLine();//获取敲回车前的一行数据
                byte[] bytes = new String(line).getBytes();
                output.write(bytes);
                output.flush();//刷新缓冲区
            }
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            try {
                if (output != null){
                    output.close();
                }
                if (socket != null){
                    socket.close();
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
}

```

```java
package com.sph.practice.test.controller.internet;

import com.sph.practice.test.markdown.internet.AcceptThread;
import com.sph.practice.test.markdown.internet.SendThread;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * Created by Shen Peihong on 2020/10/29 11:26
 * Description:
 *
 * @since 1.0.0
 */
@RestController
@RequestMapping("/tcp")
public class TcpCtl {

    @RequestMapping("/test1.do")
    public void test1() throws InterruptedException {
        //线程start一次线程执行完毕后就没了。所以需要让该线程不要停下来
        new Thread(new AcceptThread()).start();
        Thread.sleep(500);//防止发送线程先执行
        new Thread(new SendThread()).start();
    }

}

```

//注：需要使用tomcat启动，通过访问接口的形式去开启线程，不能在main方法中开启线程，因为非tomcat情况下，一个方法执行完毕后，整个虚拟机就不会继续执行。







### 1.8、并发编程 JUC

#### 1.8.1、Java并发的三大特性：原子性、可见性、有序性 

**原子性：**一个或多个操作，要么全部执行并且在执行过程中**不被任何因素打断，要么全部不执行**（例：对基本数据类型的变量的读取和赋值操作时原子性操作） 

**Q：如何保证原子性？** 

- 通过synchronized关键字定义同步代码块或同步方法保障原子性 

- 通过Lock接口保障原子性 

- 通过**Atomic**类型保障原子性



**可见性：**当一个线程修改了**共享变量**的值，其他线程能够看到修改的值 

**Q：volatile变量和普通变量的区别？**

- volatile的特殊规则保证了新值能立即同步到主内存，以及每次使用前从主内存刷新，因此我们可以说volatile保证了多线程操作时变量的可见性

**Q：如何保证可见性？**

- 使用volatile关键字标记内存屏障保证可见性 

- 通过synchronized关键字定义同步代码块或者同步方法保障可见性 

- 通过Lock接口保障可见性 

- 通过Atomic类型保障可见性 

- 通过final关键字保障可见性 



**有序性**：程序执行的顺序按照代码的先后顺序执行

**Q：如何保证有序性？**

- 通过synchronized关键字定义同步代码块或者同步方法保障可见性

- 通过Lock接口保障可见性



#### 1.8.2、浅谈Java中的Volatile关键字详解

**第一章 Volatile关键字概览**

> 1.1 多线程下变量的不可见性

（前执行子线程，子线程已经将boolean类型改成true，但是下面的主线程仍然是读不到子线程已经修改的最新值）

**//不开多线程的话，不同对象之间可以读到彼此的属性，多线程的话 不同线程之间变量默认是不可见的**

1.1.1、概述

   在多线程并发执行下，多个线程修改共享的成员变量，会出现一个线程修改了共享变量的值后，另一个线程不能直接看到该线程修改后的变量的最新值。

1.1.4、小结

  多**线程下修改共享变量会出现变量修改值后的不可见性**



1.1.5、volatile的特点

- 保证可见性（使用volatile变量时，会先同步工作内存到主内存。当修改volatile变量时，会刷新主内存）

- 不保证原子性（仍存在一个线程执行的时候，被其它线程干扰，比方多个线程同时执行num++的时候，使用AtomicInteger可以保证原子性）

- 防止指令重排（使用双重校验时，最好对该静态变量也加volatile，可以防止指令重排）

**详细解析指令重排**

比如redisTemplate = new RedisTemplate(); 会执行以下三步操作

A：分配内存空间

B：调用构造方法初始化对象

C：指向赋值号左边的变量

// 如果发生了指令重排，执行顺序是A->C->B的话，那么当执行完C时，此时另外一个线程在双重校验的if判断当前对象是否==null，这个时候命中了，但是还没执行完B，从而就会导致有问题。





> 1.2变量不可见性内存语义

1.2.1概述

**JMM（Java Memory Model）**：Java内存模型描述了Java程序中而告终变量（线程共享变量）的访问规则，以及在JVM中将变量存储到内存和从内存中读取变量这样的底层细节。

**JMM有以下规定：**

- 所有的共享变量都存储于主内存。这里所说的变量指的是实例变量和类变量。不包含局部变量，因为局部变量是线程私有的，不存在竞争问题

- 每一个线程还存在自己的工作内存，线程的工作内存，保留了被线程使用的变量的工作副本。

- 线程对变量的所有的操作（读，取）都必须在工作内存中完成，而不能直接读写主内存中的变量

- 不同线程之间也不能直接访问对方工作内存中的变量，线程间变量的值的传递需要通过主内存中转来完成

![image-20201222174629956](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222174631.png)

**Q：为何多线程之间的成员变量、类变量彼此不可见？（即是当一个线程A的变量修改后，但是另一个线程B访问A的这个变量仍然是修改之前的）**

A：因为多线程的变量都是放到主内存中的(JMM模型)，然后会从主内存copy一份工作副本到线程栈中，有些时候在自己的线程栈中更改完毕后，还没同步到主内存中，又因为主内存是各个线程之间的唯一中间媒介，所以说多线程之间的成员变量、类变量彼此不可见。

**Q：那么如何解决多线程之间变量不可见性的问题呢？**

- 需要访问到其他可能发生变量不可见问题的地方加锁

- 使用volatile关键字



**i.某一个线程进入synchronized代码块前后，执行过程如下：**

a.线程获得锁

b.清空工作内存

c.从主内存拷贝共享变量最新的值到工作内存成为副本

d.执行代码

e.将修改后的副本的值刷新回主内存中

f.线程释放锁



**ii.加上volatile**

类似于主内存和工作副本的变量做了绑定，实时同步更新

// volatile修改的变量可以在多线程并发修改下，实现线程间变量的可见性



#### 1.8.3、CopyOnWriteArrayList

```java
    /**
     * CopyOnWriteArrayList
     * 同样也会对代码块进行上锁，每次执行添加下一个值时，会先复制一份
     */
    @Test
    public void test12() throws InterruptedException {
        // 开多个线程，同时操作一个list 防止出现ConcurrentModify
        List<String> list = new CopyOnWriteArrayList<>();
        for (int i = 0; i < 100; i++) {
            new Thread(() -> {
                // 多线程情况下对ArrayList进行读写，会发生线程安全问题
                list.add(UUID.randomUUID().toString().substring(0, 4));
                System.out.println(list);
            }, "A").start();

        }
        TimeUnit.SECONDS.sleep(2);
    }
```



#### 1.8.4、CountDownLatch（减法计数器）

```java
    /**
     * CountDownLatch  减法计数器
     */
    @Test
    public void countDownLatchTest() throws InterruptedException {
        // 初始化计数器数量
        CountDownLatch countDownLatch = new CountDownLatch(2);
        for (int i = 0; i < 3; i++) {
            new Thread(() -> {
                System.out.println("打印：" + Thread.currentThread().getName());
                // 计数器自减
                countDownLatch.countDown();
            }, "A").start();
        }
        // 当计数器减到为0时，才往下继续执行，否则一直开启等待
        countDownLatch.await();
        System.out.println("计算器减到为0了");
    }
```



#### 1.8.5、ReentranReadWriteLock（读写锁）

```java
/**
 * Created by Shen Peihong on 2021/2/8 0:19
 * Description: JUC---读写锁
 *
 * @since 1.0.0
 */
public class ReadWriteLockTest {

    /**
     * 读写锁 ReentranReadWriteLock
     *
     * @throws InterruptedException
     */
    @Test
    public void test1() throws InterruptedException {
        MyCache cache = new MyCache();

        //开启5个线程存值，5个线程进行取值
        for (int i = 0; i < 20; i++) {
            final String temp = String.valueOf(i);
            new Thread(() -> {
                cache.put(temp, temp);
            }, temp).start();
        }

        for (int i = 0; i < 20; i++) {
            final String temp = String.valueOf(i);
            new Thread(() -> {
                cache.get(temp);
            }, temp).start();
        }

        TimeUnit.SECONDS.sleep(1);

    }

}

/**
 * 自定义缓存
 */
class MyCache {
    private volatile Map<String, Object> cacheMap = new HashMap<>();

    private ReadWriteLock readWriteLock = new ReentrantReadWriteLock();

    public void put(String key, Object Value) {
        readWriteLock.writeLock().lock();
        try {
            System.out.println(Thread.currentThread().getName() + "开始存值");
            cacheMap.put(key, Value);
            System.out.println(Thread.currentThread().getName() + "存值OK");
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            readWriteLock.writeLock().unlock();
        }
    }

    public void get(String key) {
        readWriteLock.readLock().lock();
        try {
            System.out.println(Thread.currentThread().getName() + "开始取值");
            cacheMap.get(key);
            System.out.println(Thread.currentThread().getName() + "取值OK");
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            readWriteLock.readLock().unlock();
        }
    }
}
```



#### 1.8.6、原子类

好处：执行的自增或相关计数操作都是符合原子性的

```java
    /**
     * AtomicInteger
     */
    @Test
    public void test1() {
        // 原子类，在自增或计数相关的操作保证了原子性操作
        // 底层机制：CAS
        // 底层操作：自旋锁
        AtomicInteger atomicInteger = new AtomicInteger();
        for (int i = 0; i < 30; i++) {
            System.out.println(atomicInteger.getAndIncrement());
        }
    }
```



#### 1.8.7、原子引用类型

```java
    /**
     * 原子引用类型
     */
    @Test
    public void test3() {
        AtomicReference<Integer> reference = new AtomicReference<>(1);
        boolean b = reference.compareAndSet(1, 2);
        System.out.println(b);
        System.out.println(reference.get());
    }
```



#### 1.8.8、防止ABA，引入原子版本引用类型

```java
    /**
     * 可能会出现ABA问题，我们不希望发生这种问题
     * 需要使用原子引用，里面提供了版本号(乐观锁的思想)
     * 使用Integer有一个坑点就是用了-128~127之外的数的话就不好使了，因为每次都会去new一个新的对象了。
     */
    @Test
    public void test2() throws InterruptedException {
        AtomicStampedReference<Integer> reference = new AtomicStampedReference<Integer>(1, 0);
        // 开启两个线程，线程1执行，线程2后执行，线程2发现版本号不对劲的话，打印出文本
        new Thread(() -> {
            int stamp1 = reference.getStamp();
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            boolean flag1 = reference.compareAndSet(1, 2, stamp1, stamp1 + 1);
            System.out.println("flag1 = " + flag1);
            System.out.println("value1 = " + reference.getReference());

            int stamp2 = reference.getStamp();
            boolean flag2 = reference.compareAndSet(2, 1, stamp2, stamp2 + 1);
            System.out.println("flag2 = " + flag2);
            System.out.println("value2 = " + reference.getReference());
        }, "A").start();

        new Thread(() -> {
            int stamp1 = reference.getStamp();
            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            // 由于版本号判断当前版本号已不对的话，不会完成期望值修改
            boolean flag3 = reference.compareAndSet(1, 6, stamp1, stamp1 + 1);
            System.out.println("flag3 = " + flag3);
            System.out.println("value3 = " + reference.getReference());

        }, "B").start();
        TimeUnit.SECONDS.sleep(2);
    }
```



#### 1.8.9、各种锁的理解

> 自旋锁

自定义一个自旋锁

```java
package com.sph.practice.test.juc.atomic;

import org.junit.Test;

import java.util.concurrent.TimeUnit;
import java.util.concurrent.atomic.AtomicReference;

/**
 * Created by Shen Peihong on 2021/2/9
 * Description: 自定义自旋锁（提供加锁方法和解锁方法）
 *
 * @since 1.0.0
 */
public class SpinLock {

    // 定义一个原子引用对象
    static AtomicReference<Thread> reference = new AtomicReference<>(null);

    // 加锁
    public void myLock() {
        Thread thread = Thread.currentThread();
        System.out.println(thread.getName() + "=> lock");

        while (!reference.compareAndSet(null, thread)) {

        }
    }

    // 解锁
    public void myUnlock() {
        Thread thread = Thread.currentThread();
        System.out.println(thread.getName() + "=> lock");

        reference.compareAndSet(thread, null);
    }

    
    
    static class SpinLockTest {

        static SpinLock spinLock = new SpinLock();

        public static void main(String[] args) {
            // 开启两个线程，两个都是去加锁和解锁，线程1代码延时久一点，线程2需要等待线程1解锁
            new Thread(() -> {
                spinLock.myLock();
                try {
                    TimeUnit.SECONDS.sleep(3);
                } catch (Exception e) {
                    e.printStackTrace();
                } finally {
                    spinLock.myUnlock();
                }
            }, "A").start();

            new Thread(() -> {
                // 线程2排在线程1后面，一直在自旋锁里面自旋，直到线程1解锁
                spinLock.myLock();
                try {
                    TimeUnit.SECONDS.sleep(1);
                    System.out.println("嘿嘿");
                } catch (Exception e) {
                    e.printStackTrace();
                } finally {
                    spinLock.myUnlock();
                }
            }, "B").start();
        }

    }
}



```



> 死锁





### 1.9、多线程

#### 1.9.1、多线程基础概念

特点：并发执行是我们肉眼看以为是同时执行 其实CPU是同一时间只能做一件事情，CPU是纳秒级别的 所以我们人眼观看就以为是同时运行了

- 一个进程可以并发多个线程，每条线程并行执行不同的任务，一个线程不能独立的存在，它必须是进程的一部分，一个进程一直运行，直到所有的非守护线程都结束运行后才能结束

- 平常执行的是单线程，打开一个线程需要使用线程对象调用start方法(执行重写Thread的run方法)

![image-20201222174657491](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222174659.png)

#### 1.9.2、线程调用规则

- 分时调度（平均分配）

- 抢占式调度（按照优先级）（Java使用的调度规则）

//设置优先级setPriority 获取优先级getPriority() //数字越大优先级越高



#### 1.9.3、线程的5种状态

![image-20201222174720367](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222174721.png)

**1、新建状态**：使用new关键字和Thread类或其子类建立一个线程对象后，该线程对象就处于新建状态。它保持这个状态直到对象被.start()开启

**2、就绪状态**：当线程对象调用了start()方法之后，该线程就进入就绪状态。就绪状态的线程处于就绪队列中，要等待JVM里线程调度器的调度。

**3、运行状态**：如果就绪状态的线程获取CPU资源，就可以执行我们书写的run()方法了，此时线程便处于运行状态，处于运行状态的线程最为复杂，它可以变为阻塞状 态、就绪状态和死亡状态。

**4、阻塞状态**：如果一个线程执行了sleep(睡眠)、suspend(挂起等方法)，失去所占用资源之后，该线程就从运行状态进入阻塞状态。在睡眠时间已到或获得设备资源后可以重新进入就绪状态。可以分为三种：

- 等待阻塞：运行状态中的线程执行wait()方法，使线程进入到等待阻塞状态

- 同步阻塞：线程在获取synchronized同步锁失败（因为同步锁被其他线程占用）

- 其他阻塞：通过调用线程的sleep()或join()发出I/O请求时，线程就会进入到阻塞状态。当sleep()状态超时，join()等待线程终止或超时，或者I/O处理完毕，线程重新转入就绪状态。

**5、死亡状态**：一个运行状态的线程完成任务或者其他终止条件发生时，该线程就切换到终止状态

常用状态切换的触发时机

- 新建状态->就绪状态：t.start();

- 就绪状态->运行状态：t.run();

- 运行状态->阻塞状态：Thread.yield();



#### 1.9.4、线程的常用方法

1、静态方法

- Thread.currentThread(); //获得当前方法所在的线程

- Thread.sleep(3000); //调用sleep() 线程休眠 。暂停执行

2、实例方法

> Mythread t1 = new Mythread("t1");

- t1.join(); //守护线程 t1.join()方法只会使主线程进入等待池等待t1线程执行完毕后才会被唤醒，所以执行顺序是：t1->主线程->其它开启的线程

- t1.start(); //开启线程

- t1.stop(); //终止线程（一枪崩了，直接停止线程 无后续操作）

- t1.interrupt(); //执行到这个方法的时候，主动停止线程，但是会进行一些操作 是用if(interrupted())判断当前线程是否为中断状态

- t1.getThreadGroup(); //获取t1线程所在的线程组 (线程在使用构造方法创建的时候，调用构造方法就可以直接给我们线程分配到某个线程组中 创建线程组 ThreadGroup tg = new ThreaGroup("frist threadGroup"))



#### 1.9.5、线程安全问题

问题：多线程工作下，同时执行同一段代码(i++)，比如多个售票点同时卖第100张表

解决：

```java
synchronized (this){
    //lock是对象,可以声明为Object类型,多个线程必须使用同一把锁才有效
    //若是实现Runnable接口，则里面的变量是共享的，不用声明为static
    //如果是继承Thread类的话，那么就需要声明锁为static
    //会产生线程安全问题的代码块
}
```



#### 1.9.6、基于多线程的定时任务

特点：其类或其父类实现了Runnable接口

**1、TimerTask**

概述：定时任务类

特点：实现了Runnable接口

使用：定时任务需执行的代码写在run()方法中

**2、Timer**

概述：定时类

下面介绍Timer类提供的API

2.1 public void schedule(TimerTask task, long delay);

定时任务类：

```java
/**
 * Created by Shen Peihong on 2020/10/22 11:05
 * Description: 定时任务类
 *
 * @since 1.0.0
 */
public class HelloTimerTask extends TimerTask {

    private Timer timer;
    //Thread类才有构造方法给取线程名字

    private static Integer num;

    public HelloTimerTask(Timer timer) {
        this.timer = timer;
    }

    public HelloTimerTask(){}

    @Override
    public void run() {
        System.out.println("定时任务：hello,world");
        //可能发生线程安全的代码放在同步块中  num初始化票=10
        /*synchronized (this){
            System.out.println("当前线程：" + Thread.currentThread().getName() + "定时任务：hello,world" + " ，当前num为：" + num);
            num --;
        }
        if (num <= 0){
            timer.cancel();
        }*/

        //由于cancel会使得当前定时任务执行完毕才结束，故放到最后

    }
}
```

定时类：

```java
/**
     * 延迟多少ms后去执行定时任务
     */
    @RequestMapping("/test1.do")
    public void test1(){
        Timer timer = new Timer();
        //arg1：定时任务类(将让定时任务执行的代码块写在run方法中)
        //arg2：延迟多少ms后执行
        timer.schedule(new HelloTimerTask(), 1000);//执行定时任务，延迟1秒
    }
```

2.2 public void schedule(TimerTask task, Date time);

```java
    /**
     *
     */
    @RequestMapping("/test2.do")
    public void test2(){
        Timer timer = new Timer();
        //arg1：定时任务类(将让定时任务执行的代码块写在run方法中)
        //arg2：指定某个时间执行该定时任务
        timer.schedule(new HelloTimerTask(), new Date(System.currentTimeMillis() + 1000));
    }
```

2.3 public void schedule(TimerTask task, long delay, long period);

```java
    /**
     *
     */
    @RequestMapping("/test4.do")
    public void test4(){
        Timer timer = new Timer();
        //arg1：定时任务类(将让定时任务执行的代码块写在run方法中)
        //arg2：延迟多少ms后开始执行定时任务
        //arg3：开始执行后每多久执行一次 （周期单位）
        timer.schedule(new HelloTimerTask(timer), 1000, 2000);
    }
```

2.4 public void cancel();

```java
/**
 * Created by Shen Peihong on 2020/10/22 11:05
 * Description:
 *
 * @since 1.0.0
 */
public class HelloTimerTask extends TimerTask {

    private Timer timer;
    //Thread类才有构造方法给取线程名字

    private static Integer num;

    public HelloTimerTask(Timer timer) {
        this.timer = timer;
    }

    public HelloTimerTask(){}

    @Override
    public void run() {
        //可能发生线程安全的代码放在同步块中  num初始化票=10
        synchronized (this){
            System.out.println("当前线程：" + Thread.currentThread().getName() + "定时任务：hello,world" + " ，当前num为：" + num);
            num --;
        }
        if (num <= 0){
            //结束定时任务的调度，执行该方法后，会让本次定时任务顺利走完，下一次不会再继续执行定时任务
            timer.cancel();
        }
    }
}

```

2.5 **public void schedule(TimerTask task, Date firstTime, long period);**

```java
    /**
     *
     */
    @RequestMapping("/test5.do")
    public void test5(){
        Timer timer = new Timer();
        //自定义某个时间
        LocalDateTime localDateTime = LocalDateTime.of(2020, 10, 22, 22, 17, 00);
        Date date = Date.from(localDateTime.atZone(ZoneId.systemDefault()).toInstant());
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        String format = sdf.format(date);
        System.out.println(format);
        /*
            2020-10-22 22:17:00
         */
        //达到上面的时间时，每2秒打一个helloWorld到控制台
        //arg1：定时任务类(将让定时任务执行的代码块写在run方法中)
        //arg2：自定义某个时间，Date类型
        //arg3：开始执行定时任务后每多久执行一次 （周期单位）
        timer.schedule(new HelloTimerTask(timer), date, 2000);
    }
```

#### 1.9.7、线程通信(生产者与消费者)

```java
package com.sph.practice.test.markdown.thread;

/**
 * Created by Shen Peihong on 2020/10/30 2:06
 * Description: 资源类提供一个Integer类型的数字，然后提供自增和自减方法，实现生产者与消费者模式
 *
 * @since 1.0.0
 */
//数据资源类
//生产者-消费者模式的套路：判断等待 -> 业务代码 -> 通知执行
public class Data {
    //数字
    private Integer num = 0;

    //自增 (防止发生线程安全问题，暂时先使用同步方法的方式)
    //线程安全：因为到时候是开启A和B线程去执行，线程中都是使用循环的方式去调自增和自减方法，可能A线程先抢到了资源，连续执行了2-3次，这种情况就会发生线程安全问题
    //综上，故需要上锁，这样一来，需要释放锁后，其它线程拿到锁了后才可以继续执行
    public synchronized void increment() throws InterruptedException {
        //当值不等于0时，执行等待操作，等待num=0时，被唤醒
        if (num != 0)
            wait();//进行等待操作，会等待被下一次唤醒
        num++;
        System.out.println(Thread.currentThread().getName() + "，已执行+1操作");
        //已实现自增，需要通知其它等待的线程
        notifyAll();
    }

    //自减
    public synchronized void decrement() throws InterruptedException {
        if (num == 0)
            wait();
        num--;
        System.out.println(Thread.currentThread().getName() + "，已执行-1操作");
        notifyAll();
    }
}
```

```java
package com.sph.practice.test.markdown.thread;

import org.junit.Test;

/**
 * Created by Shen Peihong on 2020/10/30 2:15
 * Description:
 *
 * @since 1.0.0
 */
public class DataTest {

    /**
     *
     */
    @Test
    public void test(){
        Data data = new Data();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    data.increment();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "A").start();

        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    data.decrement();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "B").start();
    }

}
```

![image-20201222174812223](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222174813.png)

//当需要线程通信时(判断等待(等待被唤醒)->业务代码->通知)，相关配套(因加锁有许多种方式，加锁方式不同的话，使用的通信工具也不同)   详情需看JDK8 API文档

#### 1.9.8、打印A、B、C问题

//如果使用sync...结合wait()、notifyAll()的话，无法做到精准通知某个监视器，而使用Lock结合Condition的话，Condition提供了监视器，可以做到精准通知

```java
package com.sph.practice.test.markdown.thread;

import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

/**
 * Created by Shen Peihong on 2020/10/30 2:54
 * Description: 打印A、B、C
 *
 * @since 1.0.0
 */
public class ABC {
    /*
        1、A打印完后，通知B打印
        2、B打印完后，通知C打印
        3、C打印完后，通知A打印
        //......依次循环数次
        方法思路：使用锁结合condition监视器对象(等待，准确通知的方式)

     */

    //创建可重入锁对象
    private Lock lock = new ReentrantLock();
    //创建condition监视器对象 (监视器对象里面提供了等待方法、通知方法(可准确通知某个监视器))
    //每个打印方法配备一个监视器对象，提供是否等待或被通知等操作
    private Condition condition1 = lock.newCondition();
    private Condition condition2 = lock.newCondition();
    private Condition condition3 = lock.newCondition();
    private Integer num = 1; //标志位 1A 2B 3C


    //封装打印A方法
    public void printA(){
        //使用Lock 结合Condition的等待、通知(可以准确唤醒某个condition监视器对象)
        try {
            lock.lock();
            //需要有个标志位，判断是否进入等待
            //使用while，而不使用if的原因是防止虚假唤醒：因为if只会执行一次，一旦线程执行完if后，就会卡在wait()方法，不会再判断条件了，所以需要使用while，循环判断
            while (num != 1){
                condition1.await();
            }
            System.out.println("打印AAA");
            num = 2;
            condition2.signal();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    //封装打印B方法
    public void printB(){
        //使用Lock 结合Condition的等待、通知(可以准确唤醒某个condition监视器对象)
        try {
            lock.lock();
            //需要有个标志位，判断是否进入等待
            //使用while，而不使用if的原因是防止虚假唤醒：因为if只会执行一次，一旦线程执行完if后，就会卡在wait()方法，不会再判断条件了，所以需要使用while，循环判断
            while (num != 2){
                condition2.await();
            }
            System.out.println("打印BBB");
            num = 3;
            condition3.signal();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    //封装打印C方法
    public void printC(){
        //使用Lock 结合Condition的等待、通知(可以准确唤醒某个condition监视器对象)
        try {
            lock.lock();
            //需要有个标志位，判断是否进入等待
            //使用while，而不使用if的原因是防止虚假唤醒：因为if只会执行一次，一旦线程执行完if后，就会卡在wait()方法，不会再判断条件了，所以需要使用while，循环判断
            while (num != 3){
                condition3.await();
            }
            System.out.println("打印CCC");
            num = 1;
            condition1.signal();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }



}
```

```java
package com.sph.practice.test.markdown.thread;

import org.junit.Test;

/**
 * Created by Shen Peihong on 2020/10/30 3:08
 * Description:
 *
 * @since 1.0.0
 */
public class ABCTest {
    //测试A->B->C 依次循环打印问题

    /**
     *
     */
    @Test
    public void test(){
        ABC abc = new ABC();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                abc.printA();
            }
        }, "A").start();

        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                abc.printB();
            }
        }, "B").start();

        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                abc.printC();
            }
        }, "C").start();
    }


}
```

#### 1.9.9、多线程相关注意事项

1、以前可能会创建一个线程类(实现Runnable接口)，然后直接在需要开启线程的地方去直接使用静态代理模式去开启线程，但是这种开启线程的方式违背了OOP思想，一个实体类/资源类去实现Runnable接口，无疑是增加了代码耦合度(不同模块之间的依赖程度越大，那么独立程度也越差)，所以我们如果想让一个线程去做一件事情的话，可以将操作的手段封装到一个方法里面，然后使用匿名内部类的形式去开启线程，线程执行的代码也就是调我们封装的方法。操作如下：

```java
package com.sph.practice.test.markdown.thread;

/**
 * Created by Shen Peihong on 2020/10/30 1:32
 * Description: 车票资源类，并且定义相关操作车票的方法
 *
 * @since 1.0.0
 */
//车票资源类
public class Ticket {

    private Integer ticket = 100;

    //封装卖票方法，执行卖票，每调一次，则卖票一张
    public void saleTicket(){
        if (ticket > 0){
            System.out.println(Thread.currentThread().getName() + "，买票成功," + "当前剩余票为" + --ticket);
        }
    }


}

```

```java
package com.sph.practice.test.markdown.thread;

import org.junit.Test;

/**
 * Created by Shen Peihong on 2020/10/30 1:36
 * Description: 卖票测试类
 *
 * @since 1.0.0
 */
public class TicketTest {

    /**
     * 开启线程卖票
     * 为什么要开启线程去卖票呢？为什么不能直接调卖票接口呢？
     * 因为可能卖票这个接口执行时间过长，使用线程就是为了异步处理，不会影响主流程的正常执行，故使用线程
     */
    @Test
    public void test1(){
        Ticket ticket = new Ticket();
        //使用匿名内部类的形式去开启线程
        //循环买票
        new Thread(() -> {
            for (int i = 0; i < 40; i++) {
                ticket.saleTicket();
            }
        }, "小牛").start();

        new Thread(() -> {
            for (int i = 0; i < 40; i++) {
                ticket.saleTicket();
            }
        }, "小花").start();

        new Thread(() -> {
            for (int i = 0; i < 40; i++) {
                ticket.saleTicket();
            }
        }, "小红").start();
    }

}

```

#### 1.9.10、Callable接口

//支持线程方法执行完毕后，有返回值

```java
    /**
     * public FutureTask(Callable<V> callable);
     *
     * @throws ExecutionException
     * @throws InterruptedException
     */
    @Test
    public void test1() throws ExecutionException, InterruptedException {
        //Thread的构造参数支持Runnbale接口，但是不支持Callable接口，所以需要找一些类实现了Runnbale接口，并且该类的构造方法或方法支持Callable接口的，
        //所以选用了FutureTask类(实现Callable接口，提供构造方法支持Callable类型
        FutureTask<String> futureTask = new FutureTask<>(new MyThread());
        new Thread(futureTask).start();
        new Thread(() -> {
            try {
                //等待返回太久了，使用异步处理
                //获取线程call()方法的返回值
                System.out.println(futureTask.get());
            } catch (Exception e) {
                e.printStackTrace();
            }
        }).start();
        System.out.println("上面使用异步处理，这里主线程就会先执行了");
        TimeUnit.SECONDS.sleep(5);
    }
```



#### 1.9.11、ThreadLocal

概念：在多线程情况下，用于给线程独自存储值用的。

Q：使用ThreadLocal去读写值与直接放局部变量的区别？

A：ThreadLocal里面的Map的Key可以理解成线程，线程对象死亡状态的话，那么会去清理掉，但是线程池一开始就根据最大核心数去初始化不死的线程对象了，所以正因为线程不死，故ThreadLocal里面的变量永远存在，所以更需要说一定要在finnaly块里面手动去remove掉，不然会导致出问题的。

```java
    /**
     * 开启两个线程，做相同的累加操作。分别存值与取值
     */
    @Test
    public void test1() throws InterruptedException {
        //两个线程 ，循环存值，然后存完取值，看看互相会不会影响到

        new Thread(() -> {
            try {
                for (int i = 0; i < 100; i++) {
                    //存值
                    THREAD_LOCAL.set(i);
                    //取值
                    System.out.println(Thread.currentThread().getName() + "：" + THREAD_LOCAL.get());
                }
            } finally {
                //THREAD_LOCAL里面的静态内部类Entry是弱引用，理论上该线程结束后，GC会自动回收
                //但一般我们自己手动清除
                THREAD_LOCAL.remove();
            }
        }, "shen").start();
        Thread.sleep(1000);

        new Thread(() -> {
            try {
                for (int i = 0; i < 100; i++) {
                    //存值
                    THREAD_LOCAL.set(i);
                    //取值
                    System.out.println(Thread.currentThread().getName() + "：" + THREAD_LOCAL.get());
                }
            } finally {
                //THREAD_LOCAL里面的静态内部类Entry是弱引用，理论上该线程结束后，GC会自动回收
                //但一般我们自己手动清除
                THREAD_LOCAL.remove();
            }
        }, "lin").start();
        Thread.sleep(3000);
    }
```



### 1.10、第三方类库

#### 1.10.1、谷歌工具类

**1、Lists**

```java
List<String> list = Lists.newArrayList(); //作用和new ArrayList<>();一样
```

```java
List<String> list1 = Lists.newArrayList("111", "222", "333"); //可初始化数据 该方法的参数可以初始化数组、list、Set、Iterator、数组列各种类型
```

```java
List<String> list = Lists.newArrayListWithCapacity(1); //使用这种去实例化list，设置容量大小，会自动扩容
```

```java
List<Integer> reverse = Lists.reverse(list); //将list反序
```

```java
List<String> transform = Lists.transform(list, UserParam::getUsernmae);//用法和stream流的map()转换一样
```

```java
List<List<String>> partition = Lists.partition(list, 3); //将list去切割，每份是3
```

**2、Sets**

```java
Set<String> set = Sets.newHashSet(); //初始化，和使用new HashSet<>()方式一样  方法的参数和list一样支持各种类型的
```

```java
Set<String> filter = Sets.filter(set, e -> e.equals("111"));//Predicate过滤，和stream流的filter作用一样
```

```java
Sets.SetView<String> union = Sets.union(set, set1); //求两个集合并集
```

```java
Sets.SetView<String> intersection = Sets.intersection(set, set1); //求两个集合的交集
```

```java
Sets.SetView<String> difference = Sets.difference(big, small); //求补集 求arg1的补集，也即是arg1中-arg2中相同的 剩余的就是arg1的补集
```

````java
Set<Set<String>> combinations = Sets.combinations(set, 2);//将Set中的元素组合 C 参数1.size()取参数2的数字 比如这里的set集合是3，那么就是C3取2
````

```java
Set<List<String>> cartesianProduct = Sets.cartesianProduct(set, set1); //求两个集合的笛卡尔乘积
```

**3、Maps**

```java
Map<String, Object> map = Maps.newHashMap(map1); //初始化Map
```



#### 1.10.2、HttpClient





### 1.11、Lombok

- @Getter：给变量生成get方法
- @Setter：给非final变量生成set方法
- @ToString：给实体类生成toString方法
- @NoArgsConstrctor：给实体类生成空参构造方法

- AllArgsContructor：给实体类生成全参构造方法
- @RequiredArgsConstructor：将标记成@NotNull的属性生成一个构造器
- @NotNull：如果运行中标记为@NotNull的属性为null，会抛出空指针异常
- **@Data**：@Data = @Setter + @Getter + @EqualsAndHashCode + @RequiredArgsConstructor + @ToString

- @SakyThrows：本质是catch到异常后外抛



### 1.12、validation

作用概述：使用注解校验参数的合法性

使用方式：支持直接在形参或实体类使用（切记形参处一定要加上@Valid注解声明开启）

详解：

@NotNull：作用于基本类型

@NotEmpty：

@NotBlank：



### 1.13、外部类与内部类

内部类可以访问







## 2、javaEE

### 2.1、tomcat相关

#### 2.1.1、简述slf4j

**1. 使用方式：**

1. Java代码使用LOGGER对象

```java
private static final transient Logger LOGGER = LoggerFactory.getLogger(TimedRemindResitTask.class);
```

2. 注解使用LOGGER对象

````jav
@Slf4j //注解标在类上方即可使用
````

**2. 简要记录一下日志级别：**

每个Logger又分了一个日志级别，用来控制日志信息的输出。日志级别从高到低分为：

A：off //最高等级，用于关闭所有日志记录。

B：fatal //指出每个严重的错误事件将会导致应用程序的退出。

C：error //指出虽然发生错误事件，但仍然不影响系统的继续运行。

D：warm //表明会出现潜在的错误情形。

E：info //一般和在粗粒度级别上，强调应用程序的运行全程。

F：debug //一般用于细粒度级别上，对调试应用程序非常有帮助。

G：all //最低等级，用于打开所有日志记录

//当输出时，只有级别高过配置中规定的级别的信息才能真正的输出，这样就很方便的来配置不同情况下要输出的内容，而不需要更改代码（单机项目配置使用debug，上线项目配置使用Info）



#### 2.2.2、HttpServletRequest

特点：该请求对象封装了用户的相关信息。

1. request.getParameter("username");

```java
//请求地址：http://localhost:8085/shop/request/test1.do?username=zhangsan
//1、获取请求参数
String username = request.getParameter("username");
System.out.println("username" + " ： " + username);
//username ： zhangsan
```

2. request.getScheme();

```java
//请求地址：http://localhost:8085/shop/request/test1.do?username=zhangsan
//2、获取请求的协议
String scheme = request.getScheme();
System.out.println("scheme" + " ： " + scheme);
//scheme ： http
```

3. request.getContextPath();

```java
//请求地址：http://localhost:8085/shop/request/test1.do?username=zhangsan
//3、获取项目前缀
String contextPath = request.getContextPath();
System.out.println("contextPath" + " ： " + contextPath);
//contextPath ： /shop
```

4. request.getServerName();

```java
//请求地址：http://localhost:8085/shop/request/test1.do?username=zhangsan
//4、获取IP地址、或域名
String serverName = request.getServerName();
System.out.println("serverName" + " ： " + serverName);
//serverName ： localhost
```

5. request.getServletPath();

```java
//请求地址：http://localhost:8085/shop/request/test1.do?username=zhangsan
//5、获取地址后缀(从项目前缀后开始)
String servletPath = request.getServletPath();
System.out.println("servletPath" + " ： " + servletPath);
//servletPath ： /request/test1.do
```

6. request.getRequestURL();

```java
//请求地址：http://localhost:8085/shop/request/test1.do?username=zhangsan
//6、获取请求URL
StringBuffer requestURL = request.getRequestURL();//获取请求URL
System.out.println("requestURL" + " ： " + requestURL);
//requestURL ： http://localhost:8085/shop/request/test1.do
```

7. request.getHeader("User-Agent");

```java
//请求地址：http://localhost:8085/shop/request/test1.do?username=zhangsan
//7、获得头部的某个属性
String userAgent = request.getHeader("User-Agent");
System.out.println("userAgent" + " ： " + userAgent);
//userAgent ： PostmanRuntime/7.26.5
```

8. request.getParameterMap();

```java
//请求地址：http://localhost:8085/shop/request/test1.do?username=zhangsan
//8、获得参数(大多用于复选框)
Map<String, String[]> parameterMap = request.getParameterMap();
for (Map.Entry<String, String[]> entry : parameterMap.entrySet()) {
    System.out.println("参数key：" + entry.getKey());
    System.out.println("key对应的value为：");
    for (String str : Lists.newArrayList(entry.getValue())) {
        System.out.println(str);
    }
    System.out.println("---------------分割线---------");
}
//由于复选框、或表单数据的name值相同的话，后台参数需要使用数组接收，不能使用getParameter()这个方法去接收呢，下面介绍一个API，可以接收所有前端的请求参数（Map类型）
```

9. request.getSession()

#### 2.2.3、HttpServletResponse

1. void addCookie(Cookie var1);

```java
//新建Cookie对象
Cookie cookie = new Cookie("sessionToken1", "666");
//设置cookie过期时间
cookie.setMaxAge(66);
//返回cookie对象给前端浏览器
response.addCookie(cookie);
```

2. void sendRedirect(String var1) throws IOException;

```java
//重定向到其它URL (支持拼前面的域名，也支持不拼，不拼的话会默认本机)
response.sendRedirect(request.getContextPath() + "/request/test1.do");
//也支持跳第三方URL
response.sendRedirect("https://www.taobao.com");
```

3. void setHeader(String var1, String var2);

```java
String encode = "encode";
//返回excel需要指定下载的文件名
response.setHeader("Content-disposition", "attachment; filename=\"" + encode + ".xls" + "\"");
//声明返回的类型
response.setHeader("ContentType", "application/msexcel");
```

#### 2.2.4、HttpSession

1. session经常组合使用的方法

```java
//设置session属性
session.setAttribute("wxqyhUserId", UUID.randomUUID().toString().replaceAll("-", ""));
//设置session过期时间
session.setMaxInactiveInterval(1);
//获取session属性
String wxqyhUserId = (String) session.getAttribute("wxqyhUserId");
System.out.println(wxqyhUserId);
/*
    737fb3e4961646d3a7bc406340e84e97
 */
//删除某个session的key/value键值对
session.removeAttribute("wxqyhUserId");
```

#### 2.2.5、Cookie

1. Cookie的常规使用

```java
//新建Cookie对象
Cookie cookie = new Cookie("sessionToken1", "666");
//设置cookie过期时间
cookie.setMaxAge(66);
```



### 2.2、Servlet技术

![image-20201222174910451](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222174912.png)



### 2.3、EL表达式

在前端页面插入Java语言<%  ...  %>
EL表达式不但可以取值，而且里面还可以写比较表达式、算数表达式、判断值是否为空
jsp有四大域对象：page、request、session、application
可以往四大域对象中放置值， request.setAttribute("key",value);  //value可以放置任何类型的值。key需要是字符串类型，到时候直接通过key取值
在前端页面取域对象的值可以使用EL表达式  ：  ${  } //可以前面输入scope域.key值    ${requestScope.number} 可简写成${number}
但是一般前面的scope域可以省略，到时候会按域对象的优先级去取值 request>session>application
关于域对象赋的值是不同类型的不同取法：
1.简单的变量
Java代码：

```jsp
<%
	request.setAttribute("number",1);
%>
```

前端代码：

```jsp
${number}
总结：当值是可以以简单的变量定义时，访问直接${number}即可
```

2.类类型
Java代码：

```jsp
<%
          User user = new User();
          user.setId(1);
          user.setUsername("123");
          request.setAttribute("user", user);
%>
```

前端代码：

```jsp
${user.id} <br/>
```



总结：当el表达式获取request域中类类型的话，那么使用 key名.属性名(前提是该类需要有get方法  相当于调用get方法去获取值呢)

3.Map类型
Java代码：

```jsp
<%
        Map<String,Object> map = new HashMap<String,Object>();
        map.put("title", 234);
        map.put("杜甫", 333);
        request.setAttribute("a", map);
%>
```

前端代码：

```jsp
${a.杜甫} <br/>
```

总结：当el表达式获取到request域中是map类型的时候，直接使用${request中的key名.集合的key名} ，${request域中的key名} //获取到的是该key对应的value
    当value是类类型，则获取某个属性使用 .属性名。
    当value是Map类型，则.key名

4.List类型
Java代码：

```jsp
<%
          List<User> list = new ArrayList<User>();
          User user = new User();
          user.setId(1);
          user.setUsername("123");
          user.setPassword("456");
          list.add(user);
          request.setAttribute("keyName",list);
%>
```

前端代码：

```jsp
${keyName[0].username} <br/>
```

总结：当存放在request域中是List类型，则我们${request域中的key}获取到的是一整个key对应的List



5.数组类型
Java代码：

```jsp
<%
          int s[] ={1,2,3};
          request.setAttribute("name", s);
%>
```

前端代码：

```jsp
${name[0]}
```

总结：当request域存放的是数组类型的话，那么 ${}获取到的是一个数组



### 2.4、JSTL表达式

.jsp中支持三种标签：1、HTML标签 2、JSP内置标签 3、自定义标签（比如引入jstl标签(人家写好了的标签库)）
使用别人自定义标签：1、导入JAR包  2、在对应的.jsp中引入标签库
使用别人写的框架：1、导入Jar包 2、在对应的.xml引入约束文档

```jsp
<%@ taglib  uri="http://java.sun.com/jsp/jstl/core"  prefix="c" %>  //prefix是前缀 类似jsp:
<%@ taglib uri="http://java.sun.com/jsp/jstl/fmt" prefix="fmt"%>
```

1、<c:set>标签  设置值标签
el表达式只能够取域中的值 但是jstl表达式可以往域对象中存值

```jsp
<c:set var="username" value="123" scope="request"></c:set>
${username }
```

var是属性名 value是属性值 scope是存到哪个域中，该前端语句相当于后台的：

```java
request.setAttribute("username","123");
```



2、<c:out>标签 取值标签

```jsp
<c:out value="${username }"></c:out>
```

输出域对象的值，等价于${username} 所以一般很少用<c:out>标签



3、<c:remove>标签 移除值标签

```jsp
<c:remove var="username"/>
```

把属性从域对象中移除掉



4、<c:if>标签 if标签

```jsp
<c:set var="age" value="18" scope="request"></c:set>
<c:if test="${age >=18 }">
          <font color="green">你已成年了！</font>
</c:if>
```

使用if标签 test是条件表达式 取值直接在里面做比较即可 因为el表达式也支持比较表达式



5、<c:choose>标签 if/esle标签

```jsp
<c:set var="age" value="18" scope="request"></c:set>
<c:choose>
          <c:when test="${age>=18 }">
                     <font color="green">你已成年！</font>
          </c:when>
          <c:otherwise>
                     <font color="red">你未成年！</font>
          </c:otherwise>
</c:choose>
```

if/else标签 if标签用<c:when> else标签用<c:otherwise> 但是都必须包含在<c:choose>这个根标签当中



6、<c:forEach>标签 循环标签

```jsp
<c:set var="age" value="18" scope="request"></c:set>
<c:forEach begin="1" end="10" var="i">
          ${age}
</c:forEach>
```

begin属性：开始索引 end属性：结束索引 var：初始指向begin索引然后遍历到end索引



```jsp
<%
          List<String> list = new ArrayList<String>();
          list.add("123");
          list.add("789");
          request.setAttribute("list", list);
%>
<c:forEach items="${list }" var="i">
          ${i }
</c:forEach>
```

当域里面存放的是List列表的话，那么<c:forEache>标签里面用属性 items接收一整个列表 然后 var索引指向第一个元素



### 2.5、XML配置文件

XML是可扩展标记语言，而HTML文件时不可扩展标记语言

XML文件：是可扩展标记语言，也即是标签我们可以自定义   书写XML里面的标签 大小写敏感 区分大小写的呢 标签成对出现 XML标签里面的属性值也是我们人工定义的 所
有子标签都需要有一个根标签
**使用别人的框架需要导入包(引入jar包 才能在类中使用包里面的类和方法和注解)和在XML文件中引入约束文档(引入约束文档才能使用框架提供的标签)**

```xml
<?xml version="1.0" encoding="UTF-8"?>  //文档声明要写在第一句 不然编译器不通过
```

XML文件书写标签和语法是非常随便的 几乎是自定义的
但是别人在制作框架的时候，会对XML的一些标签元素做约束，所以比如在引入springmvc框架，写springmvc.xml的时候，需要在XML文件引入约束文档
我们想要引入一个框架的约束文档到XML有两种方式：直接下载约束文档导入，但是得在beans中引入地址



### 2.6、约束文档

DTD(引入.dtd文件)、schema(引入.xsd文件) (引入约束文档后 才可以用一些标签，并且一些标签还规定你一定要使用什么属性)



### 2.7、Listener监听器

![image-20201222174951448](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222174953.png)

### 2.8、Filter过滤器

（和拦截器效果差不多，只不过他们底层实现的方式不同，但是都是过滤请求的，然后再去决定是否放行，或者是其它作用，过滤器在外层，过滤器是tomcat启动的时候就一直生存着，基于函数回调，而拦截器是基于反射实现的。）

![image-20201222175032986](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222175034.png)

### 2.9、Interceptor拦截器





## 3、其它

### 3.1、请求与响应

#### 3.1.1、json数据

Json概念：采用"键：值"对的文本格式来存储和表示数据

JSON对象的语法：一对花括号代表一个JSON对象

1. **json对象**

```json
{
    "ID"：1001,
    "name"："张三"
    "age"：24
}
```

2. **json对象数组**

```json
{
    "部门名称":"研发部",
    "部门成员":[
    {"ID":"1001","name":"张三","age":24},
    {"ID":"1002","name":"李四","age":25},
    {"ID":"1003","name":"王五","age":22}
    ],
    "部门位置":"大楼21号"
}
```

//value支持json对象数组

3. **json字符串**

总结：JSON可以简单的分为基本形式：基本类型、字符串、JSON对象、JSON对象数组



4. **fastjson的使用**

4.1、PO/VO类和 JSON字符串互相转换（对象）

序列化成json对象

```java
String str = JSON.toJSONString(form);
JSONObject jsonObject = JSON.parseObject(str);
```

反序列化成java对象

```java
String str = JSON.toJSONString(form);
TestPO testPO= JSON.parseObject(str,TestPO.class);
//上面的操作基本不需要了哈，因为只需要将参数序列化成java对象即可，使用@RequestBody这个注解即可
```

4.2、List<PO/VO>类和JSON字符串互相转换（数组）

序列化成json数组对象

```java
String stringJson = JSON.toJSONString(test);
JSONArray jsonArr = JSON.parseArray(stringJson);
```

反序列化成java数组对象

```java
String stringJson = JSON.toJSONString(test);
List<TestPO> arr = JSON.parseArray(stringJson,TestPO.class);
```



#### 3.1.2、序列化与反序列化

前言：我们知道，当两个进程进行远程通信时，可以相互发送各种类型的数据，包括文本、图片、音频、视频等，而这些数据都会以二进制序列的形式在网络上传送。那么当两个Java进程进行通信时，是否实现进程间的对象传送呢？答案是可以的！如何做到呢？这就需要Java序列化与反序列化了！

Q：序列化成字节序列的好处？

- 永久性保存对象，保存对象的字节序列到本地文件或者数据库中（因为保存在硬盘的数据都是二进制数据 一个字节=8个二进制位）

- 通过序列化以字节流的形式使对象在网络中进行传递和接收

- 通过序列化在进程间传递对象



1. **Java是如何序列化与反序列化的？（序列化是一种方式，而实现这种方式的工具可以用JDK的IO流接口）**

1.1 java.io.ObjectOutputStream：表示对象输出流

它的writeObject(Object obj)方法可以对参数指定的Obj对象进行序列化，把得到的字节序列写到一个目标输出流中

1.2 java.io.ObjectInputStream：表示对象输入流

它的readObject()方法输入流中读取字节序列，再把它们反序列化成为一个对象，并将其返回



2. **实现序列化的要求**

只有实现了Serializable或Externalizable接口的类的对象才能被序列化，否则抛出异常！（因为支持序列化的基础方法里面会进行instanceof Serializable判断，去调用合适的基础方法，不然的话默认只是支持基本类型，想要支持对象序列化的话，那么该对象一定要实现Serializable接口）



3. **实现Java对象序列化与反序列化的方法**

假定一个User类，它的对象需要序列化，可以有如下方法：

若User类仅仅实现了Serializable接口，则可以按照以下方式进行序列化和反序列化

- ObjectOutputStream采用默认的序列化方式，对User对象的非transient的实例变量进行序列化。

- ObjectInputStream采用默认的反序列化方式，对User对象的非transient的实例变量进行反序列化



4. **JDK类库中序列化的步骤**

步骤一：创建一个对象输出流，它可以包装一个其它类型的目标输出流，如文件输出流：

ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("d:\\temp\\a.txt"));

步骤二：通过对象输出流的writeObject方法写对象：

oos.writeObject(new User("xulingen","123456"));

oos.flush();

oos.close();



5. **JDK类库中反序列化的步骤**

步骤一：创建一个对象输入流，它可以包装一个其它类型输入流，如文件输入流

ObjectInputStream ois = new ObjectInputStream(new FileInputStream("d:\\temp\\a.txt"));

步骤二：通过对象输入流的readObject()方法读取对象：

User user = (User)ois.readObject();

//注：为了正确读取数据，完成反序列化，必须保证向对象输出流写对象的类型与从对象输入流中读对象的类型一致



6. **序列化相关注意事项**

5.1 序列化时，只对对象的状态进行保存，而不管对象的方法

5.2 当一个父类实现序列化，子类自动实现序列化，不需要显式实现Serializable接口

5.3 当一个对象的实例变量引用其他对象，序列化该对象时也把引用对象进行序列化

5.4 并非所有的对象都可以序列化，至于为什么不可以，有很多原因了，比如：

- 安全方面的原因，比如一个对象拥有private，public等field，对于一个要传输的独享，比如写到文件，或者进行RMI传输等等，在序列化进行传输的过程中，这个对象的private等域是不受保护的。

- 资源分配方面的原因，比如socket，thread类，如果可以序列化，进行传输或者保存，也无法对他们进行重新的资源分配，而且，也是没有必要这样实现

5.5 声明为static和transient类型的成员数据不能被序列化，因为static代表类的状态，transient代表对象的临时数据

5.6 序列化运行时使用一个称为serialVersionUID的版本号与每个可序列化类相关联，该序列号和反序列化过程中用于验证序列化对象的发送者和接受者是否为该对象加载了与序列化兼容的类。为它赋予明确的值。显式地定义serialVersionUID有两种用途：

- 在某些场合，希望类的不同版本对序列化兼容，因此需要确保类的不同版本具有相同的serialVersionUID。

- 在某些场合，不希望类的不同版本对序列化兼容，因此需要确保类的不同版本具有不同的serialVersionUID

5.7 Java有很多基础类已经实现了serializable接口，比如String，Vector等。但是也有一些没有实现serializable接口的。

5.8 如果一个对象的成员变量是一个对象，那么这个对象的数据成员也会被保存！这是能用序列化解决深拷贝的重要原因



7. **为何PO或VO或DTO对象需要标明成Serializable？**

想法：

- 一台服务器往另一台服务器的硬盘中存东西

- 一台服务器往另一台服务器发送请求(携带数据参数)

- 一台服务器往缓存服务器存数据



最原始的数据传输的格式：

- IO流



因为我们可能直接地就将对象放入缓存或进行Socket远程调用，又或者是想写入到某个硬盘中，那么我们需要将对象先转换成字节数组，因为在传输中需要转换成数组才能传数据，而两个端传数据必定需要有管道，IO流、或远程调用，放入流中传输

![image-20201222175105931](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222175107.png)



#### 3.1.3、内网穿透

用途：内网别人无法访问，将内网映射成外网给他人访问

操作步骤：

1. 下载内网穿透软件（natapp、花生壳） 在此推荐natapp 免费的

2. 上natapp的官网去购买隧道（购买免费的隧道）

3. 隧道协议选择Web 端口号是80 因为我们的网页启动的时候是用http协议

4. 购买完隧道(登录后查看我的隧道的authtoken )，同时也下载完natapp后，打开natapp.exe

5. 进入cmd 输入 natapp -authtoken= 粘贴我们的免费隧道的authtoken

则Forwarding的地址就是我们的内网映射成外网的地址

![image-20201222175127303](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222175130.png)

![image-20201222175140888](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222175142.png)



#### 3.1.4、富文本和纯文本区别

区别：

- 纯文本无法设置大小、样式、颜色 (txt写)

- 富文本可以设置大小、样式、颜色（doc、写字板写）

//富文本许多文本标签都是经过转义的，比如空格 等于 &nbsp   < 等于 &lt 

//注：富文本框中对每段换行文字都会自动追加<p>标签进行包裹。



#### 3.1.5、正则表达式

给定一个正则表达式和另一个字符串，我们可以达到如下的目的：

- 给定的字符串是否符合正则表达式的过滤逻辑（称作“匹配”）

- 可以通过正则表达式，从字符串中获取我们想要的特定部分

```java
    /**
     * Pattern和Matcher结合使用
     */
    @Test
    public void test1(){
        //这里可以使用各种正则表达式
        String regex = "java";
        String test = "eejavaeejavaee";
        //新建匹配规则对象
        Pattern pattern = Pattern.compile(regex);
        //匹配结果对象
        Matcher matcher = pattern.matcher(test);
        //将匹配到的字符串全替换
        String str = matcher.replaceAll("");
        System.out.println(str);
        /*
            eeeeee
         */
        //若找到与匹配规则相同的话
        if (matcher.find()){
            System.out.println("已匹配上");
        }
    }
```

#### 3.1.6、Jsoup爬虫

**1、Jsoup**

作用：解析HTML代码







#### 3.1.7、散列函数(哈希)

概念：输入任意长的输入字符串，都会输出成固定长度的输出字符串的一种函数

应用：目前最广泛的hash函数是SHA-1和MD5。



### 3.2、资源池

#### 3.2.1、jdbc连接池

DBCP、C3P0、Druid、DBPool //要和数据库交互，首先需要建立连接池，并且连接池里面需要有连接数据库对象（查询对象、增删改对象） 

使用与不使用连接池的区别：

- **原始JDBC操作数据库步骤**：1、注册数据库驱动 2、建立连接数据库对象，使用完增删改查后，释放连接 

- **使用数据库连接池(在原始JDBC进一步封装)**：使用连接池，连接池在tomcat/jetty容器启动时，初始化时存放许多的连接数据库对象，减少创建和释放的操作，减少CPU的操作，提高性能。



#### 3.2.2、线程池

1、使用ThreadPoolExecutor类去实例化线程池对象，自定义参数，不要使用Exceotors工具类提供的初始化线程池对象

```java
package com.sph.practice.test.markdown.threadpool;

import java.util.concurrent.*;
import java.util.concurrent.atomic.AtomicInteger;

/**
 * Created by Shen Peihong on 2020/10/29 17:14
 * Description:封装线程池对象方法
 *
 * @since 1.0.0
 */
public class ThreadPoolUtil {
    //核心线程数
    private int corePoolSize;
    //最大线程数 (正式工+临时工)
    private int maximumPoolSize;
    //排队空闲线程的存活时间，到点未转成任务线程的话会被拒绝策略所拒绝
    private long keepAliveTime;
    //上面参数的单位
    private TimeUnit unit;
    //队列
    private BlockingQueue<Runnable> workQueue;
    //队列长度
    private int queueSize;
    //线程工厂
    private ThreadFactory threadFactory;

    //提供线程池对象实例
    //默认创建线程池
    public ThreadPoolExecutor getInstance() {
        corePoolSize = 10;
        maximumPoolSize = 100;
        keepAliveTime = 3;
        unit = TimeUnit.SECONDS;
        queueSize = 1000;
        workQueue = new ArrayBlockingQueue<Runnable>(queueSize); //实现类已经去实现完接口定义的方法了
        threadFactory = new ThreadFactory() {
            private AtomicInteger currentNum = new AtomicInteger();

            @Override
            public Thread newThread(Runnable r) {
                System.out.println("新创建线程成功,当前序号为：" + currentNum.getAndIncrement());
                return new Thread(r);
            }
        };
        return new ThreadPoolExecutor(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue, threadFactory);
    }

}

```

1、Runnable线程类

```java
    @Test
    public void test1() throws InterruptedException {
        ThreadPoolExecutor instance = new ThreadPoolUtil().getInstance();//创建线程池对象
        for (int i = 0; i < 10; i++) {
            ThreadTest thread = new ThreadTest();
            instance.execute(thread);//本次线程代码执行完毕
        }
        TimeUnit.SECONDS.sleep(10);
    }
```

2、Callable线程类

```java
    @Test
    public void test2() throws InterruptedException, ExecutionException {
        ThreadPoolExecutor instance = new ThreadPoolUtil().getInstance();//创建线程池对象
        for (int i = 0; i < 10; i++) {
            CallableTest thread = new CallableTest();
            //支持返回值的线程只支持实现Callable接口呢
            Future<Integer> submit = instance.submit(thread); //每个线程交给线程池去跑完后，会返回返回值
            TimeUnit.SECONDS.sleep(1);
        }
        TimeUnit.SECONDS.sleep(10);
    }
```



#### 3.2.3、缓存池



#### 3.2.4、对象池



### 3.3、常见Error分析

#### 3.3.1、StackOverFlowError

//栈溢出 压栈压得太深了 

可能出现的原因：

- 函数递归调用层次过深：每调用一次，函数的参数、局部变量等信息就压一次栈，并且没有及时出栈(局部变量压栈) 

- 局部变量体积过大 

解决方案：

- 把递归调用函数改用while或者for循环来实现

- 改用堆内存或静态区，函数里定义很大的局部变量(例如大数组)

- 增加栈的大小值

#### 3.3.2、OutOfMemoryError 

//(内存泄露) java虚拟机内存空间不够 

可能出现的原因：

- 引入大量的jar包或class

- 堆的对象太多了 

- java线程太多 



### 3.4、Maven

#### 3.4.1、pom.xml

pom.xml文件包含四大属性：groupID、artifactID、version、packaging



#### 3.4.2、常用的命令

```bash
mvc clean compile：将项目编译  运行完这条语句后会在target目录下生成classes文件   
mvc clean package：打包操作  运行完这条语句会在target目录下生成jar包
mvn clean install：将本项目封装成jar包，然后安装到本地仓库，供其它项目使用(pom.xml文件导入)
```



