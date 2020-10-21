网络爬虫概述
 
*   不同语言爬虫对比
 
    *   Python ：请求模块、解析模块丰富成熟,强大的Scrapy网络爬虫框架
    *   PHP ：对多线程、异步支持不太好
    *   JAVA：代码笨重,代码量大
    *   C/C++：虽然效率高,但是代码成型慢
 
*   爬虫协议
 
    robots协议: 网站通过robots协议告诉搜索引擎哪些页面可以抓取（君子协议）/robots.txt
 
# 爬虫结构
 
*   确定URL地址
*   打开源码搜索关键字
    *   可以搜索到：静态--直接爬取
    *   搜索不到：动态，抓包找到js数据，向js请求地址发起请求
*   发出请求并得到网站的响应
*   从响应内容中提取所需数据
    *   所需数据,保存
    *   有其他需要继续跟进URL地址,继续发请求，如此循环
        *   利用指纹进行增量爬取
 
```python
# 程序结构
class xxxSpider(object):
    def __init__(self):
        # 定义常用变量,url,headers及计数等
 
    def get_html(self):
        # 获取响应内容函数,使用随机User-Agent
 
    def parse_html(self):
        # 使用正则表达式来解析页面，提取数据
 
    def save_html(self):
        # 将提取的数据按要求保存，csv、MySQL数据库等
 
    def run(self):
        # 主函数，用来控制整体逻辑
 
if __name__ == '__main__':
    spider = xxxSpider()
    spider.run()
```
 
# 请求准备
 
```python
from urllib import parse
 
# 1. 给URL地址中查询参数进行编码
params = {'wd':'美女','pn':'50'}
params = parse.urlencode(params)
# 编码后: params结果: 'wd=%E7%BE%8E%E5%A5%B3&pn=50'
# parse.quote(str)
params = parse.quote(str)
# 解码 result = parse.unquote(string)
 
# 2. 拼接地址 字符串相加/字符串格式化（占位符 %s）/format()
url = 'https://www.baidu.com/s?wd={}'
url = url.format(params)
```
 
# 响应获取
 
## urllib
 
```python
from urllib import request
 
# 3. 伪装头，创建请求对象 Request()
url = 'http://httpbin.org/get'
headers = {'User-Agent':'Mozilla/5.0'}
req = request.Request(url=url,headers=headers)
# 4. 发起请求获取响应对象 urlopen()
resp = request.urlopen(req,timeout)
html = resp.read().decode('utf-8')
 
resp.read()           - 响应内容（字节串）
resp.read().decode()  - 响应内容（字符串）
resp.geturl()         - 返回实际数据的URL地址
resp.getcode()        - HTTP响应码
```
 
## request
 
### get()
 
```python
# 1. 发起请求,并获取响应对象
res = requests.get(
    url = url,
    params = {},
    auth = ('username','password'),
    headers = headers,
    verify=False,
    proxies={'协议':'协议://IP:端口号'}
)
 
# Tips
自动对param编码并和url拼接，省去parse，format
每一页需要验证才需要auth
 
# headers：请求头
根据网站需求的请求头进行配置
需要登陆的页面：登陆一次查看并保存{'Cookie':'xxxxxx'}
 
# verify：证书验证
True(默认):检查证书认证
False（常用）: 忽略证书认证
 
# proxies:代理，隐藏自身真实IP,避免被封
# 参数类型
普通代理  {'协议://IP:端口号'}
私密+独享代理  {'协议://用户名:密码@IP:端口号' }
# 代理类型
1) 高匿代理: Web端只能看到代理IP
2) 普通代理: Web端知道有人通过此代理IP访问，但不知用户真实IP 
3) 透明代理: Web能看到用户真实IP，也能看到代理IP
 
# res:响应对象属性
1、encoding ：响应字符编码
   res.encoding = 'utf-8'
2、text ：字符串 -- 文本
3、content ：字节流 -- 音视图
4、status_code ：HTTP响应码
5、url ：实际数据的URL地址
```
 
### post()
 
```python
# 需要提交数据的操作
response = requests.post(url,data=data,headers=headers)
# data:Form表单数据字典
```
 
# 解析响应
 
## re
 
```python
import re
pattern = re.compile(r'正则表达式',re.S)
r_list = pattern.findall(html)
# 分组解析()，返回列表套元组
```
 
## lxml
 
```python
from lxml import etree
parse_html = etree.HTML(res.text)
r_list = parse_html.xpath('xpath表达式')
 
# //ul[@class="CarList"]/li/@href
#  /  结点
# []  条件li
#  @  属性
# 结点1取多个元素，xpath获取到结点1，遍历进行二级解析
# 多条件匹配 xpath1 | xpath2 | xpath3
 
# 函数
# contains() ：匹配属性值中包含某些字符串节点
  //li[contains(@id,"car_")]
# text() ：获取节点的文本内容
  //ul[@class="CarList"]/p/text()
```
 
 
 
# 数据持久化
 
## 普通文件
 
```python
# 文本
with open('xxx.txt','wb') as f:
    f.write(res.text)
# 音视频图片
with open('xxx.jpg','wb') as f:
    f.write(res.content)
```
 
## csv
 
```python
import csv
# 储存
with open('film.csv','w') as f:
    writer = csv.writer(f)
    writer.writerow([])
    writer.writerows(rows)
# 读取
with open('test.csv')as f:
    f_csv = csv.reader(f)
    for row in f_csv:
        print(row[1])
 
response['Content-Disposition'] = 'attachment;filename="book.csv"'
 
```
 
## MySQL
 
```python
import pymysql
db = pymysql.connect('localhost','root','123456','maoyandb',charset='utf8')
cursor = db.cursor()
 
ins = 'insert into filmtab values(%s,%s,%s)'
# 1. 单条插入表记录之 excute() 方法
cursor.execute(ins,['霸王别姬','张国荣','1993'])
# 2. 多条插入表记录之 excutemany() 方法 - 高效且节省资源
cursor.executemany(ins,[('大话1','周','1993'),('大话2','周','1994')])
 
db.commit()
cursor.close()
db.close()
```
 
## MongoDB
 
```python
import pymongo
 
# 1.连接对象
conn = pymongo.MongoClient(host = '192.168.153.137',port = 27017)
# 2.库对象
db = conn['maoyandb']
# 3.集合对象
myset = db['filmtab']
# 4.插入数据库
myset.insert_one({'name':'赵敏'})
myset.insert_many([{'name':'小昭'},{'age':'30'}])
```
 
# 动态爬取
 
## 控制台抓包
 
- 打开方式及常用选项
 
    ```python
    F12 -- Network
    2、控制台常用选项
       1、Network: 抓取网络数据包
            1、ALL: 抓取所有的网络数据包
            2、XHR：抓取异步加载的网络数据包
            3、JS : 抓取所有的JS文件
       2、Sources: 格式化输出并打断点调试JavaScript代码，助于分析爬虫中一些参数
       3、Console: 交互模式，可对JavaScript中的代码进行测试
    3、抓取具体网络数据包后
       1、单击左侧网络数据包地址，进入数据包详情，查看右侧
       2、右侧:
           1、Headers: 整个请求信息
                General、Response Headers、Request Headers、Query String、Form Data
           2、Preview: 对响应内容进行预览
           3、Response：响应内容
    ```
 
## 动态加载数据-Ajax
 
```python
1、右键 -> 查看网页源码中没有具体数据
2、滚动鼠标滑轮或其他动作时加载,或者页面局部刷新
3、F12打开控制台，页面动作抓取网络数据包
4、抓取json文件URL地址
5、按规律拼接url
```
 
## 响应结果解析--json解析模块
 
```python
# loads：json -- python
json.loads(json)
# dumps：python -- json
json.dumps(python)
# dump，load：结合文件操作
with open('xiaomi.json','a') as f:
    json.dump(item_list,f,ensure_ascii=False)
with open('D:/spider_test/xiaomi.json','r') as f:
    data = json.load(f)
```
 
# 登录界面
 
- **方法一 - 登录网站手动抓取Cookie**
 
    ```python
    1、先登录成功1次,获取到携带登录信息的Cookie
       登录成功 - 个人主页 - F12抓包 - 刷新个人主页 - 找到主页的包(profile)
    2、headers携带着cookie发请求
       ** Cookie
       ** User-Agent
    ```
 
- **方法二**
 
    ```python
    # 1、原理
    1、把抓取到的cookie处理为字典
    2、requests.get()中的参数:cookies
 
    # 2、处理cookie为字典
    cookies_dict = {}
    cookies = 'xxxx'
    for kv in cookies.split('; ')
      cookies_dict[kv.split('=')[0]] = kv.split('=')[1]
 
    ```
 
- **方法三 - requests模块处理Cookie**
 
    ```python
    # 思路梳理
    浏览器原理: 访问需要登录的页面会带着之前登录过的cookie
    程序原理: 同样带着之前登录的cookie去访问 - 由session对象完成
    1、实例化session对象
        session = requests.session()
    2、登录网站: session对象发送请求,登录对应网站,把cookie保存在session对象中
        res = session.post(url=url,data=data,headers=headers)
    3、访问页面: session对象请求需要登录才能访问的页面,session能够自动携带之前的这个cookie,进行请求
        res = session.get(url=url,headers=headers)
 
    # 具体步骤
    1、寻找Form表单提交地址 - 寻找登录时POST的地址
       查看网页源码,查看form表单,找action对应的地址: http://www.renren.com/PLogin.do
    2、字典形式发送用户名和密码信息到POST的地址
       用户名和密码信息以什么方式发送？ -- 字典
         post_data = {'email':'','password':''}
    session = requests.session()       
    session.post(url=url,data=data)
    ```
 
 
# 自动化操作 selenium+web
 
## 准备
 
* 定义
 
    ```python
    1、Web自动化测试工具，可运行在浏览器,根据指令操作浏览器
    2、只是工具，必须与第三方浏览器结合使用
    ```
 
* 安装selenium
 
    ```python
    Linux: sudo pip3 install selenium
    Windows: python -m pip install selenium
    ```
 
* 安装phantomjs、chromedriver、geckodriver
 
    ```python
    # 下载地址
    1、chromedriver : 下载对应版本
    http://chromedriver.storage.googleapis.com/index.html
    2、geckodriver
    https://github.com/mozilla/geckodriver/releases
    3、phantomjs
    https://phantomjs.org/download.html
 
    # windows安装
    1、下载对应版本的phantomjs、chromedriver、geckodriver
    2、把chromedriver.exe拷贝到python安装目录的Scripts目录下(添加到系统环境变量)
       # 查看python安装路径: where python
    3、验证
       cmd命令行: chromedriver
 
    # Linux安装
    1、下载后解压
       tar -zxvf geckodriver.tar.gz
    2、拷贝解压后文件到 /usr/bin/ （添加环境变量）
       sudo cp geckodriver /usr/bin/
    3、更改权限
       sudo chmod 777 /usr/bin/geckodriver
    ```
 
## 操作流程
 
```python
# 导入seleinum的webdriver接口
from selenium import webdriver
import time
 
# 设置无头模式
options = webdriver.ChromeOptions()
options.add_argument('--headless')
 
# 创建浏览器对象
browser = webdriver.PhantomJS()/Chrome(options=options)/……
browser.get('http://www.baidu.com/')
 
# 浏览器获取结点/获取文字/点击/搜索……操作
browser.find_element_by_xpath('//*[@id="kw"]').send_keys('赵丽颖')
browser.find_element_by_xpath('//*[@id="su"]').click()
 
# 关闭浏览器
browser.quit()
```
 
## 浏览器对象操作
 
```python
1、browser.get(url) # 获取请求
2、browser.page_source # HTML结构源码
3、browser.page_source.find('字符串') # 从html源码中搜索指定字符串,没有找到返回：-1
4、browser.close() # 关闭当前页
5、browser.quit() # 关闭浏览器
 
# 定位节点
# 1、单元素查找(1个节点对象)
1、browser.find_element_by_id('')
2、browser.find_element_by_name('')
3、browser.find_element_by_class_name('')
4、browser.find_element_by_xpath('')
5、browser.find_element_by_link_text('')
# 2、多元素查找([节点对象列表])
1、browser.find_elements_by_id('')
2、browser.find_elements_by_name('')
3、browser.find_elements_by_class_name('')
4、browser.find_elements_by_xpath('')
```
 
## 节点对象操作
 
### 普通操作
 
```python
# 1、搜索框发送内容
ele.send_keys('')
# 2、点击事件
ele.click()
# 3、获取文本内容，包含子节点和后代节点的文本内容
ele.text
# 4、获取属性值
ele.get_attribute('src')
```
 
### 键盘操作
 
```python
from selenium.webdriver.common.keys import Keys
# 1、输入文字
browser.find_element_by_id('kw').send_keys('赵丽颖')
# 2、输入空格
browser.find_element_by_id('kw').send_keys(Keys.SPACE)
# 3、Ctrl+a 模拟全选
browser.find_element_by_id('kw').send_keys(Keys.CONTROL, 'a')
# 4、Ctrl+c 模拟复制
browser.find_element_by_id('kw').send_keys(Keys.CONTROL, 'c')
# 5、Ctrl+v 模拟粘贴
browser.find_element_by_id('kw').send_keys(Keys.CONTROL, 'v')
# 6、输入回车,代替 搜索 按钮
browser.find_element_by_id('kw').send_keys(Keys.ENTER)
 
# Keys.
BACK_SPACE,TAB,ENTER,SHIFT,Control,ALT,ESCAPE,SPACE
PAGE_UP,PAGE_DOWN,END,HOME,LEFT,UP,RIGHT,DOWN,INSERT,DELETE
NUMPAD0 ~ NUMPAD9,F1 ~ F12
```
 
### 鼠标操作
 
```python
from selenium import webdriver
# 导入鼠标事件类
from selenium.webdriver import ActionChains
 
driver = webdriver.Chrome()
driver.get('http://www.baidu.com/')
 
#移动到 设置，perform()是真正执行操作，必须有
element = driver.find_element_by_xpath('//*[@id="u1"]/a[8]')
ActionChains(driver).move_to_element(element).perform()
 
#单击，弹出的Ajax元素，根据链接节点的文本内容查找
driver.find_element_by_link_text('高级搜索').click()
```
 
### 切换页面
 
```python
# 获取当前所有句柄（窗口）- [handle1,handle2]
all_handles = browser.window_handles
# 切换browser到新的窗口，获取新窗口的对象
browser.switch_to.window(all_handles[n])
```
 
### 页面嵌套 - iframe
 
```python
# 网页中嵌套了网页，先切换到iframe子框架，然后再执行其他操作
browser.switch_to.frame(iframe_element)
```
 
- **示例 - 登录qq邮箱**
 
```python
from selenium import webdriver
import time
 
driver = webdriver.Chrome()
driver.get('https://mail.qq.com/')
 
# 切换到iframe子框架
login_frame = driver.find_element_by_id('login_frame')
driver.switch_to.frame(login_frame)
 
# 用户名+密码+登录
driver.find_element_by_id('u').send_keys('2621470058')
driver.find_element_by_id('p').send_keys('zhanshen001')
driver.find_element_by_id('login_button').click()
 
```
 
# 爬虫异步处理框架 - scrapy
 
## 基础知识
 
- 安装
 
```python
# Ubuntu安装
1、安装依赖包
    1、sudo apt-get install libffi-dev
    2、sudo apt-get install libssl-dev
    3、sudo apt-get install libxml2-dev
    4、sudo apt-get install python3-dev
    5、sudo apt-get install libxslt1-dev
    6、sudo apt-get install zlib1g-dev
    7、sudo pip3 install -I -U service_identity
2、安装scrapy框架
    1、sudo pip3 install Scrapy
 
# Windows安装
cmd命令行(管理员): python -m pip install Scrapy
# Error: Microsoft Visual C++ 14.0 is required xxx
```
 
- Scrapy框架五大组件
 
```python
1、引擎(Engine)      ：整个框架核心
2、调度器(Scheduler) ：维护请求队列
3、下载器(Downloader)：获取响应对象
4、爬虫文件(Spider)  ：数据解析提取
5、项目管道(Pipeline)：数据入库处理
**********************************
# 下载器中间件(Downloader Middlewares) : 引擎->下载器,包装请求(随机代理等)
# 蜘蛛中间件(Spider Middlewares) : 引擎->爬虫文件,可修改响应对象属性
 
# 爬虫项目启动
1、由引擎向爬虫程序索要第一个要爬取的URL,交给调度器去入队列
2、调度器处理请求后出队列,通过下载器中间件交给下载器去下载
3、下载器得到响应对象后,通过蜘蛛中间件交给爬虫程序
4、爬虫程序进行数据提取：
   1、数据交给管道文件去入库处理
   2、对于需要继续跟进的URL,再次交给调度器入队列，依次循环
```
 
*   scrapy项目目录结构
 
```python
Baidu                   # 项目文件夹
├── Baidu               # 项目目录
│   ├── items.py        # 定义数据结构
│   ├── middlewares.py  # 中间件
│   ├── pipelines.py    # 数据处理
│   ├── settings.py     # 全局配置
│   └── spiders
│       ├── baidu.py    # 爬虫文件
└── scrapy.cfg          # 项目基本配置文件
└── begin.py            # 启动文件
```
 
## 各文件内容
 
### 创建爬虫项目步骤
 
```python
1、新建项目 ：scrapy startproject 项目名
2、cd 项目文件夹
3、新建爬虫文件 ：scrapy genspider 文件名 域名
4、明确目标(items.py)
    # 设置字段Field
5、写爬虫程序(文件名.py)
    # import items.py
    # 顺序执行函数，前面的返回yield scrapy.Request()响应对象
    # 最后一个parse返回yield data数据
6、管道文件(pipelines.py)
    #  MaoyanPipeline(object)获取data并return data返回数据
    #  MaoyanMysqlPipeline中，open_spider建立链接，process_item储存数据，close_spider断开连接
7、全局配置(settings.py)
8、运行爬虫 ：scrapy crawl 爬虫名
9、创建begin.py(和scrapy.cfg文件同目录)
    from scrapy import cmdline
    cmdline.execute('scrapy crawl 爬虫名小写'.split())
```
 
### 定义要爬取的数据结构（items.py）
 
```python
name1 = scrapy.Field()
name2 = scrapy.Field()
name3 = scrapy.Field()
```
 
### 编写爬虫文件（maoyan.py）
 
```python
import scrapy
from ..items import MaoyanItem
 
class MaoyanSpider(scrapy.Spider):
    name = 'maoyan3'
    allowed_domains = ['maoyan.com']
    # 去掉start_urls变量
 
    # 重写start_requests()方法
    def start_requests(self):
        for offset in range(0,91,10):
            url = 'https://maoyan.com/board/4?offset={}'.format(offset)
            yield scrapy.Request(url=url,callback=self.parse)  # GET
            yield scrapy.FormRequest(url=post_url,formdata=formdata)  # POST
 
    def parse(self, response):
        # 给items.py中的类:MaoyanItem(scrapy.Item)实例化
        item = MaoyanItem()
 
        # 基准xpath
        dd_list = response.xpath('//dl[@class="board-wrapper"]/dd')
        # 依次遍历
        for dd in dd_list:
            # 是在给items.py中那些类变量赋值
            item['name'] = dd.xpath('./a/@title').get().strip()
            item['star'] = dd.xpath('.//p[@class="star"]/text()').get().strip()
            item['time'] = dd.xpath('.//p[@class="releasetime"]/text()').get().strip()
 
            # 把item对象交给管道文件处理
            yield item  # 结构化文件
            yield item['链接']  # 非结构化文件
```
 
### 定义管道文件（pipelines.py）
 
```python
class MaoyanPipeline(object):
    # item: 从爬虫文件maoyan.py中yield的item数据
    def process_item(self, item, spider):
        return item
 
import pymysql
from .settings import *
 
# 自定义管道 - MySQL数据库
class MaoyanMysqlPipeline(object):
    # 爬虫项目开始运行时执行此函数
    def open_spider(self,spider):
        # 一般用于建立数据库连接
        self.db = pymysql.connect(host,user,password,database,charset)
        self.cursor = self.db.cursor()
 
    def process_item(self,item,spider):
        ins = 'insert into filmtab values(%s,%s,%s)'
        # 因为execute()的第二个参数为列表
        L = [
            item['name'],item['star'],item['time']
        ]
        self.cursor.execute(ins,L)
        self.db.commit()
        return item
 
    # 爬虫项目结束时执行此函数
    def close_spider(self,spider):
        # 一般用于断开数据库连接
        self.cursor.close()
        self.db.close()
 
# 非结构化文件
   from scrapy.pipelines.images import ImagesPipeline
   import scrapy
   class TestPipeline(ImagesPipeline):
      def get_media_requests(self,item,info):
            yield scrapy.Request(url=item['url'],meta={'name':item['name']})
      def file_path(self,request,response=None,info=None):
            name = request.meta['name']
            filename = name
            return filename
```
 
### 全局配置文件（settings.py）
 
```python
# 1、定义User-Agent
USER_AGENT = 'Mozilla/5.0'
# 2、是否遵循robots协议，一般设置为False
ROBOTSTXT_OBEY = False
# 3、最大并发量，默认为16
CONCURRENT_REQUESTS = 32
# 4、下载延迟时间
DOWNLOAD_DELAY = 1
# 5、请求头，此处也可以添加User-Agent
DEFAULT_REQUEST_HEADERS = {
    'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',
    'Accept-Language': 'en',
}
# 6、项目管道：多个数据库多个项
ITEM_PIPELINES = {
   'Maoyan.pipelines.MaoyanPipeline': 300,# 优先级
   'Maoyan.pipelines.MaoyanMysqlPipeline':200,
}
# 7、定义MySQL相关变量
MYSQL_HOST = '127.0.0.1'
MYSQL_USER = 'root'
MYSQL_PWD = '123456'
MYSQL_DB = 'maoyandb'
MYSQL_CHAR = 'utf8'
# 8、设置导出编码 - 主要针对json文件
FEED_EXPORT_ENCODING = 'utf-8'
# 9、日志名称
LOG_FILE = '文件名.log'
# 10、日志级别: 只显示当前级别的日志和比当前级别日志更严重的
LOG_LEVEL = ''
5 CRITICAL ：严重错误
4 ERROR    ：普通错误
3 WARNING  ：警告
2 INFO     ：一般信息
1 DEBUG    ：调试信息
# 12、非结构化数据存储路径
IMAGES_STORE = ''
FILES_STORE = ''
# 13、添加下载器中间件
DOWNLOADER_MIDDLEWARES = {'项目名.middlewares.类名':200}
# 14、cookie(默认禁用,取消注释-True|False都为开启)
COOKIES_ENABLED = False
# 15、非结构化文件
IMAGES_STORE = 'D:/Spider/images'
```
 
- 保存为csv、json文件
 
```python
scrapy crawl maoyan -o maoyan.csv
scrapy crawl maoyan -o maoyan.json
```
 
*   response对象
 
```python
# 属性
1、response.text ：获取响应内容 - 字符串
2、response.body ：获取bytes数据类型
3、response.xpath('')：匹配内容
 
# response.xpath('')调用方法
1、结果 ：列表,元素为选择器对象
2、.extract() ：提取文本内容,将列表中所有元素序列化为Unicode字符串
3、.extract_first() ：提取列表中第1个文本内容
4、.get() ： 提取列表中第1个文本内容
```
 
*   request对象
 
```python
1、url
2、callback
3、meta ：传递数据,定义代理
meta = {'name':name,'proxy':'http://IP:Port'}
```
 
### 爬虫项目启动方式
 
- **方式一**
 
```python
从爬虫文件(spider)的start_urls变量中遍历URL地址，把下载器返回的响应对象（response）交给爬虫文件的parse()函数处理
# start_urls = ['http://www.baidu.com/']
```
 
- **方式二**
 
```python
重写start_requests()方法，从此方法中获取URL，交给指定的callback解析函数处理
 
1、去掉start_urls变量
2、def start_requests(self):
      # 生成要爬取的URL地址，利用scrapy.Request()方法交给调度器
```
 
### 非结构化文件
 
```python
1、spider
   yield item['链接']
2、pipelines.py
   from scrapy.pipelines.images import ImagesPipeline
   import scrapy
   class TestPipeline(ImagesPipeline):
      def get_media_requests(self,item,info):
            yield scrapy.Request(url=item['url'],meta={'name':item['name']})
      def file_path(self,request,response=None,info=None):
            name = request.meta['name']
            filename = name
            return filename
3、settings.py
   IMAGES_STORE = 'D:/Spider/images'
```
 
## scrapy添加cookie
 
```python
# 1、修改 settings.py 文件
1、COOKIES_ENABLED = False  -> 取消注释,开启cookie,检查headers中的cookie
2、DEFAULT_REQUEST_HEADERS = {}   添加Cookie
 
# 2、爬虫文件 - 利用cookies参数
COOKIES_ENABLED = True   -> 修改为TRUE后，检查 Request()方法中cookies参数
def start_requests(self):
    yield scrapy.Request(url=url,cookies={},callback=xxx)
 
# 3、DownloadMiddleware设置中间件
COOKIES_ENABLED = TRUE  -> 找Request()方法中cookies参数
def process_request(self,request,spider):
    request.cookies={}
```
 
## **scrapy shell的使用**
 
交互式shell，可在不运行spider的前提下,快速调试 scrapy 代码(主要测试xpath表达式)
 
- **基本使用**
 
```python
scrapy shell URL地址
*1、request.url     : 请求URL地址
*2、request.headers ：请求头(字典)
*3、request.meta    ：item数据传递，定义代理(字典)
 
4、response.text    ：字符串
5、response.body    ：bytes
6、response.xpath('')
7、response.status  : HTTP响应码
 
# 可用方法
shelp() : 帮助
fetch(request) : 从给定的请求中获取新的响应，并更新所有相关对象
view(response) : 在本地Web浏览器中打开给定的响应以进行检查
```
 
- **scrapy.Request()参数**
 
```python
1、url
2、callback
3、headers
4、meta ：传递数据,定义代理
5、dont_filter ：是否忽略域组限制
   默认False,检查allowed_domains['']
6、cookies
```
 
## **设置中间件**
 
### 随机User-Agent
 
#### 少量User-Agent
 
- **方法一**
 
```python
# settings.py
USER_AGENT = ''
DEFAULT_REQUEST_HEADERS = {}
```
 
- **方法二**
 
```python
# spider
yield scrapy.Request(url,callback=函数名,headers={})
```
 
#### 大量User-Agent切换（中间件）
 
- **middlewares.py设置中间件**
 
```python
1、获取User-Agent
   # 方法1 ：新建useragents.py,存放大量User-Agent，random模块随机切换
   # 方法2 ：安装fake_useragent模块(sudo pip3 install fake_useragent)
from fake_useragent import UserAgent
   ua_obj = UserAgent()
   ua = ua_obj.random
2、middlewares.py新建中间件类
class RandomUseragentMiddleware(object):
    def process_request(self,reuqest,spider):
        ua = UserAgent()
        request.headers['User-Agent'] = ua.random
3、settings.py添加此下载器中间件
    DOWNLOADER_MIDDLEWARES = {'' : 优先级}
 
```
 
### 随机代理
 
```python
class RandomProxyDownloaderMiddleware(object):
    def process_request(self,request,spider):
        request.meta['proxy'] = xxx
    def process_exception(self,request,exception,spider):
        return request
```
 
# 优化爬虫
 
## 多线程爬虫
 
多个任务同时抓取
 
```python
# 1. 在 __init__(self) 中创建文件对象，多线程操作此对象进行文件写入
  self.f = open('xiaomi.csv','a',newline='')
  self.writer = csv.writer(self.f)
  self.lock = Lock()
 
#2. 每个线程抓取1页数据后将数据进行文件写入，写入文件时需要加锁
  def parse_html(self):
    app_list = []
    for xxx in xxx:
        app_list.append([name,link,typ])
    self.lock.acquire()
    self.wirter.writerows(app_list)
    self.lock.release()
 
#3. 加入线程进程，所有数据抓取完成关闭文件
  def run(self):
    # 创建多个线程
    for i in range(1):
      t = Thread(target=self.get_data)
      t_list.append(t)
      t.start()
    # 回收线程
    for t in t_list:
      t.join()
    # 关闭文件
    self.f.close()
```
## 分布式爬虫
 
多台主机共享1个爬取队列
 
- **实现**
 
    ```python
    重写scrapy调度器(scrapy_redis模块)
    sudo pip3 install scrapy_redis
    ```
 
- **settings.py说明**
 
    ```python
    # 重新指定调度器: 启用Redis调度存储请求队列
    SCHEDULER = "scrapy_redis.scheduler.Scheduler"
 
    # 重新指定去重机制: 确保所有的爬虫通过Redis去重
    DUPEFILTER_CLASS = "scrapy_redis.dupefilter.RFPDupeFilter"
 
    # 不清除Redis队列: 暂停/恢复/断点续爬(默认清除为False,设置为True不清除)
    SCHEDULER_PERSIST = True
 
    # 优先级队列 （默认）
    SCHEDULER_QUEUE_CLASS = 'scrapy_redis.queue.PriorityQueue'
    #可选用的其它队列
    # 先进先出
    SCHEDULER_QUEUE_CLASS = 'scrapy_redis.queue.FifoQueue'
    # 后进先出
    SCHEDULER_QUEUE_CLASS = 'scrapy_redis.queue.LifoQueue'
 
    # redis管道
    ITEM_PIPELINES = {
        'scrapy_redis.pipelines.RedisPipeline': 300
    }
 
    #指定连接到redis时使用的端口和地址
    REDIS_HOST = 'localhost'
    REDIS_PORT = 6379
    ```
 
### 腾讯招聘分布式改写
 
多台主机共享1个爬取队列-redis/mysql
 
代码拷贝一份到分布式中其他机器，两台或多台机器同时执行此代码
 
#### 方法一
 
```python
# settings.py
 
# 1、使用scrapy_redis的调度器
SCHEDULER = "scrapy_redis.scheduler.Scheduler"
# 2、使用scrapy_redis的去重机制
DUPEFILTER_CLASS = "scrapy_redis.dupefilter.RFPDupeFilter"
# 3、是否清除请求指纹,True:不清除 False:清除（默认）
SCHEDULER_PERSIST = True
# 4、(非必须)在ITEM_PIPELINES中添加redis管道
'scrapy_redis.pipelines.RedisPipeline': 200
# 5、定义redis主机地址和端口号
REDIS_HOST = '127.0.0.1'
REDIS_PORT = 6379
 
# mysql
ITEM_PIPELINES = {
   'Tencent.pipelines.TencentPipeline': 300,
   # 'scrapy_redis.pipelines.RedisPipeline': 200
   'Tencent.pipelines.TencentMysqlPipeline':200,
}
```
 
#### 方法二
 
使用redis_key改写
 
```python
# 第一步: settings.py无须改动
settings.py和上面分布式代码一致
# 第二步:tencent.py
from scrapy_redis.spiders import RedisSpider
class TencentSpider(RedisSpider):
    # 1. 去掉start_urls
    # 2. 定义redis_key
    redis_key = 'tencent:spider'
    def parse(self,response):
        pass
# 第三步:把代码复制到所有爬虫服务器，并启动项目
# 第四步
  到redis命令行，执行LPUSH命令压入第一个要爬取的URL地址
  >LPUSH tencent:spider 第1页的URL地址
 
# 项目爬取结束后无法退出，如何退出？
setting.py
CLOSESPIDER_TIMEOUT = 3600
# 到指定时间(3600秒)时,会自动结束并退出
```
 
# 拓展
 
## 机器视觉与tesseract
 
```python
# 作用
处理图形验证码
 
# OCR
光学字符识别(Optical Character Recognition),通过扫描等光学输入方式将各种票据、报刊、书籍、文稿及其它印刷品的文字转化为图像信息，再利用文字识别技术将图像信息转化为电子文本
 
# tesseract-ocr
OCR的一个底层识别库（不是模块，不能导入），由Google维护的开源OCR识别库
 
# pytesseract
Python模块,可调用底层识别库，是对tesseract-ocr做的一层Python API封装
```
 
### 安装
 
```python
# 1. Ubuntu安装
sudo apt-get install tesseract-ocr
sudo pip3 install pytesseract
# 2. Windows安装
1）下载安装包
2）添加到环境变量(Path)
# 3. 测试 - 终端 | cmd命令行
tesseract xxx.jpg 文件名
```
 
### 使用
 
```python
import pytesseract
# Python图片处理标准库
from PIL import Image
 
# 创建图片对象
img = Image.open('test1.jpg')
# 图片转字符串
result = pytesseract.image_to_string(img)
print(result)
```
 
### 识别度高：在线打码平台
 
- **破解云打码网站验证码**
 
- **1、下载并调整接口文档，封装成函数，打码获取结果**
 
```python
import http.client, mimetypes, urllib, json, time, requests
 
 
######################################################################
 
class YDMHttp:
    apiurl = 'http://api.yundama.com/api.php'
    username = ''
    password = ''
    appid = ''
    appkey = ''
 
    def __init__(self, username, password, appid, appkey):
        self.username = username
        self.password = password
        self.appid = str(appid)
        self.appkey = appkey
 
    def request(self, fields, files=[]):
        response = self.post_url(self.apiurl, fields, files)
        response = json.loads(response)
        return response
 
    def balance(self):
        data = {'method': 'balance', 'username': self.username, 'password': self.password, 'appid': self.appid,'appkey': self.appkey}
        response = self.request(data)
        if response:
            if (response['ret'] and response['ret'] < 0):
                return response['ret']
            else:
                return response['balance']
        else:
            return -9001
 
    def login(self):
        data = {'method': 'login', 'username': self.username, 'password': self.password, 'appid': self.appid,'appkey': self.appkey}
        response = self.request(data)
        if response:
            if (response['ret'] and response['ret'] < 0):
                return response['ret']
            else:
                return response['uid']
        else:
            return -9001
 
    def upload(self, filename, codetype, timeout):
        data = {'method': 'upload', 'username': self.username, 'password': self.password, 'appid':self.appid,'appkey':self.appkey,'codetype':str(codetype), 'timeout':str(timeout)}
        file = {'file': filename}
        response = self.request(data, file)
        if response:
            if (response['ret'] and response['ret'] < 0):
                return response['ret']
            else:
                return response['cid']
        else:
            return -9001
 
    def result(self, cid):
        data = {'method': 'result', 'username': self.username, 'password': self.password, 'appid': self.appid,'appkey': self.appkey, 'cid': str(cid)}
        response = self.request(data)
        return response and response['text'] or ''
 
    def decode(self, filename, codetype, timeout):
        cid = self.upload(filename, codetype, timeout)
        if (cid > 0):
            for i in range(0, timeout):
                result = self.result(cid)
                if (result != ''):
                    return cid, result
                else:
                    time.sleep(1)
            return -3003, ''
        else:
            return cid, ''
 
    def report(self, cid):
        data = {'method': 'report', 'username': self.username, 'password': self.password, 'appid': self.appid,'appkey': self.appkey, 'cid': str(cid), 'flag': '0'}
        response = self.request(data)
        if (response):
            return response['ret']
        else:
            return -9001
 
    def post_url(self, url, fields, files=[]):
        for key in files:
            files[key] = open(files[key], 'rb');
        res = requests.post(url, files=files, data=fields)
        return res.text
 
 
######################################################################
def get_result(filename):
    # 用户名
    username = 'yibeizi001'
    # 密码
    password = 'zhishouzhetian001'
    # 软件ＩＤ，开发者分成必要参数。登录开发者后台【我的软件】获得！
    appid = 1
    # 软件密钥，开发者分成必要参数。登录开发者后台【我的软件】获得！
    appkey = '22cc5376925e9387a23cf797cb9ba745'
    # 验证码类型，# 例：1004表示4位字母数字，不同类型收费不同。请准确填写，否则影响识别率。在此查询所有类型 http://www.yundama.com/price.html
    codetype = 5000
    # 超时时间，秒
    timeout = 60
    # 初始化
    yundama = YDMHttp(username, password, appid, appkey)
    # 登陆云打码
    uid = yundama.login()
    # 查询余额
    balance = yundama.balance()
    # 开始识别，图片路径，验证码类型ID，超时时间（秒），识别结果
    cid, result = yundama.decode(filename, codetype, timeout)
 
    return result
######################################################################
 
```
 
​    **2、访问云打码网站，获取验证码并在线识别**
 
```python
'''识别云打码官网的验证码'''
from selenium import webdriver
from ydmapi import *
from PIL import Image
 
class YdmSpider(object):
    def __init__(self):
        self.options = webdriver.ChromeOptions()
        # 浏览器窗口最大化
        self.options.add_argument('--start-maximized')
        self.browser = webdriver.Chrome(options=self.options)
        self.browser.get('http://www.yundama.com/')
 
    # 获取验证码图片截取出来
    def get_image(self):
        # 1.获取页面截图
        self.browser.save_screenshot('index.png')
        # 2.获取验证码节点坐标,把图片截取出来
        # location: 获取节点左上角的坐标(x y)
        location = self.browser.find_element_by_xpath('//*[@id="verifyImg"]').location
        # size: 获取节点的大小(宽度和高度)
        size = self.browser.find_element_by_xpath('//*[@id="verifyImg"]').size
        # 四个坐标
        left_x = location['x']
        left_y = location['y']
        right_x = left_x + size['width']
        right_y = left_y + size['height']
        # 从index.png中截图图片
        img = Image.open('index.png').crop((left_x,left_y,right_x,right_y))
        img.save('verify.png')
 
    # 获取识别结果
    def get_result(self):
        result = get_result('verify.png')
        print('识别结果:',result)
 
    # 入口函数
    def run(self):
        self.get_image()
        self.get_result()
 
if __name__ == '__main__':
    spider = YdmSpider()
    spider.run()
```
 
## **Fiddler抓包工具**
 
- **配置Fiddler**
 
    ```python
    # 添加证书信任
    1、Tools - Options - HTTPS
       勾选 Decrypt Https Traffic 后弹出窗口，一路确认
    # 设置只抓取浏览器的数据包
    2、...from browsers only
    # 设置监听端口（默认为8888）
    3、Tools - Options - Connections
    # 配置完成后重启Fiddler（重要）
    4、关闭Fiddler,再打开Fiddler
    ```
 
- **配置浏览器代理**
 
    ```python
    SwitchyOmega切换代理
 
    默认
    http 80
    https 443
    ```
 
 
## **移动端app数据抓取**
 
- **方法1 - 手机 + Fiddler**
 
    ```python
    设置方法见文件夹 - 移动端抓包配置
    ```
 
- **方法2 - F12浏览器工具**
 
    ```python
    浏览器调节成手机模式，请求访问手机端网址
    ```
 
# 反爬与反反爬
 
- 访问频繁
 
    - headers：一般被关注的变量是UserAgent和Referer
 
        ```python
        1、发送请求携带请求头:
            headers={'User-Agent' : 'Mozilla/5.0 xxxxxx'}
        2、多个请求随机切换User-Agent
           1、定义列表存放大量User-Agent，使用random.choice()每次随机选择
           2、定义py文件存放大量User-Agent，使用random.choice()每次随机选择
           3、使用fake_useragent模块每次访问随机生成User-Agent
        ```
 
    - 拉黑高频访问IP
 
        ```python
        1、 创建代理池，请求头池
        2、 控制爬取速度
        ```
 
    - Cookies
 
        ```python
        1、 建立有效的cookie池，每次访问随机切换
        ```
 
*   响应内容前端JS
 
    *   动态生成
 
        ```python
        1、html页面中可匹配出内容，程序中匹配结果为空
        响应内容中嵌入js，对页面结构做了一定调整导致，通过查看网页源代码，格式化输出查看结构，更改xpath或者正则测试
        2、如果数据出不来可考虑更换 IE 的User-Agent尝试，数据返回最标准
        ```
 
    *   签名及js加密
 
        ```python
        1、 一般为本地JS加密,查找本地JS文件,分析,或者使用execjs模块执行JS
        ```
 
    *   js调整页面结构
 
    *   js在响应中指向新的地址
 
*   验证码
 
    ```python
    1、 验证码数量较少可人工填写
    2、 图形验证码可使用tesseract识别
    3、 其他情况只能在线打码、人工打码和训练机器学习模型
    ```
 
# Tips
 
*   测试网站 http://httpbin.org/get
 
*   ?xx=xx查询/#锚点
 
*   伪装头：
 
    ```python
    from fake_useragent import UserAgent
    agent = UserAgent().random
    ```
 
*   创建文件夹
 
    ```python
    import os
    self.directory = '/home/tarena/images/{}/'.format(self.word)
    if not os.path.exists(self.directory):
       os.makedirs(self.directory)
    ```
 
- 爬虫常用插件
 
    ```python
    1、google-access-helper : 谷歌访问助手
    2、Xpath Helper: 轻松获取HTML元素的xPath路径
       打开/关闭: Ctrl + Shift + x
    3、JsonView: 格式化输出json格式数据
    4、Proxy SwitchyOmega: Chrome浏览器中的代理管理扩展程序
    ```
 
*   不要使你的程序因为任何异常而终止
    *   页面请求设置超时时间,并用try捕捉异常,超过指定次数则更换下一个URL地址
    *   所抓取任何数据,获取具体数据前先判断是否存在该数据
 
*   pycharm中正则处理headers和formdata
 
    ```python
    1、pycharm进入方法 ：Ctrl + r ，选中 Regex
    2、处理headers和formdata
      (.*): (.*)
      "$1": "$2",
    3、点击 Replace All
    ```
 
*   抓取数据一般处理方式
    1、txt文件
    2、csv文件
    3、json文件
    4、MySQL数据库
    5、MongoDB数据库
    6、Redis数据库
 
*   response对象
 
    ```python
    # 属性
    1、response.text ：获取响应内容 - 字符串
    2、response.body ：获取bytes数据类型
    3、response.xpath('')：匹配内容
 
    # response.xpath('')调用方法
    1、结果 ：列表,元素为选择器对象
    2、.extract() ：提取文本内容,将列表中所有元素序列化为Unicode字符串
    3、.extract_first() ：提取列表中第1个文本内容
    4、.get() ： 提取列表中第1个文本内容
    ```
 
* 参数
    * spider.py方法间：scrapy.Request(url,meta,callback)
    * spider.py和pipl之间：yield item
    * pipl.py类之间：return item
 
# 案例
 
## 动态爬取案例
 
### 民政局
 
*   增量爬取 + 分表存储
 
    ```python
    import requests
    from lxml import etree
    import re
    import pymysql
    from hashlib import md5
 
    class GovementSpider(object):
      def __init__(self):
        self.url = 'http://www.mca.gov.cn/article/sj/xzqh/2019/'
        self.headers = {'User-Agent':'Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1; WOW64; Trident/4.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; Media Center PC 6.0; .NET4.0C; InfoPath.3)'}
        self.db = pymysql.connect('localhost','root','attack','govdb',charset='utf8')
        self.cursor = self.db.cursor()
        # 用于executemany([(),(),()])
        self.province = []
        self.city = []
        self.county = []
 
      # 获取假链接
      def get_false_link(self):
        html = requests.get(url = self.url,headers = self.headers).text
        # 解析
        parse_html = etree.HTML(html)
        a_href_list = parse_html.xpath('//table//tr[2]/td[2]/a/@href')
        if a_href_list:
          a_href = a_href_list[0]
          false_link = 'http://www.mca.gov.cn' + a_href
          finger = md5(false_link.encode()).hexdigest()
          # 判断数据库中是否存在
          sel = 'select * from request_finger where finger=%s'
          result = self.cursor.execute(sel, [finger])
          if not result:
            self.get_true_link(false_link)
            ins = 'insert into request_finger values(%s)'
            self.cursor.execute(ins, [finger])
            self.db.commit()
          else:
            print('数据已是最新')
        else:
          print('提取链接失败')
 
 
      # 获取真链接
      def get_true_link(self,false_link):
        # 先获取假链接的响应,然后根据响应获取真链接
        html = requests.get(url = false_link,headers = self.headers).text
        # 利用正则提取真实链接
        re_bds = r'window.location.href="(.*?)"'
        pattern = re.compile(re_bds,re.S)
        true_link = pattern.findall(html)[0]
 
        self.parse_html(true_link)
 
      # 数据提取
      def parse_html(self,true_link):
        html = requests.get( url = true_link,headers = self.headers).text
 
        # xpath提取数据
        parse_html = etree.HTML(html)
        tr_list = parse_html.xpath('//tr[@height="19"]')
        for tr in tr_list:
          code = tr.xpath('./td[2]/text()')[0].strip()
          name = tr.xpath('./td[3]/text()')[0].strip()
          print(name,code)
          # 上海市 上海市 浦东新区
          if code[-4:] == '0000':
            self.province.append((name, code))
            # 把4个直辖市添加到city表
            if code[:2] in ['11', '12', '31', '50']:
              self.city.append((name, code, code))
 
          elif code[-2:] == '00':
            pcode = code[:2] + '0000'
            self.city.append((name, code, pcode))
            # 记录最近1次的市的编号
            last_city = code
          else:
            if code[:2] in ['11', '12', '31', '50']:
              ccode = code[:2] + '0000'
            else:
              ccode = last_city
            self.county.append((name, code, ccode))
 
        self.insert_mysql()
 
      def insert_mysql(self):
        # 1.先清除原有数据
        del1 = 'delete from province'
        del2 = 'delete from city'
        del3 = 'delete from county'
        self.cursor.execute(del1)
        self.cursor.execute(del2)
        self.cursor.execute(del3)
        self.db.commit()
        # 2.插入新的数据
        ins1 = 'insert into province values(%s,%s)'
        ins2 = 'insert into city values(%s,%s,%s)'
        ins3 = 'insert into county values(%s,%s,%s)'
        self.cursor.executemany(ins1, self.province)
        self.cursor.executemany(ins2, self.city)
        self.cursor.executemany(ins3, self.county)
        self.db.commit()
 
      # 主函数
      def main(self):
        self.get_false_link()
 
    if __name__ == '__main__':
      spider = GovementSpider()
      spider.main()
    ```
 
- 连接查询
 
    ```mysql
    select province.pname,city.cname,county.xname from province inner join city on  province.pcode=city.cfather_code inner join county on  city.ccode=county.xfather_code;
    ```
 
*   selenium实现
 
    ```python
    from selenium import webdriver
 
    class GovSpider(object):
      def __init__(self):
        # 设置无界面
        options = webdriver.ChromeOptions()
        options.add_argument('--headless')
        # 添加参数
        self.browser = webdriver.Chrome(options=options)
        self.one_url = 'http://www.mca.gov.cn/article/sj/xzqh/2019/'
 
      def get_incr_url(self):
        self.browser.get(self.one_url)
        # 提取最新链接节点对象并点击
        self.browser.find_element_by_xpath(
          '//td[@class="arlisttd"]/a[contains(@title,"代码")]'
        ).click()
        # 切换句柄
        all_handlers = self.browser.window_handles
        self.browser.switch_to.window(all_handlers[1])
        self.get_data()
 
      def get_data(self):
        tr_list = self.browser.find_elements_by_xpath(
          '//tr[@height="19"]'
        )
        for tr in tr_list:
          code = tr.find_element_by_xpath('./td[2]').text.strip()
          name = tr.find_element_by_xpath('./td[3]').text.strip()
          print(name,code)
 
      def run(self):
        self.get_incr_url()
        self.browser.quit()
 
    if __name__ == '__main__':
      spider = GovSpider()
      spider.run()
    ```
 
## 多线程爬虫案例
 
### 小米商店
 
- **目标**
 
    ```python
    1、网址 ：百度搜 - 小米应用商店，进入官网
    2、目标 ：所有应用分类
       应用名称
       应用链接
    ```
 
- **实现步骤**
 
    ```python
    # 1. 确认是否为动态加载
    1.1) 页面局部刷新
    1.2) 右键查看网页源代码,搜索关键字未搜到,为动态加载，需要抓取网络数据包分析
 
    # 2. F12抓取网络数据包
    2.1) 抓取返回json数据的URL地址（Headers中的Request URL）
         http://app.mi.com/categotyAllListApi?page={}&categoryId=2&pageSize=30
    2.2) 查看并分析查询参数（headers中的Query String Parameters）
         page: 1        只有page在变，0 1 2 3 ... ...
         categoryId: 2
         pageSize: 30
 
    # 3. 将抓取数据保存到csv文件 - 注意线程锁问题
    from threading import Lock
    lock = Lock()
    # 加锁 + 释放锁
    lock.acquire()
    lock.release()
 
    <li><a  href="/category/(.*?)">(.*?)</a></li>
    ```
 
- **整体思路**
 
    ```python
    # 1. 在 __init__(self) 中创建文件对象，多线程操作此对象进行文件写入
      self.f = open('xiaomi.csv','a',newline='')
      self.writer = csv.writer(self.f)
      self.lock = Lock()
 
    #2. 每个线程抓取1页数据后将数据进行文件写入，写入文件时需要加锁
      def parse_html(self):
        app_list = []
        for xxx in xxx:
            app_list.append([name,link,typ])
        self.lock.acquire()
        self.wirter.writerows(app_list)
        self.lock.release()
 
    #3. 所有数据抓取完成关闭文件
      def run(self):
        self.f.close()
    ```
 
- **代码实现**
 
    ```python
    import requests
    from threading import Thread
    from queue import Queue
    import time
    from fake_useragent import UserAgent
    import csv
    from threading import Lock
    import random
    import re
 
    class XiaomiSpider(object):
      def __init__(self):
        self.url = 'http://app.mi.com/categotyAllListApi?page={}&categoryId={}&pageSize=30'
        # 存放所有URL地址的队列
        self.q = Queue()
        self.i = 0
        # 打开文件
        self.f = open('xiaomi.csv','a')
        self.writer = csv.writer(self.f)
        # 创建锁
        self.lock = Lock()
 
 
      def get_cateid(self):
        # 请求
        url = 'http://app.mi.com/'
        headers = { 'User-Agent': UserAgent().random }
        html = requests.get(url=url,headers=headers).text
 
        p = re.compile('<a href="/category/(.*?)">(.*?)</a>',re.S)
        r_list = p.findall(html)
        print(r_list)
        for r in r_list:
          # 入队列
          self.url_in(r)
 
      # url入队列
      def url_in(self,r):
        pages = self.get_pages(r[0])
        for page in range(1,pages):
          url = self.url.format(page,r[0])
          # 把URL地址入队列
          self.q.put(url)
 
      # 获取counts的值并计算页数
      def get_pages(self,typ_id):
        # 每页返回的json数据中,都有count这个key
        url = self.url.format(0,typ_id)
        html = requests.get(
          url=url,
          headers={ 'User-Agent':UserAgent().random }
        ).json()
        count = html['count']
        pages = int(count) // 30 + 1
 
        return pages
 
      # 线程事件函数: get() - 请求 - 解析 - 处理数据
      def get_data(self):
        while True:
          if not self.q.empty():
            url = self.q.get()
            headers = { 'User-Agent':UserAgent().random }
            html = requests.get(url=url,headers=headers).json()
            self.parse_html(html)
          else:
            break
 
      # 解析函数
      def parse_html(self,html):
        # 存放1页的数据 - 写入到csv文件
        app_list = []
 
        for app in html['data']:
          # 应用名称 + 链接 + 分类
          name = app['displayName']
          link = 'http://app.mi.com/details?id=' + app['packageName']
          typ_name = app['level1CategoryName']
          # 把每一条数据放到app_list中,目的为了 writerows()
          app_list.append([name,typ_name,link])
 
          print(name,typ_name)
          time.sleep(random.uniform(0,1))
          self.i += 1
 
        # 开始写入1页数据 - app_list
        self.lock.acquire()
        self.writer.writerows(app_list)
        self.lock.release()
 
      # 主函数
      def main(self):
        # URL入队列
        self.get_cateid()
        t_list = []
        # 创建多个线程
        for i in range(1):
          t = Thread(target=self.get_data)
          t_list.append(t)
          t.start()
 
        # 回收线程
        for t in t_list:
          t.join()
 
        # 关闭文件
        self.f.close()
        print('数量:',self.i)
 
    if __name__ == '__main__':
      start = time.time()
      spider = XiaomiSpider()
      spider.main()
      end = time.time()
      print('执行时间:%.2f' % (end-start))
 
    ```
 
### 腾讯招聘
 
- **确定URL地址及目标**
 
    ```python
    1、URL: 百度搜索腾讯招聘 - 查看工作岗位
    2、目标: 职位名称、工作职责、岗位要求
 
    ```
 
- **要求与分析**
 
    ```python
    1、通过查看网页源码,得知所需数据均为动态加载
    2、通过F12抓取网络数据包,进行分析
    3、一级页面抓取数据: 职位名称
    4、二级页面抓取数据: 工作职责、岗位要求
 
    ```
 
- **一级页面json地址**
 
    ```python
    """index在变,timestamp未检查"""
    https://careers.tencent.com/tencentcareer/api/post/Query?timestamp=1563912271089&countryId=&cityId=&bgIds=&productId=&categoryId=&parentCategoryId=&attrId=&keyword={}&pageIndex={}&pageSize=10&language=zh-cn&area=cn
 
    ```
 
- **二级页面地址**
 
    ```python
    """postId在变,在一级页面中可拿到"""
    https://careers.tencent.com/tencentcareer/api/post/ByPostId?timestamp=1563912374645&postId={}&language=zh-cn
 
    ```
 
- **思路提示**
 
    ```
    【思考】 两级页面是否需要指定两个队列分别存放？
        提示1: 建立2个队列,分别存放不同级的URL地址
        提示2: 从对列中get地址,最好使用timeout参数
 
    ```
 
- **代码实现**
 
    ```python
    import requests
    import json
    import time
    from fake_useragent import UserAgent
    from queue import Queue
    from threading import Thread,Lock
    from urllib import parse
    import csv
 
    class TencentSpider(object):
      def __init__(self):
        self.one_url = 'https://careers.tencent.com/tencentcareer/api/post/Query?timestamp=1563912271089&countryId=&cityId=&bgIds=&productId=&categoryId=&parentCategoryId=&attrId=&keyword={}&pageIndex={}&pageSize=10&language=zh-cn&area=cn'
        self.two_url = 'https://careers.tencent.com/tencentcareer/api/post/ByPostId?timestamp=1563912374645&postId={}&language=zh-cn'
        self.one_q = Queue()
        self.two_q = Queue()
        self.lock = Lock()
        self.i = 0
        self.f = open('tencent.csv','w')
        self.writer = csv.writer(self.f)
        # 存放所有数据的大列表,用于writerows()方法
        self.item_list = []
 
 
      def get_html(self,url):
          headers = { 'User-Agent':UserAgent().random }
          html = requests.get(url=url,headers=headers).text
          return html
 
      def url_in(self):
          keyword = input('请输入职位类别:')
          keyword = parse.quote(keyword)
          total = self.get_total(keyword)
          for page in range(1, total+1):
              one_url = self.one_url.format(keyword,page)
              self.one_q.put(one_url)
 
      # 获取总页数
      def get_total(self,keyword):
        url = self.one_url.format(keyword,1)
        html = requests.get(url=url,headers={'User-Agent':UserAgent().random}).json()
        numbers = int(html['Data']['Count'])
        if numbers % 10 == 0:
            total = numbers // 10
        else:
            total = numbers // 10 + 1
 
        return total
 
      # 线程1事件函数
      def parse_one_page(self):
          while True:
              if not self.one_q.empty():
                  one_url = self.one_q.get()
                  html = json.loads(self.get_html(one_url))
                  for job in html['Data']['Posts']:
                      post_id = job['PostId']
                      two_url = self.two_url.format(post_id)
                      self.two_q.put(two_url)
              else:
                  break
 
 
      # 线程2事件函数
      def parse_two_page(self):
          while True:
              try:
                  two_url = self.two_q.get(block=True,timeout=3)
                  html = json.loads(self.get_html(two_url))
                  # 职位名称+职责+要求
                  item = {}
                  item['name'] = html['Data']['RecruitPostName']
                  item['duty'] = html['Data']['Responsibility']
                  item['require'] = html['Data']['Requirement']
                  # 写入csv文件数据类型: [(),(),(),...,()]
                  item_tuple = (item['name'],item['duty'],item['require'])
                  self.item_list.append(item_tuple)
 
                  print(item)
                  self.lock.acquire()
                  self.i += 1
                  self.lock.release()
              except Exception as e:
                  break
 
      def run(self):
          self.url_in()
          t1_list = []
          t2_list = []
          for i in range(5):
              t = Thread(target=self.parse_one_page)
              t1_list.append(t)
              t.start()
 
          for i in range(5):
              t = Thread(target=self.parse_two_page)
              t2_list.append(t)
              t.start()
 
          for t in t1_list:
              t.join()
 
          for t in t2_list:
              t.join()
 
          print('数量:',self.i)
          # 将所有数据一次性写入文件
          self.writer.writerows(self.item_list)
          self.f.close()
 
    if __name__ == '__main__':
      start_time = time.time()
      spider = TencentSpider()
      spider.run()
      end_time = time.time()
      print('执行时间:%.2f' % (end_time-start_time))
    ```
 
## scrapy框架案例
 
### 盗墓笔记
 
-   目标
 
```python
# 抓取目标网站中盗墓笔记1-8中所有章节的所有小说的具体内容，保存到本地文件
1、网址 ：http://www.daomubiji.com/
 
```
 
- 准备工作xpath
 
```python
1、一级页面xpath：
a节点: //li[contains(@id,"menu-item-20")]/a
title: ./text()
link : ./@href
 
2、二级页面
  基准xpath ：//article
  for循环遍历后:
    name=article.xpath('./a/text()').get()
    link=article.xpath('./a/@href').get()
 
3、三级页面xpath：response.xpath('//article[@class="article-content"]/p/text()').extract()
# 结果: ['p1','p2','p3','']
 
```
 
- **项目实现**
 
**1、创建项目及爬虫文件**
 
```python
1、创建项目 ：scrapy startproject Daomu
2、创建爬虫 ：
   1、cd Daomu
   2、scrapy genspider daomu www.daomubiji.com
 
```
 
**2、定义要爬取的数据结构 - items.py**
 
```python
import scrapy
 
 
class DaomuItem(scrapy.Item):
    # 确定pipelines处理数据时需要哪些数据
    # 1. 一级页面标题 - 创建文件夹需要
    title = scrapy.Field()
    # 2. 二级页面标题 - 创建文件需要
    name = scrapy.Field()
    # 3. 小说内容
    content = scrapy.Field()
 
```
 
**3、爬虫文件实现数据抓取 - daomu.py**
 
```python
# -*- coding: utf-8 -*-
import scrapy
from ..items import DaomuItem
 
class DaomuSpider(scrapy.Spider):
    name = 'daomu'
    allowed_domains = ['www.daomubiji.com']
    start_urls = ['http://www.daomubiji.com/']
 
    # 解析一级页面 - 链接+title
    def parse(self, response):
        # 基准xpath
        a_list = response.xpath('//li[contains(@id,"menu-item-20")]/a')
        for a in a_list:
            item = DaomuItem()
            item['title'] = a.xpath('./text()').get()
            link = a.xpath('./@href').get()
            # 扔给调度器入队列
            yield scrapy.Request(
                url=link,
                # 不同解析函数之间传递数据
                meta={'item':item},
                callback=self.parse_two_page
            )
 
    # 解析二级页面函数 : 名称(七星鲁王 第一章 血尸)+链接
    def parse_two_page(self,response):
        # 获取item
        item = response.meta['item']
        article_list = response.xpath('//article')
        for article in article_list:
            name = article.xpath('./a/text()').get()
            two_link = article.xpath('./a/@href').get()
            # 继续交给调度器入队列
            yield scrapy.Request(
                url=two_link,
                meta={'item':item,'name':name},
                callback=self.parse_three_page
            )
 
    # 解析三级页面: 小说内容
    def parse_three_page(self,response):
        item = response.meta['item']
        item['name'] = response.meta['name']
        # p_list: ['段落1','段落2','段落3']
        p_list = response.xpath('//article[@class="article-content"]//p/text()').extract()
        content = '\n'.join(p_list)
        item['content'] = content
 
        yield item
```
 
**4、管道文件实现数据处理 - pipelines.py**
 
 
```python
import os
 
class DaomuPipeline(object):
    def process_item(self, item, spider):
        # item['title']:  盗墓笔记1:七星鲁王宫
        # item['name']:   七星鲁王 第一章 血尸
        # item['content']: 具体小说内容
        # directory: /home/tarena/novel/盗墓笔记1：七星鲁王宫/
        directory = '/home/tarena/novel/{}/'.format(item['title'])
        if not os.path.exists(directory):
            os.makedirs(directory)
 
        filename = directory + item['name'] + '.txt'
        with open(filename,'w') as f:
            f.write(item['content'])
 
        return item
```
 
### 腾讯招聘
 
- **1、创建项目+爬虫文件**
 
```python
scrapy startproject Tencent
cd Tencent
scrapy genspider tencent hr.tencent.com
 
# 一级页面(postId):
https://careers.tencent.com/tencentcareer/api/post/Query?timestamp=1566266592644&countryId=&cityId=&bgIds=&productId=&categoryId=&parentCategoryId=&attrId=&keyword={}&pageIndex={}&pageSize=10&language=zh-cn&area=cn
 
# 二级页面
https://careers.tencent.com/tencentcareer/api/post/ByPostId?timestamp=1566266695175&postId={}&language=zh-cn
 
```
 
- **2、定义爬取的数据结构**
 
```python
# 名称+类别+职责+要求+地址+时间
job_name = scrapy.Field()
job_type = scrapy.Field()
job_duty = scrapy.Field()
job_require = scrapy.Field()
job_address = scrapy.Field()
job_time = scrapy.Field()
 
```
 
- **3、爬虫文件**
 
```python
import scrapy
from urllib import parse
import requests
import json
from ..items import TencentItem
 
class TencentSpider(scrapy.Spider):
    name = 'tencent'
    allowed_domains = ['careers.tencent.com']
    one_url = 'https://careers.tencent.com/tencentcareer/api/post/Query?timestamp=1566266592644&countryId=&cityId=&bgIds=&productId=&categoryId=&parentCategoryId=&attrId=&keyword={}&pageIndex={}&pageSize=10&language=zh-cn&area=cn'
    two_url = 'https://careers.tencent.com/tencentcareer/api/post/ByPostId?timestamp=1566266695175&postId={}&language=zh-cn'
    headers = { 'User-Agent':'Mozilla/5.0' }
    # 重写start_requests()
    keyword = input('请输入职位类别:')
    keyword = parse.quote(keyword)
 
    def start_requests(self):
        total = self.get_total()
        # 生成一级页面所有页的URL地址,交给调度器
        for index in range(1,total+1):
            url = self.one_url.format(self.keyword,index)
            yield scrapy.Request(
                url=url,
                callback=self.parse_one_page
            )
    # 获取总页数
    def get_total(self):
        url = self.one_url.format(self.keyword, 1)
        html = requests.get(url=url, headers=self.headers).json()
        count = html['Data']['Count']
        if count % 10 == 0:
            total = count // 10
        else:
            total = (count // 10) + 1
 
        return total
 
    # 一级页面:提取postid
    def parse_one_page(self,response):
        html = json.loads(response.text)
        for one in html['Data']['Posts']:
            # postId ,two_url
            postid = one['PostId']
            url = self.two_url.format(postid)
            yield scrapy.Request(
                url=url,
                callback=self.parse_two_page
            )
 
    # 解析二级页面
    def parse_two_page(self,response):
        item = TencentItem()
        html = json.loads(response.text)
        # 名称+类别+职责+要求+地址+时间
        item['job_name'] = html['Data']['RecruitPostName']
        item['job_type'] = html['Data']['CategoryName']
        item['job_duty'] = html['Data']['Responsibility']
        item['job_require'] = html['Data']['Requirement']
        item['job_address'] = html['Data']['LocationName']
        item['job_time'] = html['Data']['LastUpdateTime']
 
        yield item
 
```
 
- **4、管道文件**
 
```mysql
create database tencentdb charset utf8;
use tencentdb;
create table tencenttab(
job_name varchar(500),
job_type varchar(100),
job_duty varchar(2000),
job_require varchar(2000),
job_address varchar(100),
job_time varchar(100)
)charset=utf8;
 
```
 
   **管道文件pipelines.py实现**
 
```python
class TencentPipeline(object):
    def process_item(self, item, spider):
        print(dict(item))
        return item
 
import pymysql
 
class TencentMysqlPipeline(object):
    def open_spider(self,spider):
        self.db = pymysql.connect(
          'localhost','root','123456','tencentdb',charset='utf8'
        )
        self.cursor = self.db.cursor()
 
    def process_item(self,item,spider):
        ins='insert into tencenttab values(%s,%s,%s,%s,%s,%s)'
        L = [
            item['job_name'],
            item['job_type'],
            item['job_duty'],
            item['job_require'],
            item['job_address'],
            item['job_time']
        ]
        self.cursor.execute(ins,L)
        self.db.commit()
 
        return item
 
    def close_spider(self,spider):
        self.cursor.close()
        self.db.close()
 
```
 
- **5、settings.py**
 
```python
# 定义常用变量，添加管道即可
```
 
## 图片管道案例
 
### 360图片
 
- **目标**
 
```python
www.so.com -> 图片 -> 美女
```
 
- **抓取网络数据包**
 
```python
2、F12抓包,抓取到json地址 和 查询参数(QueryString)
url = 'https://image.so.com/zjl?ch=beauty&t1=595&src=banner_beauty&sn={}&listtype=new&temp=1'
ch: beauty
t1: 595
src: banner_beauty
sn: 90
listtype: new
temp: 1
 
```
 
- **项目实现**
 
**1、创建爬虫项目和爬虫文件**
 
```python
scrapy startproject So
cd So
scrapy genspider so image.so.com
 
```
 
**2、定义要爬取的数据结构(items.py)**
 
```python
img_url = scrapy.Field()
img_title = scrapy.Field()
 
```
 
**3、爬虫文件实现图片链接+名字抓取**
 
```python
import scrapy
import json
from ..items import SoItem
 
class SoSpider(scrapy.Spider):
    name = 'so'
    allowed_domains = ['image.so.com']
    # 重写start_requests()方法
    url = 'http://image.so.com/zjl?ch=beauty&sn={}&listtype=new&temp=1'
 
    def start_requests(self):
        for sn in range(0,91,30):
            full_url = self.url.format(sn)
            # 扔给调度器入队列
            yield scrapy.Request(
                url=full_url,
                callback=self.parse_image
            )
 
    def parse_image(self,response):
        html = json.loads(response.text)
        item = SoItem()
        for img_dict in html['list']:
            item['img_url'] = img_dict['qhimg_url']
            item['img_title'] = img_dict['title']
 
            yield item
 
```
 
**4、管道文件（pipelines.py）**
 
```python
from scrapy.pipelines.images import ImagesPipeline
import scrapy
 
class SoPipeline(ImagesPipeline):
    # 重写get_media_requests()方法
    def get_media_requests(self, item, info):
        yield scrapy.Request(
            url=item['img_url'],
            meta={'name':item['img_title']}
        )
 
    # 重写file_path()方法,自定义文件名
    def file_path(self, request, response=None, info=None):
        img_link = request.url
        # request.meta属性
        filename = request.meta['name'] + '.' + img_link.split('.')[-1]
 
        return filename
 
```
 
**5、设置settings.py**
 
```python
IMAGES_STORE = '/home/tarena/images/'
 
```
 
**6、创建run.py运行爬虫**
 
- **图片管道使用方法总结**
 
    ```python
    1、爬虫文件: 将图片链接yield到管道
    2、管道文件:
       from scrapy.pipelines.images import ImagesPipeline
       class XxxPipeline(ImagesPipeline):
            def get_media_requests(self,xxx):
                pass
            def file_path(self,xxx):
                pass
    3、settings.py中:
       IMAGES_STORE = '绝对路径'
    ```
 
- **文件管道使用方法总结**
 
    ```python
    1、爬虫文件: 将文件链接yield到管道
    2、管道文件:
       from scrapy.pipelines.images import FilesPipeline
       class XxxPipeline(FilesPipeline):
            def get_media_requests(self,xxx):
                pass
            def file_path(self,xxx):
                return filename
    3、settings.py中:
       FILES_STORE = '绝对路径'
    ```
 