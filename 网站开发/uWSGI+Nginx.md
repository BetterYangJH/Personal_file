# uWSGI+Nginx

## 项目迁移

1.  在安装机器上安装和配置同版本的数据库,python及依懒的包

    ```shell
    # 导出当前模块数据包的信息:
    $ pip3 freeze > package_list.txt
    # 导入到另一台新主机
    $ pip3 install -r package_list.txt
    ```

2.  django 项目迁移

    ```shell
    sudo scp /home/tarena/django/mysite1 root@88.77.66.55:/home/root/xxx
    ```

## WSGI配置

-   Web服务器网关接口，实现WSGI协议、http等协议
-   Nginx -- uWSGI -- Django -- uWSGI -- Nginx
    -   Nginx中HttpUwsgiModule与uWSGI服务器进行交换。
    -   uWSGI调用django触发请求和响应，字典方式给django参数纯方法级调用，不是数据传输
-   uwsgi启动不会像runserver一样自动重载，django需要stop-start重启uWSGI
-   当uwsgi 启动后，django项目的程序已变成后台守护进程,不需要runserver，在关闭当前终端时此进程也不会停止。

### uWSGI步骤 (ubuntu 18.04 )

-   安装uWSGI

    ```shell
    $ sudo pip3 install uwsgi
    ```

-   配置uWSGI

    -   添加配置文件 `项目文件夹/uwsgi.ini`

        ```ini
        [uwsgi]
        # 通讯方式及端口
        socket=127.0.0.1:8000
        # http=127.0.0.1:8000
        
        # 项目当前工作目录
        chdir=.../my_project 这里需要换为项目文件夹的绝对路径，最外层文件夹目录
        # 项目中wsgi.py文件的目录，相对于shdir
        wsgi-file=my_project/wsgi.py
        # 进程个数
        process=4
        # 每个进程的线程个数
        threads=2
        # 服务的主进程pid记录文件
        pidfile=uwsgi.pid
        # 服务的目志文件位置
        daemonize=uwsgi.log
        # 开启主进程管理模式
        # 一主多从，主接收外界通讯并fork子进程，子进程实现具体功能
        master=true
        
        # 通讯方式区别
        # http
        # 客户端主动发起请求,服务器响应,服务器不能主动发起响应.
        # 一次请求完毕后则断开连接,以节省资源.
        
        # Socket
        # 双方都可以主动发送数据
        # 客户端跟服务器直接使用Socket进行连接,可保持连接通道
        ```

    -   修改settings.py

        ```python
        DEBUG=False
        ALLOWED_HOSTS = ['网站域名'] 或者 ['服务监听的ip地址']
        ```

-   运行uWSGI

    ```shell
    # 启动 uwsgi
    $ cd 项目文件夹
    $ sudo uwsgi --ini 项目文件夹/uwsgi.ini
    # 停止 uwsgi
    $ cd 项目文件夹
    $ sudo uwsgi --stop uwsgi.pid
    ```

-   确认状态：

    ```shell
    $ ps aux|grep 'uwsgi'  查看当前进程
    $ vim uwsgi.pid        查看主进程pid
    
    ps显示的最小的ini（主进程）要和pid文件中记录的相同
    不同说明重复开启，记录与实际不一致，需要kill进程重新启动
    ps -ef | grep 'uwsgi' | grep -v grep |awk '{print $2}' | xargs kill -9 关闭所有进程
    ```

## nginx 配置

-   配置 nginx 反向代理服务器
-   用nginx 配置静态文件路径,解决静态路径问题
-   Nginx是轻量级的高性能Web服务器，提供了诸如HTTP代理（正向代理）和反向代理、负载均衡、缓存等一系列重要特性，在实践之中使用广泛，C语言超并发。
-   Nginx单独部署，uwsgi和django在一起
-   Nginx中server{location url{server_path}}给执行路由指定静态文件地址

### nginx步骤 (ubuntu 18.04 )

-   安装 nginx

    ```shell
    $ sudo apt install nginx
    ```

-   配置 nginx

    ```shell
    $ sudo su
    $ vim /etc/nginx/sites-enabled/default
    server {
        ...
        location / {
        	# 重定向到127.0.0.1的8000端口
            uwsgi_pass 127.0.0.1:8000;  
            # 将所有的参数转到uwsgi下
            include /etc/nginx/uwsgi_params;  
        }
        location /static {
            # root 第一步创建文件夹的绝对路径,如:
             root .../项目名_static;          
        }
        ...
    }
    # 检查语法是否正确
    $ sudo nginx -t
    # 重启服务
    $ sudo service nginx start|stop|restart|status
    ```

-   配置nginx 静态文件路径

    -   创建新路径-主要存放Django所有静态文件 如： .../项目名_static/

    -   在Django中添加新配置

        ```python
        # settings.py 
        STATIC_ROOT = '.../项目名_static/static  
        ```

    -   将Django项目重所有静态文件复制到STATIC_ROOT中 ，包括Django内建的静态文件

        ```shell
        $ python3 manage.py collectstatic 
        ```

## 404/500 界面

-   在模板文件夹内添加 404/500.html 模版，当视图触发Http404/500 异常时将会被显示
-   404/500.html 仅在发布版中(即setting.py 中的 DEBUG=False时) 才起作用
-   当向应处理函数触发Http404/500异常时就会跳转到404/500界面

```python
# setting
'DIRS': [os.path.join(BASE_DIR,'templates')],
# 主templates
from django.http import Http404/500
def xxx_view( ):
    raise Http404/500  # 直接返回404/500
```

### Nginx和Apache

-   nginx
    -   异步非阻塞多进程，高并发性更好
    -   轻量级，占用内存资源更少，配置简洁，模块化设计
    -   更适合负载均衡和反向代理
-   Apache
    -   同步多进程，稳定性更好
    -   模块多，功能全，reweite更强

## 邮件告警

-   当正式服务器上代码运行有报错时，可将错误追溯信息发至指定的邮箱

-   配置如下 settings.py

    ```python
    #关闭调试模式
    DEBUG = False  
    
    #错误报告接收方
    ADMINS = [
        ('name1', 'xxxx@example.com'), 
        ('name2', 'xxxx@example.com')
    ]
    
    #发送错误报告方，默认为root@localhost账户，多数邮件服务器会拒绝
    SERVER_EMAIL = 'email配置中的邮箱'
    ```

-   过滤敏感信息

    报错邮件中会显示一些错误的追踪，这些错误追踪中会出现如 password等敏感信息，Django已经将配置文件中的敏感信息 过滤修改为 多个星号，但是用户自定义的视图函数需要用户手动过滤敏感信息

    -   views.py

        ```python
        from django.views.decorators.debug import sensitive_variables
        
        @sensitive_variables('user', 'pw', 'cc')
        def process_info(user):
            pw = user.pass_word
            cc = user.credit_card_number
            name = user.name
            ...
        #注意：
        #1 若报错邮件中牵扯到user,pw,cc等局部变量的值，则会将其替换成  *****, 而 name 变量还显示其真实值
        #2 多个装饰器时，需要将其放在最顶部
        #3 若不传参数，则过滤所有局部变量的值
        ```