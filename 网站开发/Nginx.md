# Nginx upstream负载均衡

## 概念

是一种策略，用于防止一台服务器过载，而其他服务器闲置情况发生的策略。通过该策略可以使得提供相同服务的服务器负载基本相同 。

## 常用策略

-   轮询(默认)
-   加权轮询
-   IP_hash
-   URL_hash
-   响应时间

### 轮询

轮询是upstream的默认分配方式，即每个请求按照时间顺序轮流分配到不同的后端服务器，如果某个后端服务器down掉后，能自动剔除

```nginx
upstream  xxx {
	server 10.0.0.7:80; 
	server 10.0.0.8:80; 
}
```

### 权重轮询

权重是轮询的加强版，即可以指定轮询比率，weight和访问几率成正比，主要应用于后端服务器硬件差异性的场景下

按照权重8050端口走5个请求，8060端口才会走一个请求

```nginx
upstream xxx {
    server 127.0.0.1:8050       
    	weight=5;
    server 127.0.0.1:8060       
        weight=1;
｝
```

### IP_hash＋真实IP

-   每个请求按访问IP的hash结果分配，这样每个访客固定访问一个后端服务器，web服务器就能建立起稳固的session

    ```nginx
    upstream xxx {
       ip_hash;
       server 192.168.100.104:80;
       server 192.168.100.105:80;
     }
    ```

-   IP_hash的问题

    要求Nginx一定是最前端的服务器,否则Nginx得不到正确IP,就不能根据IP作hash.我们可以通过在nginx中获取用户请求时的真实IP，然后根据这些真实IP做hash策略，也就是自定义nginx的hash策略。

    ```nginx
    map $http_x_forwarded_for $clientRealIp {
    	# 没有通过代理，直接用 remote_addr
    	"" $remote_addr;
        #用正则匹配，从 x_forwarded_for 中取得用户的原始IP
        #例如 X-Forwarded-For: 202.123.123.11, 208.22.22.234, 192.168.2.100,...
        #这里第一个 202.123.123.11 是用户的真实 IP，后面其它都是经过的 CDN 服务器
    	~^(?P<firstAddr>[0-9\.]+),?.*$ 
            $firstAddr;
    }
    
    --skip--
    
    upstream xxx {
    	hash $clientRealIp;
    	server 192.168.100.104:9080;
    	server 192.168.100.105:9080;
    }
    ```

### url_hash+缓存命中

-   按访问url的hash结果来分配请求，后端服务器为缓存时比较有效
-   注意：在upstream中加入hash语句，server语句中不能写入weight等其他的参数，hash_method是使用的hash算法
-   要配合缓存命中来使用
    -   集群服务器同一个资源多次请求，可能会到达不同的服务器上，导致不必要的多次下载
    -   使用url_hash策略，同一个url（也就是同一个资源请求）会到达同一台机器，缓存资源，再收请求，从缓存中读取
    -   减少带宽，减少时间
    -   客户端-服务器集群A-云存储-下载文件-临时缓存-服务器集群A-客户端

```nginx
 upstream xxx { 
      server 10.0.0.10:7777; 
      server 10.0.0.11:8888; 
      hash $request_uri; 
      hash_method crc32;
}
```

### 后端响应时间

-   fair(第三方)

    -   按照后端服务器的响应时间rt来分配请求
    -   响应时间短即rt小的后端服务器优先分配请求
    -   必须下载nginx-upstream-fair-master

    ```nginx
     upstream backend {
     	fair;
        server 192.168.1.101;
        server 192.168.1.102;
        server 192.168.1.103;
                
    }
    ```

已安装Nginx，配置第三方模块时，只需要--add-module=/第三方模块目录，然后make编译一下就可以，不要 make install 安装。编译后复制objs下面的Nginx到指定目录下

## 参数

| 参数         | 描述                                           |
| ------------ | ---------------------------------------------- |
| service      | 反向服务地址＋端口                             |
| weight       | 权重                                           |
| max_fails    | 失败多少次认为主机已挂掉，踢出                 |
| fail_timeout | 踢出后重新探测时间                             |
| backup       | 当其他的节点挂掉，启动的预留备份服务器         |
| max_conns    | 最大连接数                                     |
| slow_start   | 当节点恢复，不立即加入                         |
| down         | 当前的server暂时不参与负载均衡(不对外提供服务) |

### max_conns

可以根据服务的好坏来设置最大连接数

```nginx
upstream xxx {
    server 127.0.0.1:8050    weight=5  max_conns=800;
    server 127.0.0.1:8060    weight=1;
}
```

### max_fails、 fail_timeout

-   max_fails=3, fail_timeout=30s
    -   30秒内请求某一应用失败3次，认为该应用宕机
    -   等待30秒，这期间内不会再把新请求发送到宕机应用
    -   时间到后再有请求进来继续尝试连接宕机应用且仅尝试1次，如果还是失败，则继续等待30秒
    -   直到恢复。

```nginx
upstream xxx {
    server 127.0.0.1:8050    weight=1  max_fails=1  fail_timeout=30;
    server 127.0.0.1:8060    weight=1;
}
```

### down

8060端口和8070端口暂不对外开放

```nginx
upstream xxx {
    server 127.0.0.1:8050    weight=1  max_fails=1  fail_timeout=20;
    server 127.0.0.1:8060    weight=1  down;
    server 127.0.0.1:8070    weight=1  down; 
    server 127.0.0.1:8090    weight=1;
}
```

### backup

8070端口是备份节点

当8050端口号或者8090端口挂掉时候，8070端口备份节点才会开启，当8050端口号或者8090端口挂掉再启动时候，8070端口节点才会再次关闭

```nginx
upstream xxx {
    server 127.0.0.1:8050    weight=1  max_fails=1  fail_timeout=20;
    server 127.0.0.1:8060    weight=1  down；
    server 127.0.0.1:8070    weight=1  backup; 
    server 127.0.0.1:8090    weight=1  max_fails=1  fail_timeout=20;
}
```

## location配置

例子：

```nginx
location / { 
            root  html; 
            index  index.html index.htm; 
            proxy_pass http://xxx; 
}
```

localtion匹配优先级

| 顺序 | 匹配标识的location                  | 匹配说明 |
| ---- | ----------------------------------- | -------- |
| 1    | location = / {                      | 精确匹配 |
| 2    | location = ^~ /images/ {            | 前缀匹配 |
| 3    | loction ~* .(gif \| jpg \| jpeg)$ { | 正则匹配 |
| 4    | location /documents/ {              | 模糊匹配 |
| 5    | location / {                        | 默认匹配 |

## Nginx 企业当中配置

-   /etc/nginx/nginx.conf

```nginx
#定义Nginx运行的用户和用户组
user www www;
 
#nginx进程数，建议设置为等于CPU总核心数。
worker_processes 8;
 
#全局错误日志定义类型
#记录级别[debug|info|notice|warn|error|crit]
error_log /var/logs/error.log info;
 
#进程文件
pid runnginx.pid;
 
#一个nginx进程打开的最多文件描述符数目
#理论值应该是最多打开文件数(系统的值ulimit-n)与nginx进程数相除
#但是nginx分配请求并不均匀，所以建议与ulimit-n的值保持一致。
worker_rlimit_nofile 65535;
 
#事件处理设置
events {
    #事件模型
    #[kqueue|rtsig|epoll|/dev/poll|select|poll]
    #epoll模型是Linux 2.6以上版本内核中的高性能网络I/O模型		#FreeBSD就用kqueue模型。
	use epoll;
	#单个进程最大连接数（最大连接数=连接数*进程数）
	worker_connections 65535;
}
 
#http服务器
http {
    #文件扩展名与文件类型映射表
	include mime.types; 
    #默认文件类型
	default_type application/octet-stream; 
    #默认编码
	charset utf-8; 
    #服务器名字的hash表大小
	server_names_hash_bucket_size 128; 
    #上传文件大小限制
	client_header_buffer_size 32k; 
	large_client_header_buffers 4 64k; 
	client_max_body_size 8m;
     #开启高效文件传输模式
     #nginx是否调用sendfile函数来输出文件，对于普通应用设为on
     #用来进行下载等应用磁盘IO重负载应用，可设置为off，以平衡磁盘与网络I/O处理速度，降低系统的负载。
	sendfile on;
    #开启目录列表访问，合适下载服务器，默认关闭。
	autoindex on;
    #防止网络阻塞
	tcp_nopush on; 
	tcp_nodelay on; 
    #长连接超时时间，单位是秒
	keepalive_timeout 120; 
	 	 
#gzip模块设置
    #开启gzip压缩输出
	gzip on; 
    #最小压缩文件大小
	gzip_min_length 1k;
    #压缩缓冲区
	gzip_buffers 4 16k;
    #压缩版本(默认1.1，squid2.5请使用1.0)
	gzip_http_version 1.0;
    #压缩等级
	gzip_comp_level 2;
    #压缩类型，默认就已经包含textml，所以下面就不用再写了
	gzip_types text/plain application/x-javascript text/css application/xml;
	gzip_vary on;
    #开启限制IP连接数的时候需要使用
	#limit_zone crawler $binary_remote_addr 10m; 
    
#负载均衡，weigth参数表示权值，权值越高被分配到的几率越大。 
	upstream xxx {
		server 192.168.80.121:80 weight=3;
		server 192.168.80.122:80 weight=2;
		server 192.168.80.123:80 weight=3;
	}
    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*；     
}
```

-   /etc/nginx/conf.d/xxx.conf

    xxx.conf 此配置文件是自己创建的，必须以`.conf`结尾

```nginx
#虚拟主机的配置
	server {
		#端口
		listen 80;
		#域名可以有多个，用空格隔开
		server_name www.yyy.com yyy.com;
		index index.html index.htm index.php;
   		#启动nginx之前路径
		root /xxx/www/yyy; 
    
#页面缓存expires
#单位：s/m/h/d/max - 秒/分/时/天/最大(不建议)
        #图片缓存时间设置
		location ~ .*.(gif|jpg|jpeg|png|bmp|swf)$ {
			expires 10d;
		}
		#JS和CSS缓存时间设置
		location ~ .*.(js|css)?$ {
			expires 1h; #保留1小时
		}

#代理设置
		#定义本虚拟主机的访问日志
		access_log /var/logs/access.log;
		#对 "/" 启用反向代理
		location / { 
         #一般我们在配置python Django应用时都是使用http，让Nginx直接使用proxy_pass就把它从本地代理到前端服务器
		#当uwsgi配置http=127.0.0.1:8000时，我们可以如下设置Nginx进行代理
        	proxy_pass http://xxx; #代理推送
			proxy_redirect off; #关闭代理重写
         	 
             #将$remote_addr的值放进变量X-Real-IP中，此变量名可变，$remote_addr的值为客户端的ip
        	 #$remote_addr 只能获取到与服务器本身直连的上层请求ip，所以设置$remote_addr一般都是设置第一个代理上面
			proxy_set_header X-Real-IP $remote_addr;
			
#有时通过cdn访问，那么后面web服务器获取到的是cdn的ip而非真是用户ip
#用X-Forwarded—For链路反追踪
#客户真实IP-多层级proxy-web服务器，各层IP都会记录下来
#后端的Web服务器可以通过X-Forwarded-For获取用户真实IP
			proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
			
			#以下是一些反向代理的配置，可选。
        	#$host就是nginx代理服务器，也就是客户端请求的host
			proxy_set_header Host $host; 
        	#允许客户端请求的最大单文件字节数
			client_max_body_size 10m; 
        	#缓冲区代理缓冲用户端请求的最大字节数，
			client_body_buffer_size 128k; 
        	#nginx跟后端服务器连接超时时间(代理连接超时)
			proxy_connect_timeout 90; 
        	#后端服务器数据回传时间(代理发送超时)
			proxy_send_timeout 90; 
        	#连接成功后，后端服务器响应时间(代理接收超时)
			proxy_read_timeout 90; 
        	#设置代理服务器（nginx）保存用户头信息的缓冲区大小
			proxy_buffer_size 4k; 
        	#proxy_buffers缓冲区，网页平均在32k以下的设置
			proxy_buffers 4 32k; 
        	#高负荷下缓冲大小（proxy_buffers*2）
			proxy_busy_buffers_size 64k; 
        	#设定缓存文件夹大小，大于这个值，将从upstream服务器传
			proxy_temp_file_write_size 64k; 
		}

# location设置不同资源配置
    	# 通过location 精确匹配/xxx
    	location = /xxx { 
        #当uwsgi配置socket时，外部浏览器将无法直接访问
        #会提示类似于“缓存区(buffer)超过最大长度”类似的问题
	    #此时使用nginx进行代理时，如下配置
        	uwsgi_param X-Real-IP $remote_addr; 
        	uwsgi_param X-Forwarded-For $proxy_add_x_forwarded_for; 
        	uwsgi_param X-Forwarded-Proto $http_x_forwarded_proto; 
        	include  uwsgi_params;
       		uwsgi_pass 192.168.90.21:8000;          
    	}
    	
		#设定查看Nginx状态的地址
		location /status {
        	#开启统计功能模块
			stub_status on; 
        	#关闭成功日志
			access_log off;  
        	#仅允许这个ip通过
			#allow 192.168.0.1;  
        	#其他都不允许通过
			#deny all;           
		}
		#所有动态页面均交由tomcat
		location ~ .(jsp|jspx|do)?$ {
			proxy_pass http://127.0.0.1:8080;
			proxy_set_header Host $host;
			proxy_set_header X-Real-IP $remote_addr;
			proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		}		
		#所有静态文件由nginx直接读取不经过tomcat
		location ~ .*.(htm|html|gif|jpg|jpeg|png|bmp|swf|ioc|rar|zip|txt|flv|mid|doc|ppt|pdf|xls|mp3|wma)$ { 
			expires 15d; 
		}		
		location ~ .*.(js|css)?$ { 
			expires 1h; 
		}
	}
```