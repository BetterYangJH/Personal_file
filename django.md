# 一、Django框架的介绍

## Django的安装

- 查看已安装的版本
    ```python
    >>> import django
    >>> print(django.VERSION)
    (1, 11, 8, 'final', 0)
    ```
- 安装
    1. 在线安装
        - `$ sudo pip3 install django`  安装django的最新版本
        - `$ sudo pip3 install django[==版本]` 安装django的指定版本
    2. 离线安装
        - 安装离线包
            - `$ tar -xvf Django-1.11.8.tar.gz`
            - `$ cd Django-1.11.8`
            - `$ sudo python3 setup.py install`
- Django的卸载
  
  `$ pip3 uninstall django`
  
- Django 的开发环境
    - Django 1.11.x 支持 Python 2.7, 3.4, 3.5 和 3.6（长期支持版本 LTS)
    - 注: Django 1.11.x 不支持 Python 3.7

# 二、Django框架开发

## 创建项目

  - 创建

    `$ django-admin startproject 项目名称`

  - 其他命令
    ```shell
    $ cd 项目目录
    # 运行
    $ python3 manage.py runserver#开发模式服务器，默认127.0.0.1：8000可修改
    $ python3 manage.py runserver 5000
    # 创建app
    $ python3 manage.py startapp
    # 数据库
    $ python3 manage.py makemigrations
    $ python3 manage.py migrate
    # SHELL
    $ python3 manage.py shell
    ```
## 项目目录结构

- `manage.py`项目管理的主程序,在开发阶段用于管理整个项目的开发运行的调式
  
    - `manage.py` 包含项目管理的子命令, 如:
        - `python3 manage.py runserver` 启动服务
        - `python3 manage.py startapp` 创建应用
        - `python3 manage.py migrate` 数据库迁移
    
- `mysite1` 项目包文件夹
  
    - `__init__.py`包初始化文件,当此项目包被导入(import)时此文件会自动运行，添加mysql欺骗
    
        ```python
        import pymysql
        pymysql.install_as_MySQLdb()
        ```
    
    - `wsgi.py`Web Server Gateway Interface网关接口的配置文件，仅部署项目时使用
    
    - `urls.py`项目的基础路由配置文件，所有的动态路径必须先走该文件进行匹配
    
    - `settings.py`Django项目的配置文件, 此配置文件中的一些全局变量将为Django框架的运行传递一些参
    
- `templates`（自己创建的动态HTMl模板文件夹）

- `static`（自己创建的静态资源文件夹）

- `middleware`（自己创建的中间件文件夹）

    - `mymiddleware.py`

- `app`（自己创建的app文件夹）
		
  
  - `templates`文件夹,自己创建的动态HTMl模板
		- `app`防止寻找模板顺序错误创建的文件夹
  - `static`（自己创建的静态资源文件夹）
  - `migrations` 文件夹,保存数据迁移的中间文件
  - `__init__.py`应用子包的初始化文件
  - `admin.py`应用的后台管理配置文件,使自定义类能在admin管理平台显示
  - `apps.py`应用的属性配置文件
  - `models.py`与数据库相关的模型映射类文件,定义模型
  - `tests.py`应用的单元测试文件
  - `views.py`视图函数
  - `url.py`二级路由分发

## 基础配置(settings.py)

**此模块需要通过 `from django.conf import settings` 导入和使用**

1. BASE_DIR 绑定当前项目的绝对路径
   
    动态计算出来的，保证不同机器不修改地址就可以执行, 所有文件都可以依懒此路径
    
    ```python3
    os.path.abspath(__file__)  当前文件绝对路径
    os.path.dirname(os.path.abspath(__file__))  父路径--mysite1包路径
    os.path.dirname(os.path.dirname(os.path.abspath(__file__)))  父路径--mysite1项目路径
    ```

 2. DEBUG 用于配置目的启用模式, 取值:
    1. True 表示开发环境中使用 `调试模式`(用于开发中)
    2. False 表示当前项目运行在`生产环境`(不启用调试)
    
3. ALLOWED_HOSTS
    - 生产环境必须指定ALLOWED_HOSTS
      
    - 设置允许访问到本项目的网络地址列表,取值:
      
        1. [] 空列表,表示只有请求头中host为`127.0.0.1`, `localhost`能访问本项目
        2. ['*']，表示任何请求头的host都能访问到当前项目
        3. ['192.168.1.3', '127.0.0.1'] 表示只有当前两个host头的值能访问当前项目
        ```python3
        注意:
        如果要在局域网其它主机也能访问此主机,启动方式应使用如下模式：
        python3 manage.py runserver 0.0.0.0:5000
        # 指定网络设备所有主机都可以通过5000端口访问(需加`ALLOWED_HOSTS = ['*']`) 
        ```

4. INSTALLED_APPS 指定当前项目中安装的应用列表，注册app
   
5. MIDDLEWARE 用于注册中间件，可以自己写中间件

    post请求注释掉'django.middleware.csrf.CsrfViewMiddleware'

6. TEMPLATE 模板的配置信息

7. DATABASE 数据库的配置信息，不同数据配置不同

8. LANGUAGE_CODE 语言配置，英文 : `"en-us"`/中文 : `"zh-Hans"`

9. TIME_ZONE 服务器端时区，世界标准时间: `"UTC"`/中国时区 : `"Asia/Shanghai"`

10. ROOT_URLCONF 根级 url 配置 'mysite1.urls'
## 视图函数(view.py)

- 视图函数是用于接收一个浏览器请求并通过HttpResponse对象返回数据的函数。此函数可以接收浏览器请求并根据业务逻辑返回相应的内容给浏览器
- 视图处理的函数的语法格式:
    ```python
    from django.http import HttpResponse
    def xxx_view(request[, 其它参数...]):
        return HttpResponse对象
    request用于绑定HttpRequest对象，通过此对象可以获取浏览器的参数和数据
    ```

*   视图类

    ```python
    from django.views.generic import View
    
    class GoodsIndexView(View)
    def get(self, request)
    def post/delete……
    使用一个或多个实例方法，对应响应提交方式的处理函数
    ```

## 路由配置(url.py)

- settings.py 中的`ROOT_URLCONF` 指定了主路由配置列表urlpatterns的文件位置

- 如果同一个路由可以匹配多个正则，从上到下直到匹配到第一个符合的
  
- 最后用‘’匹配所有非法路径，增强鲁棒性
  
- 地址只差/django会补全，setting里APPEND_SLASH
  
- 不加url时，自动有首页，加了任意url时，默认主页匹配失效，需要指定
  
  > urlpatterns 是一个路由-视图函数映射关的列表,此列表的映射关系由url函数来确定
  

```python
from django.conf.urls import url

urlpatterns=[url(regex, views.fun/include(app.urls), name=None),url()……]

不同路由可以共用一个视图函数

# regex
一般格式：r'^/xxx$'，匹配http：//ip:port/后面的内容
# 位置传参
可以用正则表达式分组()提取参数后用函数位置传参传递给视图函数
view
def fun(request,n1,n2...)
	return
url
url('()()',view.fun)
()分组内容按顺序传给fun的除request后参数
# 关键字传参
用正则表达式分组(?P<name>pattern)提取参数后用函数关键字传参传递给视图函数
# file : <项目名>/urls.py
# 以下示例匹配
# http://127.0.0.1:8000/person/xiaoming/20
# http://127.0.0.1:8000/person/xiaohong/29
# http://127.0.0.1:8000/person/xiaolan/9
urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^person/(?P<name>\w+)/(?P<age>\d{1,2})',views.person_view),
]
组名必须和def形参名对应

# 一级
from django.conf.urls import url, include
urlpatterns = [
    #http://127.0.0.1:8000/music/xxxx
    url(r'^music/', include('music.urls')),
]
# 二级
from django.conf.urls import url
from . import views
urlpatterns = [
    url(r'^index$', views.index)
]
```

## 框架设计模式

-   以前MVC 模式
    -   MVC 代表 Model-View-Controller（模型-视图-控制器） 模式。
    -   MVC
        -   M 模型层(Model), 数据库
        -   V 视图层(View),前端
        -   C 控制(Controller)，处理请求、获取数据、返回结果
-   Django的MTV 模式
    
    *   MTV 代表 Model-Template-View（模型-模板-视图） 模式。这种模式用于应用程序的分层开发
    
    -   MTV 
        -   M -- 模型层(Model) ， 数据库
        -   T -- 模板层(Template)  ，前端
        -   V -- 视图层(View)  ，接收请求、获取数据、返回结果

## 请求响应

### 请求对象

request.post

表单提交头Content-Type: application/x-www-form-urlencoded

get_json

表单提交头Content-Type: json

get请求：

提交数据由url完成，不在请求体提交

### 响应

*   字符

    HttpResponse（‘’）

*   页面

    render（request,'html',dict）

*   302跳转

    HttpResponseRedirect('url!!!')

## 创建项目流程

```python
# 1. setting
LANGUAGE_CODE = 'zh-Hans'
TIME_ZONE = 'Asia/Shanghai'

# 2. shell 生成app
python3 manage.py startapp book
# 3. settings.py 注册app
INSTALLED_APPS=[;book]
# 4. url.py 配置路由
url(r/,locals),app内urlpatterns

# 5. post请求：setting注释掉MIDDLEWARE中# 'django.middleware.csrf.CsrfViewMiddleware',

# 6. 数据库（创建-配置-欺骗-数据库-类-manage-migrate）
1. 手动创建数据库，create charset，删除drop
2. setting.py
    DATABASES = {'default': 
    	{'ENGINE': 'django.db.backends.mysql',       		  
         'NAME': 'mysite4',        
         'USER': 'root',        
		 'PASSWORD': '123456',        
		 'HOST': '127.0.0.1',        
		 'PORT': 3306}}
3. init.py
import pymysql
pymysql.install_as_MySQLdb()
4. app的model.py
创建类
5. shell
python3 manage.py makemigrations
python3 manage.py migrate
python3 manage.py shell
	from book.models import Book
	Book.objects.create(关键字传参)
	这里做的修改自动migtate
```

## 模块位置

```python
# views
from django.http import HttpResponse
from django.shortcuts import render

from django.conf import settings

from .models import Book

from django.core.paginator import Paginator
from django.views.decorators.cache import cache_page

# urls
from django.conf.urls import url，include
from . import views

# models
from django.db import models

# admin.py
from . import models
from django.contrib import admin
```

## request

```python
request.method  # 'GET', 'POST', 'HEAD', 'DELETE'
request.GET     # QueryDict 用来获取查询字符串的内容
request.path_info
request.META
request.POST

render
HttpResponse
HttpResponseRedirect
```

### 域名

```python
# url.py
不加'/'
url(r'^xxx/xxx$',include(app.urls))

# views.py
# 静态文件：template省略
render(request,'/app/xxx.html')

# html
加'/'
<form action='/index'>
```



# 三、模板 Templates

**模板可以根据视图中传递的字典数据动态生成相应的HTML网页**

## 模板的配置

-    默认的模块文件夹`templates` 
    
-   模板的配置
    
    -   在 settings.py 中有一个 TEMPLATES 变量
        
        ```python
        # file: settings.py
        TEMPLATES = [
            {
                'BACKEND': 'django.template.backends.django.DjangoTemplates',
                'DIRS': [os.path.join(BASE_DIR, 'templates')],  # 添加模板路径
                'APP_DIRS': True,  # 是否索引各app里的templates目录
                ...
            },
        ]
        ```
        
        1.  BACKEND : 指定模板的引擎（包，解析templates）
        2.  DIRS : 模板的搜索目录(可以是一个或多个)
        3.  APP_DIRS : 是否要在应用中的 `templates` 文件夹中搜索模板文件
        4.  OPTIONS : 有关模板的选项
    
-   模板的加载方式

    -   通过 loader 获取模板,通过HttpResponse进行响应

        ```python
        from django.template import loader
        # 1.通过loader加载模板
        t = loader.get_template("模板文件名")
        # 2.将t转换成 HTML 字符串
        html = t.render(字典数据)
        # 3.用响应对象将转换的字符串内容返回给浏览器
        return HttpResponse(html)
        ```

    *   使用 render() 直接加载并响应模板

        ```python
        from django.shortcuts import render
        return render(request,'模板文件名', 字典数据)
        ```

## 模板的变量

*   视图函数中必须将变量封装到字典中才允许传递到模板上

```python
def xxx_view(request)
    dic = {
        "变量1":"值1",
        "变量2":"值2",
    }
    return render(request, 'xxx.html', dic)
可以用locals函数封装全部局部变量传递
```

## url 反向解析

-   url 反向解析是指在视图或模板中，用为url定义的名称来查找或计算出相应的路由

-   url 函数的语法

    -   url(regex, views.fun, kwargs=None, name="别名")
    
-   url() 的`name`关键字参数

    -   作用:

        -   根据url 列表中的`name=`关键字传参给 url确定了个唯一确定的名字，在模板中，可以通过这个名字反向推断出此url信息

    -   在模板中通过别名实现地址的反向解析

        ```
        {% url '别名' %}
        {% url '别名' '参数值1' '参数值2' %}
        ```

## 模板查找规则

*   [APP_DIRS TRUE]以及配置了外层的templates路径

*   查找外层 -- 自己应用内部 -- 按照settigns.py中注册的顺序查找其他应用内的templates文件夹

*   解决方法

    在本身templates里创建和应用同名文件夹，存放html，引用时加文件夹名
    eg：news--templates--news--index.html

    ​		views--def--return--news/index,html

# 四、静态文件static

**不能与服务器端做动态交互的文件都是静态文件如:图片,css,js,音频,视频,固定的html文件**

## 静态文件配置

-   在 settings.py 中配置一下两项内容:

```python
# file: setting.py
# 通用前缀，不用每一个都加一边
STATIC_URL = '/static/'
# 静态文件在服务器中的位置
STATICFILES_DIRS = (
    os.path.join(BASE_DIR, "static"),
)
```

# 五、 HTMl的静态文件和模板的使用

## 静态文件

1.  使用静态文件的访问路径进行访问(相对地址，用当前地址栏ip，port拼出地址)

    -   访问路径: STATIC_URL = '/static/'

    -   示例:

        ```python
        <img src="/static/images/lena.jpg">
        <img src="http://127.0.0.1:8000/static/images/lena.jpg">
        ```

2.  通过 {% static %}标签访问静态文件（django标签地址，用static_url拼出地址）

    -   `{% static %}` 表示的就是静态文件访问路径

    1.  加载 static
        -   `{% load static %}`
    2.  使用静态资源时
        -   语法:
            -   `{% static '静态资源路径' %}`
        -   示例:
            -   `<img src="{% static 'images/lena.jpg' %}">`

    正式环境下，静态资源往往交由第三方CDN厂商处理，src地址写第三方厂商提供地址

## 模板

#### 模板的变量

*   在模板中使用变量语法
    *   `{{ 变量名 }}`
    *   `{{ 变量名.index }}`
    *   `{{ 变量名.key}}`
    *   `{{ 对象.方法 }}`
    *   `{{ 函数名 }}`

#### 模板的标签

1.  作用

    -   将一些服务器端的功能嵌入到模板中

2.  标签语法

    ```
    {% 标签 %}
    ...
    {% 结束标签 %}
    ```

3.  if 标签

    ```
    {% if 条件表达式1 %}
    ...
    {% elif 条件表达式2 %}
    ...
    {% elif 条件表达式3 %}
    ...
    {% else %}
    ...
    {% endif %}
    ```

4.  if 标签里的布尔运算符

    -   if 条件表达式里可以用的运算符 ==, !=, <, >, <=, >=, in, not in, is, is not, not、and、or
    -   在if标记中使用实际括号是无效的语法。 如果您需要它们指示优先级，则应使用嵌套的if标记。

5.  for 标签

    1.  语法

        ```
        {% for 变量 in 可迭代对象 %}
            ... 循环语句
        {% empty %}
            ... 可迭代对象无数据时填充的语句
        {% endfor %}
        ```

    2.  内置变量 - forloop

        |        变量         |                描述                 |
        | :-----------------: | :---------------------------------: |
        |   forloop.counter   |    循环的当前迭代（从1开始索引）    |
        |  forloop.counter0   |    循环的当前迭代（从0开始索引）    |
        | forloop.revcounter  |  循环结束的迭代次数（从1开始索引）  |
        | forloop.revcounter0 |  循环结束的迭代次数（从0开始索引）  |
        |    forloop.first    |   如果这是第一次通过循环，则为真    |
        |    forloop.last     |    如果这是最后一次循环，则为真     |
        | forloop.parentloop  | 当嵌套循环，parentloop 表示外层循环 |

#### 过滤器

1.  作用

    -   在变量输出时对变量的值进行处理
    -   您可以通过使用 过滤器来改变变量的输出显示。

2.  语法

    -   {{ 变量 | 过滤器1:参数值1 | 过滤器2:参数值2 ... }}

3.  常用的过滤器

    |      过滤器       |                             说明                             |
    | :---------------: | :----------------------------------------------------------: |
    |       lower       |                   将字符串转换为全部小写。                   |
    |       upper       |                    将字符串转换为大写形式                    |
    |       safe        |              默认不对变量内的字符串进行html转义              |
    |     add: "n"      |                      将value的值增加 n                       |
    | truncatechars:'n' | 如果字符串字符多于指定的字符数量，那么会被截断。 截断的字符串将以可翻译的省略号序列（“...”）结尾。 |

#### 模板的继承

-   模板继承可以使父模板的内容重用,子模板直接继承父模板的全部内容并可以覆盖父模板中相应的块

-   定义父模板中的块 `block`标签

    ```
    {% block block_name %}
    定义模板块，此模板块可以被子模板重新定义的同名块覆盖
    {% endblock block_name %}
    ```

-   继承模板 `extends` 标签(写在模板文件的第一行)

    -   子模板继承语法标签
        -   `{% extends '父模板名称' %}`
    -   子模板复用/重写父模板中的内容块

    ```
    {% block block_name %}
    子模板块用来覆盖父模板中 block_name 块的内容
    {% endblock block_name %}
    ```

# 六、app

-   应用在Django项目中是一个独立的业务模块,可以包含自己的路由,视图,模板,模型

## 创建应用app

-   创建步骤

    -   python3 manage.py startapp 应用名称(必须是标识符命令规则)

    -   在settings.py 的 INSTALLED_APPS 列表中配置注册此应用

        ```python
        # file : settings.py 
        INSTALLED_APPS = [
            ... ...,
            '自定义应用名称'
        ]
        ```

-   Django应用的结构组成

    1.  `migrations` 文件夹
        -   保存数据迁移的中间文件
    2.  `__init__.py`
        -   应用子包的初始化文件
    3.  `admin.py`
        -   应用的后台管理配置文件
    4.  `apps.py`
        -   应用的属性配置文件
    5.  `models.py`
        -   与数据库相关的模型映射类文件
    6.  `tests.py`
        -   应用的单元测试文件
    7.  `views.py`
        -   定义视图处理函数的文件


## 应用的分布式路由

-   Django中，基础路由配置文件(urls.py)可以不处理用户具体路由，基础路由配置文件的可以做请求的分发(分布式请求处理)。具体的请求可以由各自的应用来进行处理（主url分发路由，子url配置路由）

## include 函数

-   作用:

    -   用于分发将当前路由转到各个应用的路由配置文件的 urlpatterns 进行分布式处理

        ```python
        # 主目录
        from django.conf.urls import include
        url（r'^$',include('music.urls')）
        # 应用
        创建urls.py
        urlpatterns = [url(index,fun)]，这里的r会解析成/app/index
        ```


# 七、数据库模型

## mysql 数据库配置使用 

1.  安装 pymysql包

    -   用作 python 和 mysql 的接口
        -   `$ sudo pip3 install pymysql`
    -   安装 mysql 客户端(非必须)
        `$ sudo pip3 install mysqlclient`

2.  手动创建数据库

    `create database 数据库名 default charset utf8      ;`

3.  数据库的配置

    -   sqlite 数据库配置

        ```python
        # file: settings.py
        DATABASES = {
            'default': {
                    'ENGINE': 'django.db.backends.sqlite3',
                    'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
            }
        }
        ```

    -   mysql 数据库配置

        ```python
        DATABASES = {
            'default' : {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': 'mywebdb',  # 数据库名称,需要自己定义
                'USER': 'root',
                'PASSWORD': '123456',  # 管理员密码
                'HOST': '127.0.0.1',
                'PORT': 3306,
            }
        }
        1.  ENGINE 后端引擎
        2.  NAME 数据库的名称
        3.  USER 数据库的用户名
        4.  PASSWORD 接数据库的密码。
        5.  HOST 数据库时的主机IP。
        6.  PORT 数据库时使用的端口。
        ```

    4.  添加 mysql 支持

        修改项目中\_\_init\_\_.py 加入如下内容来提供pymysql引擎的支持

        ```python
    import pymysql
        pymysql.install_as_MySQLdb()
        ```

## 模型（Models）

-   模型是一个Python类，它是由django.db.models.Model派生出的子类。
    -   一个模型类代表数据库中的一张数据表，每个对象是一个数据
    -   模型类中每一个类属性都代表数据库中的一个字段。
    -   模型是数据交互的接口，是表示和操作数据库的方法和方式

## Django 的 ORM框架

-   ORM（Object Relational Mapping）即对象关系映射，使用类代替SQL语句继续数据库操作
-   ORM 优势:
    1.  只需要面向对象编程, 不需要面向数据库编写代码.
        -   对数据库的操作都转化成对类属性和方法的操作，不用编写各种数据库的sql语句.
        -   通过简单的配置就可以进行数据库的切换（只改配置，不用改语法）.
    2.  实现了数据模型与数据库的解耦, 屏蔽了不同数据库操作上的差异.
        -   不在关注不同数据库的内部细节.
        -   通过简单的配置就可以轻松更换数据库（只改配置，不用改语法）.
-   ORM 缺点:
    2.  根据对象的操作转换成SQL语句,根据查询的结果转化成对象, 在映射过程中有性能损失.

*   数据库的迁移

    同步对模型所做更改（添加字段，删除模型等） 到数据库

    ```python
    $ python3 manage.py makemigrations   生成一个中间文件,并保存在migrations文件夹中
    $ python3 manage.py migrate			 生成迁移脚本文件,migrations目录中的中间文件 --> 回数据库
    ```

## 编写模型类Models

-   模型类需继承自`django.db.models.Model`

    ```python
from django.db import models
    class 模型类名(models.Model):
        字段名 = models.字段类型(字段选项)
        class Meta:
            数据库储存设置
    模型类名 -- 表名的一部分，建议类名首字母大写
    字段名 -- 类属性名
    ```
3.  字段类型

    BooleanField()
            -   数据库类型:tinyint(1)
                    -   编程语言中:使用True或False来表示值
                            -   在数据库中:使用1或0来表示具体的值
    
    2.  CharField()
        -   数据库类型:varchar
            -   必须要指定max_length参数值
    
    3. DateField()
        	-   数据库类型:date
            -   作用:表示日期
            -   编程语言中:使用字符串来表示具体值
            -   参数3选1:
                -   auto_now: 每次保存对象时间(取值:True/False)。
                -   auto_now_add: 第一次被创建时间(取值:True/False)。
                -   default: 默认设置时间(取值:字符串格式时间如: '2019-6-1')。
    
    4.  DateTimeField()
            -   数据库类型:datetime(6)
        	-   作用:表示日期和时间
            -   参数同DateField
    
    5.  DecimalField()
            -   数据库类型:decimal(x,y)
            -   编程语言中:使用小数表示该列的值
            -   在数据库中:使用小数
            -   参数:
                -   max_digits: 总位数
            	-   decimal_places: 小数点后的位数
    
    6.  FloatField()
            -   数据库类型:double
        	-   编程语言中和数据库中都使用小数表示值
        
    7.  EmailField()
            -   数据库类型:varchar
            -   编程语言和数据库中使用字符串
    
    8.  IntegerField()
            -   数据库类型:int
        	-   编程语言和数据库中使用整数
        
    9.  URLField()
            -   数据库类型:varchar(200
            -   编程语言和数据库中使用字符串
    
    10.  ImageField()
             -   数据库类型:varchar(100)
             -   upload_to="static/images"指定图片的上传路径
             -   会自动重命名保证不会重名，保留映射关系
    
    11. TextField()
            -   数据库类型:longtext
        	-	作用:表示不定长的字符数据
6. 字段选项
* primary_key
    如果设置为True,表示该列为主键,如果指定一个字段为主键，则此数库表不会自动创建id字段，没有设置的话会默认生成
* blank
    设置为True时，字段可以为空。设置为False时，字段是必须填写的。
* null
    默认为False,如果设置为True,表示该列值允许不填写。
* default
    设置所在列的默认值,如果字段选项null=False建议添加此项
* db_index
    如果设置为True,表示为该列增加索引
* unique
    如果设置为True,表示该字段在数据库中的值必须是唯一(不能重复出现的)
* db_column
    指定列的名称,如果不指定的话则采用属性名作为列名
    设置此字段在admin界面上的显示名称。

## 数据库迁移的错误处理方法

-   数据库的迁移文件混乱的解决办法（用django操作时候不能再在mysql里操作）

    1.  删除 所有 migrations 里所有的 000?_XXXX.py (`__init__.py`除外)
    2.  删除 数据库
        -   sql> drop database mywebdb;
    3.  重新创建 数据库
        -   sql> create datebase mywebdb default charset...;
    4.  重新生成migrations里所有的 000?_XXXX.py
        -   python3 manage.py makemigrations
    5.  重新更新数据库
        -   python3 manage.py migrate

# 八、数据库的基本操作   

*   **操作方法**
    
    *   对象操作：ORM映射，用类代替SQL语句
    *   终端操作：django shell操作
    *   原生操作：MyModel.objects.raw(sql语句)
    
*   逻辑

    ```python
    database-mysql
    cache-redis
    
    使用
    model创建的类直接创建到database,orm使用
    get_redis_connection(name)链接缓存，redis.get操作
    ```

## 管理器对象操作

-   每个继承自 models.Model 的模型类，都会有一个 objects 对象被同样继承下来。这个对象叫管理器对象

-   数据库的增删改查可以通过模型的管理器实现

    ```python
    class MyModel(models.Model):
        ...
    MyModel.objects.create(...) # objects 是管理器对象```
    ```

### 增create

-   Django 使用一种直观的方式把数据库表中的数据表示成Python 对象

-   创建数据中每一条记录就是创建一个数据对象

    1.  MyModel.objects.create(关键字不用加引号属性1=值1, 属性2=值1,...)

        -   成功: 返回创建好的实体对象
        -   失败: 抛出异常

    2.  创建 MyModel 实例对象,并调用 save() 进行保存

        ```python
        obj = MyModel(属性=值,属性=值)
        obj.属性=值
        obj.save()
        无返回值,保存成功后,obj会被重新赋值
        ```

### 查

-   SQL语句中 WHERE 子句的内容。

-   filter(), exclude()，get()方法，查询谓词限制


#### 查询谓词

-   每一个查询谓词是一个独立的查询功能

1.  `__exact` : 等值匹配

    ```python
    Author.objects.filter(id__exact=1)
    # 等同于select * from author where id = 1
    ```

2.  `__contains` : 包含指定值

    ```python
    Author.objects.filter(name__contains='w')
    # 等同于 select * from author where name like '%w%'
    ```

3.  `__startswith` : 以 XXX 开始 w%

4.  `__endswith` : 以 XXX 开始 %w

5.  `__gt` : 大于指定值

    ```python
    Author.objects.filer(age__gt=50)
    # 等同于 select * from author where age > 50
    ```

6.  `__gte` : 大于等于

7.  `__lt` : 小于

8.  `__lte` : 小于等于

9.  `__in` : 查找数据是否在指定范围内

    ```python
Author.objects.filter(country__in=['中国','日本','韩国'])
    # 等同于 select * from author where country in ('中国','日本','韩国')
    ```
    
10.  `__range`: 查找数据是否在指定的区间范围内

     ```python
     # 查找年龄在某一区间内的所有作者
     Author.objects.filter(age__range=(35,50))
     # 等同于 SELECT ... WHERE Author BETWEEN 35 and 50;
     ```


#### 包含条件查询filter

-   方法: filter(属性1=值1, 属性2=值2)
-   作用:返回包含此 `条件` 的 全部的数据集
-   返回值:QuerySet容器对象,内部存放 MyModel 实例，没有数据返回空数组
-   说明:

    -   当多个属性在一起时为"与"关系，即当`Books.objects.filter(price=20, pub="清华大学出版社")` 返回定价为20 `且` 出版社为"清华大学出版社"的全部图书

#### 不包含条件筛选exclude

-   语法:exclude(条件)
    
-   作用:返回不包含此 `条件` 的 全部的数据集

-   示例:查询 `清华大学出版社，定价大于50` 以外的全部图书

    ```python
books = models.Book.objects.exclude(pub="清华大学出版社", price__gt=50)
    for book in books:
        print(book)
    ```

#### 查询指定的一条数据get

-   语法:get(条件)
-   作用：返回满足条件的唯一一条数据
-   返回值:MyModel 对象
-   说明:
    -   该方法只能返回一条数据
    -   查询结果多余一条数据则抛出,Model.MultipleObjectsReturned异常
    -   查询结果如果没有数据则抛出Model.DoesNotExist异常
    -   视图层和try配合防止客户端报错

#### 查询指定字段values

-   语法：values('字段')
-   返回值:只含有查询字段的QuerySet对象，字典＋列表
-   values_list()
-   返回元祖＋列表

#### 聚合查询(统计)aggregate/annotate

-   聚合查询是指对一个数据表中的一个字段的数据的部分或全部进行**统计**查询

1.  不分组聚合aggregate直接查

    -   统计所有数量，直接查询时候用，一共多少书

    -   语法:

        -   from django.db.models import *
        -   MyModel.objects.aggregate(结果变量名=聚合函数('列'))
        -   聚合函数: Sum, Avg, Count, Max, Min

    -   返回结果:{"结果变量名": 值}

2.  分组聚合annotate结果查

    -   每一类统计一个数量，查询出来结果用，一共多少出版社
    -   语法: 

        -   from django.db.models import *
        -   MyModel.objects.values（列）获得只有一个指定字段的QuerySet结果集
        -   QuerySet.annotate(结果变量名=聚合函数('列'))
    -   返回结果: QuerySet 结果集,内部存储结果的字典
    -   .values('查询列名')

### 改update

1.  修改单个实体的某些字段值的步骤:

    1.  查：得到要修改的单个对象（get单个，filter列表找单个）
    2.  改：对象.属性 = value
    3.  存：对象.save()
    
2.  批量修改
	1. 查：得到要修改的对象列表
     	2. 改：列表.update('列'=value) 

### 删delete

1.  查：获得单个对象/结果集对象列表
2.  删：对象/对象集.delete()

### F对象 - F()

1.  作用:

    *   不获取字段值直接操作字段+update
    *   字段之间的比较+filter+谓词

2.  优点

    -   一个F对象代表一个模型类的一个字段所有数据
    -   字段值在不加载到内存中的情况下直接在数据库服务器端进行操作
    -   用于对每一个数据进行操作，代替循环
    -   内部加锁进行操作，防止脏读，解决并发

3.  语法:

    ```python
    from django.db.models import F
    # 不获取字段值直接操作字段
    models.类.objects.all().update(列=F(列)+10)
    # 字段之间的比较
    books = models.Book.objects.filter(market_price__gt=F('price'))
    ```

### Q对象 - Q()

1.  作用
    *   获取查询结果集使用复杂的逻辑或  `|` 、 逻辑非 `~` 等操作filter+谓词/=
2.  语法

```python
from django.db.models import Q
models.Book.objects.filter(Q(price__lt=20)|Q(pub="清华大学出版社"))

Q(条件1)|Q(条件2)  # 条件1成立或条件2成立
Q(条件1)&Q(条件2)  # 条件1和条件2同时成立
Q(条件1)&~Q(条件2)  # 条件1成立且条件2不成立
```

## Django shell创建数据方法

-   利用 Django Shell 可以代替编写View的代码来进行直接操作

-   在Django Shell 下只能进行简单的操作，不能运行远程调式

-   启动方式:

    ```shell
    $ python3 manage.py shell
    from app import models.类
    ```

### 查询数据

-   数据库的查询需要使用管理器对象进行 

-   MyModel.objects.方法（参数）

-   关键字全部需要''

    | 方法      | 说明                              |
    | --------- | --------------------------------- |
    | all()     | 查询全部记录,返回QuerySet查询对象 |
    | get()     | 查询符合条件的单一记录            |
    | filter()  | 查询符合条件的多条记录            |
    | exclude() | 查询符合条件之外的全部记录        |
    | ...       |                                   |

1.  查询返回所有数据

    -   方法: all()
    -   返回值: QuerySet容器对象(类列表),内部存放 MyModel 对象
    -   注意
        -   可以book[索引]访问数据，但内部不是切片，而是select limit语句
        -   惰性查询，需要时才进行查询
    -   在模型类中重写 `def __str__(self): ` 方法可以将自定义默认的字符串

    ```python
    class Book(models.Model):
        title = ...
        def __str__(self):
            return "书名: %s, 出版社: %s, 定价: %s" % (self.title, self.pub, self.price)
    <QuerySet [<Book: 异类 中信 30.00 28.00>, <Book: 三体 机械 60.00 40.00>, <Book: 百科 机械 90.00 60.00>, <Book: 飘 机械 90.00 60.00>, <Book: 呐喊 机械 90.00 60.00>]>
    ```

2.  查询返回指定列(字典表示)

    -   方法: values('列1', '列2')

    -   返回值: QuerySet

        -   容器内存字典
        -   \[索引][key]
        -   select 列1,列2 from xxx	

3.  查询返回指定列（元组表示)

    -   方法:values_list('列1','列2')
    -   作用:返回元组形式的查询结果
    -   返回值: QuerySet容器对象,
        -   容器内存元组
        -   \[索引][索引]
        -   select 列1,列2 from xxx

4.  排序查询

    -   方法:order_by('-列','列')

    -   可以在任意返回结果为queryset的方法后直接调用all().order_by

    -   用SQL 语句的ORDER BY 子句对查询结果进行根据某个字段选择性的进行排序

    -   默认升序,'-列'表示降序

## 原生的数据库操作方法

1.  语法：

    MyModel.objects.raw(sql语句)

2.  返回值：

    RawQuerySet 集合对象 【只支持基础操作，比如循环】

-   使用django中的游标cursor对数据库进行 增删改操作

    -   在Django中使用UPDATE,DELETE非查询语句必须使用游标进行操作

    -   使用步骤:

        1.  导入cursor所在的包

        2.  用创建cursor类的构造函数创建cursor对象

            ```python
            from django.db import connection
            with connection.cursor() as cur:
                cur.execute('执行SQL语句')
            ```


## admin 后台数据库管理

-   使用步骤:

    1.  创建后台管理帐号:


        # shell
        $ python3 manage.py createsuperuser
        Username (leave blank to use 'tarena'): tarena  # 此处输入用户名
            Email address: laowei@tedu.cn  # 此处输入邮箱
            Password: # 此处输入密码(密码要复杂些，否则会提示密码太简单)
            Password (again): # 再次输入重复密码
    
    2.  用注册的帐号登陆后台管理界面
        http://127.0.0.1:8000/admin

### 添加自定义类数据表

-   自定义模型类admin后台管理界中显示和管理

-   配置步骤:


    # admin.py
    from . import models
    from django.contrib import admin
        admin.site.register(models.类)
        
    @admin.register(EqType)
    class EqTypeAdmin(admin.ModelAdmin)
### 数据的展现形式

-   在admin后台管理数据库中对自定义的数据记录都展示为 `XXXX object` 类型的记录

-   在用户自定义的模型类中可以重写 `def __str__(self):` 方法解决显示问题


    # models.py
    class Book(models.Model):
        ...
        def __str__(self):
            return "书名" + self.title

### 模型管理器类

-   调整admin界面和每个自定义类的可使用功能

-   使用：

    ```python
    # admin.py
    from django.contrib import admin
    from . import models
    class 类名Manager(admin.ModelAdmin):
        list_display = ['id', 'title', 'price', 'market_price']
    admin.site.register(models.类名, 类名Manager)
    ```

-   模型管理器类ModelAdmin中实现的高级管理功能（xxx = [xxx,yyy,zzzz]列表形式，多个字段）

    1.  list_display=['字段1','字段2'] 修改列表表头显示字段
    2.  list_display_links 可以链接到对象的“更改”页面的字段
    3.  list_filter 修改列表页面右侧栏中的过滤器(分组字段) 
    4.  search_fields 列表页面上的搜索框去哪些字段查询
    5.  list_editable 允许在更改列表页面上直接进行编辑（不能和list_display_links同时存在）。

### 数据库表管理

1.  修改模型类字段的显示名字

    -   模型类各字段的第一个参数为 verbose_name,此字段显示的名字会在后台数据库管理页面显示

    -   通过 verbose_name 字段选项,修改显示名称示例如下：

        ```python
        title = models.CharField(
            max_length = 30,
            verbose_name='显示名称'
        )
        ```

2.  通过Meta内嵌类 定义模型类的属性及展现形式

    ```python
class Book(models.Model):
        title = CharField(....)
    class Meta:
            1. db_table = '数据表名'
                - 该模型所用的数据表的名称。(设置完成后需要立马更新同步数据库)
            2. verbose_name = '单数名'
                - 给模型对象的一个易于理解的名称(单数),用于显示在/admin管理界面中（不需要migrate）
            3. verbose_name_plural = '复数名'
                - 该对象复数形式的名称(复数),用于显示在/admin管理界面中（不需要migrate）
    ```

## 表关联关系映射

-   常用的表关联方式有三种:一对一映射，一对多映射，多对多映射

#### 一对一映射（models.OneToOneField，objects.create(B属性名=a对象)）

1.  语法

    ```python
    # 1. 创建类：OneToOneField
    class A(model.Model):
    class B(model.Model):
        属性 = models.OneToOneField(小写A类名)
    # 2. 创建：创建一，另一属性等于一对象
    a = A.objects.create()
    b = B.objects.create(,B属性名=a对象)
    # 3. 查询：属性访问
    a.b.字段
    b.a.字段
    
    # 备注
    外键属性关联 ---> django
    属性 = 对象  
    author = author1，使用时去author查
    外键值关联 ---> mysql
    字段 = 字段 
    author_id = author1.id，赋值直接用
    少一次查询
    ```


-   应用:
    -   第三方授权登录，本身自己的表user和其他app表
    -   冷热数据分类
           - 热数据--高频访问数据
           - 冷数据--低频访问数据

#### 一对多映射（models.ForeignKey，objects.create(B属性名=a对象)）

1.  语法

    ```python
    # 1. 创建类：ForeignKey
    class A(model.Model):
    class B(model.Model):
    	属性 = models.ForeignKey("一"的模型类...)
    # 2. 创建：创建一，创建多属性等于一对象
    a = A.objects.create()
    b1 = B.objects.create(B属性名=a对象)
    b2 = B.objects.create(B属性名=a对象)
    #方案1 用类属性关联
    # pub = Publisher.objects.create(name='清华大学出版社')
    # Book.objects.create(title='经济学通识', publisher=pub)
    # Book.objects.create(title='经济学非通识', publisher=pub)
    #方案2 使用外键值 关联
    # pub = Publisher.objects.create(name='中信出版社')
    # Book.objects.create(title='爆裂', publisher_id=pub.id)
    # 3. 查询a一b多
    多查一：属性访问
    b1.a.字段
    b2.a.字段
    一查多：多_set.all()属性
    a = models.A.objects.get()获得a对象
    b_list = a.b_set.all()
    或
    b1 = models.B.objects.filter(字段=a对象)
    b2 = models.C.objects.filter(字段=a对象)
    # 通过xxx_set 获取pub1对应的多个Book数据对象 book_set相当于特定的objects，object的方法set都有
    ```

2.  外键类ForeignKey 

    -   构造函数:

        ```python
        ForeignKey(to, on_delete, **options)
        ```

    -   常用参数:

        -   on_delete

            1.  models.CASCADE  级联删除，默认模式

                *   django在执行删除时，默认先删除对应多表中的数据，最后再删除一表中的数据

                *   mysql本身不是CASCADE，mysql默认级联删除为Restrict保护模式,此模式下删除主表会报错

            2.  models.PROTECT 抛出ProtectedError 以阻止被引用对象的删除;

            3.  SET_NULL 设置ForeignKey null；需要指定null=True

            4.  SET_DEFAULT  将ForeignKey设置为其默认值；必须设置ForeignKey的默认值。

        -   `**options` 可以是常用的字段选项如:

            1.  null
    2.  unique等
            3.  ...

#### 多对多映射（models.ManyToManyField）

1.  语法

    ```python
    # 1. 创建类：ManyToManyField
    class A(models.Model):
    class B(models.Model):
        ...
        属性 = models.ManyToManyField(A)
    # 2. 创建:创建11，12，通过11创建21，12添加21
    a1 = A.objects.create()
    a2 = A.objects.create()
    b1 = a.b_set.create()
    a2.b_set.add(b1)
    b2 = a.b_set.create()
    a2.b_set.add(b2)
    # 3. 查询：
    data/data_list = a.b_set.fun
    fun:
    author.book_set.all()
    author.book_set.filter()
    author.book_set.clear()
    ```

2.  注意

    会自动创建第三张表，关系表，用id等进行对应

# 九、缓存Cache

*   目的

    视图渲染有一定成本，对于低频变动的页面可以考虑使用缓存技术，减少实际渲染次数

*   思想

```python
given a URL, try finding that page in the cache
if the page is in the cache:
    return the cached page
else:
    generate the page
    save the generated page in the cache (for next time)
    return the generated page
```

*   使用缓存场景：
    *   博客列表页
    *   电商商品详情页
    *   缓存导航及页脚

## 设置

*   注意

    cache_page同时启用服务器缓存和浏览器缓存cache-page和expires,此时浏览器是否缓存依赖于浏览器配置

1.  数据库缓存

```python
# setting.py
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.db.DatabaseCache',
        'LOCATION': 'my_cache_table',
        'OPTIONS':{
            'MAX_ENTRIES': 300,
            'CULL_FREQUENCY': 2,（清理1/2）
        }
    }
}
# shell
$ python manage.py createcachetable
$ cache_key value expires
```

2.  文件系统缓存

```python
# setting.py
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.filebased.FileBasedCache',
        'LOCATION': '/var/tmp/django_cache',#这个是文件夹的路径
        #'LOCATION': 'c:\test\cache',#windows下示例
    }
}
```

3.  本地内存缓存

```python
# setting.py
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.locmem.LocMemCache',
        'LOCATION': 'unique-snowflake'
    }
}
```

## 使用

-   在视图View中使用（缓存整个）

    ```python
    from django.views.decorators.cache import cache_page
    @cache_page(30)  -> 单位s
    def my_view(request):
        ...
    ```

-   在路由URL中使用

    ```python
    from django.views.decorators.cache import cache_page
    urlpatterns = [
        path/url('foo/', cache_page(60)(my_view)),
    ]
    ```

-   在模板中使用（还是会走视图函数，只在模板中缓存部分）

    ```python
    {% load cache %}
    {% cache 500 sidebar request.user.username %}cache 时间 key set_key  
        .. sidebar for logged in user ..
    {% endcache %}
    ```

## 浏览器中的缓存（不用再次向服务器请求）    

*   强缓存（动态资源）:

    *   不会向服务器发送请求，直接从缓存中读取资源
    *   Expires
        *   缓存过期时间，用来指定资源到期的时间，是服务器端的具体的时间点
        *   Expires=max-age + 请求时间
        *   HTTP/1.0 的产物，受限于本地时间，如 果修改了本地时间，可能会造成缓存失效

    *   Cache-Control 
        *   在HTTP/1.1中，Cache-Control主要用于控制网页缓存。
        *   不受本地时间控制，按UTC

    *   Expires  VS Cache-Control
        *   expires 1.0协议缓存头-绝对时间，cc 1.1新版本缓存头-相对时间
        *   根据http要求，缓存头响应时，会同时将expires旧头和cache-control新头返回给浏览器但是，浏览器去的最新版本头缓存[结合浏览器支持的http协议]

*   协商缓存（静态资源）

    *   Last-Modified和if-Modified-since

        *   强制缓存失效后，浏览器携带缓存标识向服务器发起请求，服务器根据缓存标识决定是否使用缓存的过程

        *   服务器响应返回静态文件响应头包含Last-Modified（资源的最近一次修改时间）

        *   浏览器接到服务器相应后检查Last-Modified，有则储存到浏览器缓存中，缓存时间根据各浏览器不同

            火狐:(现在时间-上次时间)/10作为当次缓存时间

        *   当浏览器再次发送该资源请求时，优先检查缓存中是否已经有缓存数据，

            *   有缓存，未过期，不向服务器发送请求直接返回

            *   有缓存，已失效，发送‘协商请求’到服务器，请求头中包含if -Modified-since，值为last-modified

                *   服务器接到if -Modified-since，检查是否和该资源文件的最近修改时间相等

                    相等：证明资源没变，返回304响应状态码

                    不相等，证明资源有修改过，返回200响应状态码，且响应具体新的资源内容

            *   无缓存，正常发送请求至服务器[F5+ctrl绝对不走缓存]，取得最新资源

        *   缺点：只能精确到秒，容易发生单秒内多次修改，检测不到

    *   ETag和If-None-Match
    
*   流程和Last-Modified相同，但返回资源文件唯一标识(由服务器hash生成)，任何改变都能捕捉更精确
    
*   Last-Modified VS  ETag  
    
    *   精度不一样 -  Etag 高
    
    *   性能上 - Last-Modifi 高
    
        *   优先级 - Etag 高

# 十、组件

## 中间件 Middleware

-   中间件是 Django 请求/响应处理的钩子框架。它是一个轻量级的、低级的“插件”系统，用于全局改变 Django 的输入（请求）或输出（响应），每个中间件组件负责做一些特定的功能。

-   注册中间件:

    ```python
    # file : settings.py
    MIDDLEWARE = [
        ...
           ]
    ```

-   编写中间件:

    -   中间件类须继承自 `django.utils.deprecation.MiddlewareMixin`类

    -   中间件类须实现下列五个方法中的一个或多个:

        -   `def process_request(self, request):` 执行路由之前被调用，在每个请求上调用，返回None(继续传递)或HttpResponse对象（请求停止并返回，拦截请求流程并响应服务器） 

        -   `def process_view(self, request, callback, callback_args, callback_kwargs):` 调用视图之前被调用，在每个请求上调用，返回None或HttpResponse对象

        -   `def process_response(self, request请求, response--views的返回值):` 所有响应返回浏览器  被调用，在每个请求上调用，返回HttpResponse对象（可以过滤使用response，可以对比request和response）

        -   `def process_exception(self, request, exception):` 当处理过程中抛出异常时调用，返回一个404等HttpResponse对象

        -   `def process_template_response(self, request, response):` 在视图刚好执行完毕之后被调用，在每个请求上调用，返回实现了render方法的响应对象（过滤页面显示内容）

            ```python
            # file : middleware/mymiddleware.py
            from django.http import HttpResponse, Http404
            from django.utils.deprecation import MiddlewareMixin
            
            class MyMiddleWare(MiddlewareMixin):
                def process_request(self, request):
                    print("中间件方法 process_request 被调用")
                def process_view(self, request, callback, callback_args, callback_kwargs):
                    print("中间件方法 process_view 被调用")
                def process_response(self, request, response):
                    print("中间件方法 process_response 被调用")
                    return response
                def process_exception(self, request, exception):
                    print("中间件方法 process_exception 被调用")
                def process_template_response(self, request, response):
                    print("中间件方法 process_template_response 被调用")
                    return response
            ```

    -    中间件中的大多数方法在返回None时表示忽略当前操作进入下一项事件，当返回HttpResponese对象时表示此请求结束，直接返回给客户端

    -   多个中间件同时注册，上下下上洋葱式执行

        ```python
        browser
        --process_request1--process_request2
        --urls.py
        --process_view1--process_view2
        --views.py
        --(process_exception，process_template_response)
        --process_response2--process_response1
        --browser
        ```

-   案例
-   用中间件实现某个IP地址请求次数限制（特定地址限制用装饰器更合适，中间件有全局性）
    
    -   要求一分钟内每个ip最多访问xxx次{ip:{count,时间戳}

### 应用：跨站请求伪造 CSRF

-   跨站请求伪造攻击

    -   某些恶意网站上包含链接、表单按钮或者JavaScript，它们会利用登录过的用户在浏览器中的认证信息cookie，试图在其他网站上利用用户cookie完成某些操作，这就是跨站请求伪造(CSRF，即Cross-Site Request Forgey)。 

-   解决：

    -   低级：借助特殊状态码验证，CSRF中间件和模板标签提供对跨站请求伪造简单易用的防护
    -   高级：settings.py中MIDDLEWARE 中的 `django.middleware.csrf.CsrfViewMiddleware` 的中间件

-   解决请求拒绝:

    1.  取消settings.py中MIDDLEWARE 中csrf 验证(不推荐)

    2.  通过验证 csrf_token 验证

        ```python
        # settings.py
        开启MIDDLEWARE中csrf验证,开启验证
        
        # html
        {% csrf_token %}
        # 添加该标签post时会有验证，用于和cookie中的验证
        
        # 验证
        cookies: 暗号1 - csrftoken
        form表单：暗号2 - csrfmiddlewaretoken
        同时提交，核对正确才能说明登陆网站页面提交的信息
        ```
    

## 单元测试

```python
# tasts.py
from django.test import TestCase

class xxxTest (TestCase):
    def text_xxx()
    
# shell
$ python3 manage.py test [packet/module/class/fun]
```

## 分页Paginator

-   Django提供了django.core.paginator模块Paginator类可以方便的实现分页功能，在每个页中显示部分数据。

    ```python
    # views.py
    from django.core.paginator import Paginator
    def fun(request):
        paginator = Paginator(对象列表, 每页数据个数)
        page = p.page(页码信息)
        
    # paginator是Paginator对象
    属性：
    -   count：需要分类数据的对象总数
    -   num_pages：分页后的页面总数
    -   page_range：从1开始的range对象, 用于记录当前面码数
    -   per_page 每页数据的个数
    方法:
    -   page(页码信息):返回page对象
        如果提供的页码不存在，抛出InvalidPage异常
    # page是Page对象，可迭代
    属性：
    -   object_list：当前页所有数据对象的列表
    -   number：当前页的序号，从1开始
    -   paginator：当前page对象相关的Paginator对象
    方法：
    -   has_next()：如果有下一页返回True
    -   has_previous()：如果有上一页返回True
    -   has_other_pages()：如果有上一页或下一页返回True
    -   next_page_number()：返回下一页的页码，如果下一页不存在，抛出InvalidPage异常
    -   previous_page_number()：返回上一页的页码，如果上一页不存在，抛出InvalidPage异常
    -   len()：返回当前页面对象的个数
    ```

-   分页示例:


    # views.py
    from django.core.paginator import Paginator
    def book(request):
        bks = models.Book.objects.all()
        paginator = Paginator(bks, 10)
        print('当前对象的总个数是:', paginator.count)
        print('当前对象的面码范围是:', paginator.page_range)
        print('总页数是：', paginator.num_pages)
        print('每页最大个数:', paginator.per_page)
    
        cur_page = request.GET.get('page', 1)  # 得到默认的当前页
        page = paginator.page(cur_page)
        return render(request, 'bookstore/book.html', locals())
    ```

```html
# html
<html>
    <head>
        <title>分页显示</title>
    </head>
    <body>
    {% for b in page %}
        <div>{{ b.title }}</div>
    {% endfor %}
    
    {# 分页功能 #}
    {# 上一页功能 #}
    {% if page.has_previous %}
    <a href="{% url 'book' %}?page={{ page.previous_page_number }}">上一页</a>
    {% else %}
    上一页
    {% endif %}
    
    {% for p in paginator.page_range %}
        {% if p == page.number %}
            {{ p }}
        {% else %}
            <a href="{% url 'book' %}?page={{ p }}">{{ p }}</a>
        {% endif %}
    {% endfor %}
    
    {#下一页功能#}
    {% if page.has_next %}
    <a href="{% url 'book' %}?page={{ page.next_page_number }}">下一页</a>
    {% else %}
    下一页
    {% endif %}
    </body>
    </html>
```

## 文件操作

*   文件上传

    ```python
    # HTML
    <form enctype="multipart/form-data" method='post'>
    <input type="file" name="xxx">
    
    # shell：创建保存资源文件夹
    $ mkdir -p static/files
    # settings.py记录上传文件的位置
    MEDIA_ROOT = os.path.join(BASE_DIR, 'static/files')
    
    # views.py
    file=request.FILES['xxx']
    -  file.name 文件名
    -  file.file 文件的字节流数据
    with open(filename, 'wb') as f:
       data = file.file.read()
       f.write(data)  
    ```

*   文件下载

    ```python
    import csv
    from django.http import HttpResponse
    from .models import Book
    
    def make_csv_view(request):
        response = HttpResponse(content_type='text/csv')
        response['Content-Disposition'] = 'attachment; filename="mybook.csv"'
        writer = csv.writer(response)
        for b in all_book:    
        	writer.writerow([b.id, b.title])
        return response
    ```

## 用户认证Admin

-   Django自带用户认证系统。 它处理用户账号、组、权限以及基于cookie的用户会话。

-   作用:

    1.  添加普通用户和超级用户
    2.  修改密码

-   User模型类

-   位置: `from django.contrib.auth.models import User`

-   默认user的基本属性有：

    | 属性名       | 类型                                                         | 是否必选 |
    | ------------ | ------------------------------------------------------------ | -------- |
    | username     | 用户名                                                       | 是       |
    | password     | 密码                                                         | 是       |
    | email        | 邮箱                                                         | 可选     |
    | first_name   | 名                                                           |          |
    | last_name    | 姓                                                           |          |
    | is_superuser | 是否是管理员帐号(/admin)                                     |          |
    | is_staff     | 是否可以访问admin管理界面                                    |          |
    | is_active    | 是否是活跃用户,默认True。一般不删除用户，而是将用户的is_active设为False。 |          |
    | last_login   | 上一次的登录时间                                             |          |
    | date_joined  | 用户创建的时间                                               |          |

-   数据库表现形式

    ```sql
    mysql> use myauth;
    mysql> desc auth_user;
    +--------------+--------------+------+-----+---------+----------------+
    | Field        | Type         | Null | Key | Default | Extra          |
    +--------------+--------------+------+-----+---------+----------------+
    | id           | int(11)      | NO   | PRI | NULL    | auto_increment |
    | password     | varchar(128) | NO   |     | NULL    |                |
    | last_login   | datetime(6)  | YES  |     | NULL    |                |
    | is_superuser | tinyint(1)   | NO   |     | NULL    |                |
    | username     | varchar(150) | NO   | UNI | NULL    |                |
    | first_name   | varchar(30)  | NO   |     | NULL    |                |
    | last_name    | varchar(30)  | NO   |     | NULL    |                |
    | email        | varchar(254) | NO   |     | NULL    |                |
    | is_staff     | tinyint(1)   | NO   |     | NULL    |                |
    | is_active    | tinyint(1)   | NO   |     | NULL    |                |
    | date_joined  | datetime(6)  | NO   |     | NULL    |                |
    +--------------+--------------+------+-----+---------+----------------+
    11 rows in set (0.00 sec)
    ```

### auth基本模型操作:

-   创建用户

    -   创建普通用户create_user

        ```python
        from django.contrib.auth import models
        user = models.User.objects.create_user(username='用户名', password='密码', email='邮箱',...)
        # 密码输入明文，自动加密加盐
        user.save()
        ```

    -   创建超级用户create_superuser

        ```python
        from django.contrib.auth import models
        user = models.User.objects.create_superuser(username='用户名', password='密码', email='邮箱',...)
        user.save()
        ```
    
-   删除用户(伪删除)

    ```python
    from django.contrib.auth import models
    try:
        user = models.User.objects.get(username='用户名')
        user.is_active = False  # 记当前用户无效
        user.save()
        print("删除普通用户成功！")
    except:
        print("删除普通用户失败")
    return HttpResponseRedirect('/user/info')
    ```

-   修改密码set_password

    ```python
    from django.contrib.auth import models
    try:
        user = models.User.objects.get(username='xiaonao')
        user.set_password('654321')
        user.save()
        return HttpResponse("修改密码成功！")
    except:
        return HttpResponse("修改密码失败！")
    ```

-   检查密码是否正确check_password

    ```python
    from django.contrib.auth import models
    try:
        user = models.User.objects.get(username='xiaonao')
        if user.check_password('654321'):  # 成功返回True,失败返回False
            return HttpResponse("密码正确")
        else:
            return HttpResponse("密码错误")
    except: 
        return HttpResponse("没有此用户！")
    ```

## 电子邮件发送

-   利用QQ邮箱发送电子邮件
-   django.core.mail 子包封装了 电子邮件的自动发送SMT协议
-   前其准备:
    1.  申请QQ号
    2.  用QQ号登陆QQ邮箱并修改设置
        -   用申请到的QQ号和密码登陆到 <https://mail.qq.com/>
        -   修改 `QQ邮箱->设置->帐户->“POP3/IMAP......服务”`
-   settings.py 设置

```python
# 发送邮件设置
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend' # 固定写法
EMAIL_HOST = 'smtp.qq.com' # 腾讯QQ邮箱 SMTP 服务器地址
EMAIL_PORT = 25  # SMTP服务的端口号
EMAIL_HOST_USER = 'xxxx@qq.com'  # 发送邮件的QQ邮箱
EMAIL_HOST_PASSWORD = '******'  # 在QQ邮箱->设置->帐户->“POP3/IMAP......服务” 里得到的在第三方登录QQ邮箱授权码
EMAIL_USE_TLS = True  # 与SMTP服务器通信时，是否启动TLS链接(安全链接)默认false
```

视图函数中

```python
from django.core import mail
mail.send_mail(
            subject,  #题目
            message,  # 消息内容
            from_email,  # 发送者[当前配置邮箱]
            recipient_list=['xxx@qq.com'],  # 接收者邮件列表
            )
from django,core.validators import validate_email校验器
```

# 十二、项目部署

## 1 项目迁移

1. 在安装机器上安装和配置同版本的数据库,python及依懒的包

    ```shell
    导出当前模块数据包的信息:
    $ pip3 freeze > package_list.txt
    导入到另一台新主机
    $ pip3 install -r package_list.txt
    ```

2. django 项目迁移

    ```shell
    sudo scp /home/tarena/django/mysite1 root@88.77.66.55:/home/root/xxx
    请输入root密码：
    ```

## 2 WSGI配置

-   Web服务器网关接口，实现WSGI协议、http等协议
-   Nginx -- uWSGI -- Django -- uWSGI -- Nginx
    -   Nginx中HttpUwsgiModule与uWSGI服务器进行交换。
    -   uWSGI调用django触发请求和响应，字典方式给django参数纯方法级调用，不是数据传输
-   uwsgi启动不会像runserver一样自动重载，django需要stop-start重启uWSGI
-   当uwsgi 启动后，django项目的程序已变成后台守护进程,不需要runserver，在关闭当前终端时此进程也不会停止。

### uWSGI步骤 (ubuntu 18.04 )

- 安装uWSGI

    ```shell
    $ apt-get install build-essential python-dev
    $ sudo pip3 install uwsgi
    ```

- 配置uWSGI

    - 添加配置文件 `项目文件夹/uwsgi.ini`

        -   如: mysite1/mysite1/uwsgi.ini

        ```ini
        [uwsgi]
        # 通讯方式及端口
        socket=127.0.0.1:8000　　有nginx　　
        # http=127.0.0.1:8000　　没有nginx，uwsgi独自做服务器
        
        # 项目当前工作目录
        chdir=/home/tarena/.../my_project 这里需要换为项目文件夹的绝对路径，最外层文件夹目录
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

* 运行uWSGI
    * 启动 uwsgi

    ```shell
    $ cd 项目文件夹
    $ sudo uwsgi --ini 项目文件夹/uwsgi.ini
    ```

    * 停止 uwsgi

    ```shell
    $ cd 项目文件夹
    $ sudo uwsgi --stop uwsgi.pid
    ```

* 确认状态：

    ```shell
    $ ps aux|grep 'uwsgi'  查看当前进程
    $ vim uwsgi.pid        查看主进程pid
    
    ps显示的最小的ini（主进程）要和pid文件中记录的相同
    不同说明重复开启，记录与实际不一致，需要kill进程重新启动
    ps -ef | grep 'uwsgi' | grep -v grep |awk '{print $2}' | xargs kill -9 关闭所有进程
    ```

## 3 nginx 配置

-   配置 nginx 反向代理服务器
-   用nginx 配置静态文件路径,解决静态路径问题
-   Nginx是轻量级的高性能Web服务器，提供了诸如HTTP代理（正向代理）和反向代理、负载均衡、缓存等一系列重要特性，在实践之中使用广泛，C语言超并发。
-   Nginx单独部署，uwsgi和django在一起
-   Nginx中server{location url{server_path}}给执行路由指定静态文件地址
-   nginx压缩　－　传输　－　浏览器解压
### nginx步骤 (ubuntu 18.04 )

-   安装 nginx 
    
    ```shell
    $ sudo apt install nginx
    ```
- 配置 nginx 

    ```shell
    $ sudo su
    $ vim /etc/nginx/sites-enabled/default
    server {
        ...
    48    location / {
        	# 80请求重定向到127.0.0.1的8000端口
            uwsgi_pass 127.0.0.1:8000;  
            # 将所有的参数转到uwsgi下
            include /etc/nginx/uwsgi_params; 
            #try_files $uri $uri/=404;
        }
        location /static {
            # root 第一步创建文件夹的绝对路径,如:
             root /home/tarena/项目名_static;          
        }
        ...
    }
    # 检查语法是否正确
    $ sudo nginx -t
    # 重启服务
    $ sudo service nginx start|stop|restart|status
    ```

- 配置nginx 静态文件路径

    -   创建新路径-主要存放Django所有静态文件 如： /home/tarena/项目名_static/

    -   在Django中添加新配置

        ```python
        # settings.py 
        STATIC_ROOT = '/home/tarena/项目名_static/static  
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

*   nginx
    *   异步非阻塞多进程，高并发性更好
    *   轻量级，占用内存资源更少，配置简洁，模块化设计
    *   更适合负载均衡和反向代理
*   Apache
    *   同步多进程，稳定性更好
    *   模块多，功能全，reweite更强

## 邮件告警

-   当正式服务器上代码运行有报错时，可将错误追溯信息发至指定的邮箱

- 配置如下 settings.py

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

    报错邮件中会显示一些错误的追踪，这些错误追踪中会出现如 password等敏感信息，Django已经将配置文件中的敏感信息 过滤修改为   多个星号，但是用户自定义的视图函数需要用户手动过滤敏感信息

    *   views.py

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

## 美化

```shell
# shell
pip3 install django-simpleui -i https://pypi.tuna.tsinghua.edu.cn/simple/
#settings.py
app=[
'simpleui',
……
]
```

