# 一、Nginx

## 1、Nginx常识

### 1.1、Nginx基本概念

```txt
（1）nginx是什么，做什么事情
（2）反向代理
（3）负载均衡
（4）动静分离
```



### 1.2、nginx安装

在linux安装nginx：1.远程登陆服务器 2.安装pcre、openssl、zlib 这三个相关nginx依赖
安装完nginx后，会在对应的目录下生成local文件下，local文件夹下的nginx就是我们的nginx了。
**注：**nginx安装完毕后，默认是运行在80端口的(我们可以尝试使用ip:nginx的端口访问nginx反向代理服务器)  //要记得需要打开防火墙并且有配置该端口
**查看防火墙开放的端口号：**firewall-cmd --list-all
**设置防火墙开放端口：**sudo firewall-cmd --add-port=8001/tcp --permanent（设置完要重启防火墙 firewall-cmd --reload）



## 2、Nginx基础

### 2.1、nginx常用命令

//使用nginx命令前提是要进入/local/nginx/sbin目录下(存在一个绿色的nginx 可以使用./nginx执行)才可以执行nginx命令)  // linux中./就表示执行当前目录下的某个文件，比如当前目录又a.sh 使用./a.sh 即执行a脚本

查看nginx的版本号：./nginx -v
启动nginx：./nginx
关闭nginx：./nginx -s stop
**改完nginx.conf文件后，先检查语法，语法没问题再reload：./nginx -t**
**重新加载nginx(更改了nginx的配置文件后需要重新加载)：./nginx -s reload**



### 2.2、nginx配置文件组成

**i：全局块**
worker_processes = 1 值越大，可以支持的并发处理量也越多
**ii：events块**
events块涉及的指令主要影响Nginx服务器与用户的网络连接
worker_connections 1024; 支持的最大连接数(客户访问)
**iii：http块 = http全局块 + server块（多个）**
http全局块：包括文件引入、MIME-TYPE定义、日志自定义、连接超时时间、单链接请求数上限等
server块 = server全局块 + location（多个）



### 2.3、反向代理

（1）正向代理：例如 浏览器访问谷歌浏览器需要先配置代理服务器，由代理服务器去帮助我们访问谷歌浏览器
（2）反向代理：由Nginx去选择哪一台tomcat去执行请求，暴露的是反向代理nginx服务器的地址，隐藏了真实服务器IP地址

![image-20201222192741322](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222192742.png)

**反向代理实例：**
1、实现效果
（1）打开浏览器，在浏览器地址栏输入地址 www.123.com，跳转到linux系统tomcat主页面中

2、准备工作
（1）在linux系统安装tomcat，使用默认端口8080

- tomcat 安装文件放到linux系统中，解压
- 进入tomcat的bin目录中，./startup.sh启动tomcat服务器

（2）对外开放访问的端口（防火墙要添加8080端口 凡是安装的软件有端口问题，都需要到防火墙添加开放的端口）
        firewall-cmd --add-port=8080/tcp --permanent //防火墙开放8080端口
        firewall-cmd --reload //重启防火墙
        firewall-cmd --list-all //查看已经开放的端口号
（3）
**反向代理进阶实例：**根据不同的请求路径前缀去转发到不同的tomcat
   **前提：**准备两个tomcat服务器，8080、8081
具体配置

- 找到nginx配置文件，再配置进阶反向代理

![image-20201222192757028](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222192803.png)



**//这里开始详细解释location 后面的匹配规则**
3.1、 = 匹配
location = / {
    #精准匹配，主机名后面不能带任何的字符串
    proxy_pass http://192.168.199.109:8080/;
}

3.2、 通用匹配
location / {
    #通用匹配，主机名后面可以带任何的字符串
    proxy_pass http://192.168.199.109:8080/; #进行请求转发
}


location /xxx {
    #匹配所有以/xxx开头的路径
    proxy_pass http://192.168.199.109:8080/; #进行请求转发
}

3.3、正则匹配
location ~/xxx/ {
    #带~波浪线开头的是正则表达式匹配，现在这个是匹配开头，和上面通用匹配作用一样，但是这个优先级更高
    proxy_pass http://192.168.199.109:8080/; #进行请求转发
}

3.4、匹配开头路径
location ^~/xxx/ {
    #异或+波浪线也是匹配前缀，这个和通用匹配和正则匹配想过一样，但是这个优先级是最高的
    proxy_pass http://192.168.199.109:8080/; #进行请求转发
}
前缀匹配的优先级顺序：^~ 》 ~ 》 /

3.5、匹配后缀路径
// ~* 是后缀匹配
location ~* \.(gif|jpg|png)$ {
    #匹配以gif或者jpg或者png为结尾的路径
}
**//注：location是否以"/"结尾**
在nginx中location进行的是模糊匹配
注1、没有"/"结尾时，location /abc/def可以匹配/abc/defghi请求，也可以匹配/abc/def/ghi等
注2、而有"/"结尾时，location/abc/def不能匹配/abc/defghi请求，只能匹配/abc/def/anything这样的请求

**//注：proxy_pass是否以"/"结尾**



### 2.4、负载均衡
因为追求高并发，所以需要使用集群（多台服务器做一个集群），做集群是为了负载均衡（给服务器减压）
nginx负载均衡配置：
http内配置：
upstream myserver{
    server 192.168.17.129:8080 weight=5; //外部配置映射的tomcat路径 到时候配置location的时候，直接作为proxy_pass的value值
    server 192.168.17.129:8081 weight=10;
}

server{
    listen    80;//nginx监听的端口号
    server_name    192.168.17.129；//nginx服务器的IP地址

​	location / {

​		proxy_pass：http://myserver; //引用外部配置的IP地址(指向两台tomcat) 实现负载均衡

​	}

}



### 2.5、动静分离

![image-20201222192848079](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222192849.png)

**动静分离方法有两种实现方式：（简单理解成：Nginx处理静态页面、Tomcat处理动态页面）**
5.1：纯粹把静态文件独立成单独的域名，放在独立的服务器上

![image-20201222192901167](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222192902.png)

具体配置如下：

![image-20201222192909551](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222192910.png)

5.2：动态跟静态文件混合在一起发布，通过nginx来分开
通过location指定不通的后缀名（.action、.do、.js、.html、.css）实现不同的请求转发。通过expires参数设置，设置浏览器缓存过期时间。这种方式的请求状态码为304



### 2.6、nginx高可用集群

![image-20201222192922978](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222192923.png)

//安装keepalived在usr目录下，安装完成后，在etc生成了keepalived目录，里面有keppalived.conf目录的配置文件

![image-20201222192936715](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222192937.png)



### 2.7、nginx分发策略

- 轮询（默认）：每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除
- weight：weight越大，权重越大
- ip_hash：每个请求按访问Ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session问题
- fair：根据后端服务器的响应时间来决定用哪个tomcat



### 2.8、nginx反向代理缓存

```nginx
http{
    # 缓存路径 /data/nginx/cache 缓存结构为2层，即该路径下会有2层子目录，缓存文件会保存在最下层子目录    
    # 缓存的key会保存在名为web_cache的内存区域，该内存区域大小为50m
    # 10分钟内缓存没有被访问就会过期
    # 缓存文件最多占用1g空间
    proxy_cache_path /data/nginx/cache levels=1:2 keys_zone=web_cache:50m inactive=10m max_size=1g;

    # proxy_cache_path 指令必须在include指令之前
    include vhosts/*.conf;

    接下来在server小节的location /小节添加如下配置
    location / {
    
        proxy_store off;
        proxy_redirect off;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header Host $http_host;
        proxy_pass http://my-server/;
        # 缓存使用前面定义的内存区域
        proxy_cache web_cache;
        # 这个貌似没啥用
        proxy_ignore_headers   Expires Set-Cookie;
        # 对于 200 和 304 的响应码进行缓存，过期时间为 2 分钟，这会覆盖前面定义的 10 分钟过期时间
        proxy_cache_valid 200 304 2m;
        # 设置缓存的 key，这里用到了 nginx 的内嵌变量，表示用整个 url 作 key
        proxy_cache_key  $scheme$proxy_host$request_uri;
        # 在返回的响应里添加响应头 X-Proxy-Cache，其值表示是否命中了缓存，貌似没啥用
        add_header X-Proxy-Cache $upstream_cache_status;
        # 返回的响应头里，设置过期时间为 10 分，貌似没啥用
        expires 10m;
        # 貌似没啥用
        add_header Access-Control-Allow-Origin "http://myserver.net";
        # 貌似没啥用
        add_header Access-Control-Allow-Credentials true;

    }

}
```



### 2.9、nginx静态文件缓存

#### 2.9.1、兼容http、server、location下的配置

```nginx
    #跟后端服务器连接的超时时间_发起握手等候响应超时时间 默认60s （上下文：http server location）
	proxy_connect_timeout 60;

 *  #连接成功后_等候后端服务器响应的时间_其实已经进入后端的排队之中等候处理 默认60s （上下文：http server location） 
    proxy_read_timeout 60;

	#后端服务器数据回传时间_就是在规定时间内后端服务器必须传完所有数据 默认60s （上下文：http server location）
    proxy_send_timeout 60;

    #代理请求缓存区_这个缓存区间会保存用户的头信息以供Nginx进行规则处理_一般只要能保存下头信息即可 
    proxy_buffer_size 128k;

    #同上 告诉Nginx保存单个用的几个Buffer最大用多大空间 
    proxy_buffers 4 128k;

    #如果系统很忙的时候可以申请更大的proxy_buffers 官方推荐*2
    proxy_busy_buffers_size 256k;

    #proxy缓存临时文件的大小
    proxy_temp_file_write_size 128k;

    #用于指定本地目录来缓冲较大的代理请求
    proxy_temp_path /usr/local/nginx/temp;

    #设置web缓存区名为cache_one,内存缓存空间大小为12000M，自动清除超过15天没有被访问过的缓存数据，硬盘缓存空间大小200g
    proxy_cache_path /usr/local/nginx/cache levels=1:2 keys_zone=cache_one:200m inactive=1d max_size=30g;
```

//注意如下：
此处的重点在最后一句，缓存存储路径为：/usr/local/nginx/cache，levels=1:2代表缓存的目录结构为2级目录
如下图，缓存会在/usr/local/nginx/cache目录下生成，包含2级目录，在之下就是缓存文件，测试的时候可以到该目录下查看缓存文件是否生成。



#### 2.9.2、location花括号下的配置

```nginx
    #静态数据保存时效
location ~ \.html$ {
      proxy_pass http://source.qingk.cn;
      proxy_redirect off;
      proxy_cache cache_one;
      #此处的cache_one必须于上一步配置的缓存区域名称相同
      proxy_cache_valid 200 304 12h;
      proxy_cache_valid 301 302 1d;
      proxy_cache_valid any 1m;
      #不同的请求设置不同的缓存时效
      proxy_cache_key $uri$is_args$args;
      #生产缓存文件的key，通过4个string变量结合生成
      expires 30d;
      #其余类型的缓存时效为30天
      proxy_set_header X-Forwarded-Proto $scheme;
}
```

//注意如下：
1、只有在proxy_pass的时候，才会生成缓存，下一次请求执行到proxy_pass的时候会判断是否有缓存，如果有则直接读缓存，返回给客户端，不会执行proxy_pass；如果没有，则执行proxy_pass，并按照规则生成缓存文件；可以到nginx的cache文件夹下看是否生成了缓存文件。
2、proxy_set_header Host $host 这一句可能导致缓存失败，所以不能配置这一句。我在测试的时候遇到了这个问题，不明原理。
3、proxy_pass使用upstream出错，换成域名或ip则可行。



#### 2.9.3、在proxy_pass跳转的location中配置静态文件的路径

```nginx
location ~ .*\.(html)$ {
default_type 'text/html';
root "/usr/local/openresty/nginx/html"; //会在指向tomcat服务器前先判断是否缓存有，若指向的缓存文件中有则取缓存，若指向的缓存文件中无则去访问tomcat服务器
}
//下面的配置未完待续： https://blog.csdn.net/zhangjunli/article/details/80436489
```



## 3、补充说明

### 3.1、nginx配置补充

//放在http花括号下

1、backup

```nginx
upstream ops_load {
    server 192.168.56.103:8081 backup;   #该主机只做备份，8082和8083都无法使用时才使用8081
    server 192.168.56.103:8082;
    server 192.168.56.103:8083
}
```

2、down

```nginx
upstream ops_load {
    server 192.168.56.103:8081 down;   #8081机子可能有问题，先暂时将8081停掉
    server 192.168.56.103:8082;
    server 192.168.56.103:8083
}
```

3、ip_hash

```nginx
upstream ops_load {
    ip_hash;   #标了这个命令后，那么用户第一次请求转到这个proxy_pass后，到了那一台机后，以后都会走这一台了
    server 192.168.56.103:8081; 
    server 192.168.56.103:8082;
    server 192.168.56.103:8083
}
```



### 3.2、nginx日志

```txt
access_log：指的是访问日志，我们通过访问日志可以获取用户的IP、请求处理的时间、浏览器信息等
error_log：错误日志记录了访问出错的信息，可以用于定位错误的原因(404等)
//凡是请求在nginx里面有日志，说明请求已经到达了nginx了
```



client_max_body_size 1200m;