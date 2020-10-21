# 基础
 
## 特点
 
- 抓取、更新调度多站点的特定的页面
- 结构化信息提取
- 可扩展
- 可监控
 
## 架构
 
* scheduler（调度器）- 单点
* fetcher（抓取器） - 分布式
* processor（脚本执行）- 分布式
 
- scheduler 发起调度，fetcher 抓取网页内容， processor 执行预先编写的python脚本，输出结果或产生新的提链任务（发往 scheduler），形成闭环。
 
## 准备
 
* 安装
 
    ```shell
    $sudo apt-get install pyspider
    ```
 
* 报错及处理
 
    * 测试是否成功
 
        ```shell
        $ pyspider
        ```
 
        出现以下界面为成功
 
        ```shell
        phantomjs fetcher running on port 25555
        [I 200312 16:20:26 result_worker:49] result_worker starting...
        [I 200312 16:20:26 tornado_fetcher:638] fetcher starting...
        [I 200312 16:20:26 processor:211] processor starting...
        [I 200312 16:20:26 scheduler:647] scheduler starting...
        [I 200312 16:20:27 scheduler:782] scheduler.xmlrpc listening on 127.0.0.1:23333
        [I 200312 16:20:27 scheduler:586] in 5m: new:0,success:0,retry:0,failed:0
        [I 200312 16:20:27 app:76] webui running on 0.0.0.0:5000
        ```
 
    * 错误类型总结及处理
 
        * WsgiDAV发布版本语法不兼容错误
 
            * 提示
 
            ```shell
            ValueError: Invalid configuration:
              - Deprecated option 'domaincontroller': use 'http_authenticator.domain_controller' instead.
            Error: Could not create web server listening on port 25555
            ```
 
            * 解决
 
            ```shell
            # 打开对应文件
            $ sudo vim/usr/local/lib/python3.5/dist-packages/pyspider/webui/webdav.py
            # 修改文件，209行左右
            'domaincontroller': NeedAuthController(app),
            改为
            'http_authenticator':{
                   'HTTPAuthenticator':NeedAuthController(app),
                },
            # 保存退出
            ```
 
        * Werkzeug版本过高
 
            * 提示
 
            ```shell
            ImportError: cannot import name 'DispatcherMiddleware'
            ```
 
            * 解决
 
            ```shell
            # 卸载原来版本
            $sudo pip uninstall werkzeug -y
            # 安装0.16.1版本
            $sudo pip install werkzeug==0.16.1
            ```
 
        * 端口占用
 
            * 提示
 
            ```shell
            Error: Could not create web server listening on port 25555
            ```
 
            * 解决
 
            ```shell
            # 查看占用端口进程
            $lsof -i:端口号
            # 结束进程
            $kill 进程PID号
            ```
 
* 启动
 
    ```shell
    $pyspider
    ```
 
* 访问
 
    `localhost:5000`
 
 
# 基础使用
 
* 创建新项目
 
    * 右上角Create 按钮
        * Project Name项目名称
        * Start URL启动链接(爬虫项目第一个访问的页面链接)
 
* 基础结构
 
    ```python
    #!/usr/bin/env python
    # -*- encoding: utf-8 -*-
    # Created on 2020-03-12 16:23:35
    # Project: test
 
    from pyspider.libs.base_handler import *
 
    class Handler(BaseHandler):
        crawl_config = {}
 
        # 脚本执行频率 - 分（每天）
        @every(minutes=24 * 60)
        # 脚本入口，run按钮执行
        def on_start(self):
            # 添加新任务到队列
            self.crawl('网址', callback=self.index_page)
 
        # 多久不重新爬取 - 秒（十天）
        @config(age=10 * 24 * 60 * 60)
        # 获取响应页面
        def index_page(self, response):
            # pyquery对象,jQuery-like选择提取元素的信息
            for each in response.doc('a[href^="http"]').items():
                self.crawl(each.attr.href, callback=self.detail_page)
 
        # 优先级
        @config(priority=2)
        # 返回一个字段对象作为结果,默认会被resultdb捕捉
        def detail_page(self, response):
            return {
                "url": response.url,
                "title": response.doc('title').text(),
            }
    ```
 
## webUI
 
* 一个接口是一个任务
* 一个任务有多个项目
    * 项目是独立的，但您可以将另一个项目作为模块导入 from projects import other_project
    * 一期工程有5个状态：TODO，STOP，CHECKING，DEBUG和RUNNING
        - TODO - 创建一个脚本来编写
        - STOP- 您可以将项目标记为STOP您希望它停止（= =）。
        - CHECKING- 修改正在运行的项目时，为防止不完整修改，项目状态将CHECKING自动设置。
        - DEBUG/ RUNNING- 这两种状态对蜘蛛没有区别。但是将它标记为DEBUG第一次运行然后将其更改RUNNING为检查后是很好的。
    * 抓取速度被控制，rate并burst用令牌桶算法
        - rate - 一秒钟内有多少请求
        - burst- 考虑到这种情况，rate/burst = 0.1/3这意味着蜘蛛每10秒抓一页。所有任务都已完成，项目每分钟检查最后更新的项目。假设找到3个新项目，pyspider将“爆发”并抓取3个任务而不等待3 * 10秒。但是，第四项任务需要等待10秒。
    * 删除项目，请设置group为delete和状态STOP，等待24小时
 
# 进阶
 
## 流程
 
```python
1 修改on_start的回调url
2 修改index_page匹配数据，标签属性都可以用.访问
3 利用CSS选择器助手点选元素并自动生成解析，替换doc内容
4 修改detail_page获取数据
 
总结：
用UI辅助定位元
用response.doc()在程序中定位元素
可以使用re,css,xpath定位元素
slef.crawl可以设置参数
```
 
## 其他
 
* 使用all模式运行
 
    ```python
    1 启动phantomjs
    $pyspider phantomjs
    2 修改模式，添加js代码辅助 on_start中
    self.crawl(
        url,
        fetch_type='js',
        js-script='''
            js_code
        '''
        callback)
    ```
 
* 常用js 代码
 
    * 模拟滚动操作
 
        ```js
        function() {                        
            window.scrollTo(0,document.body.scrollHeight);
        }
        ```
 
* tastID
 
    * 每个任务有固定tastID
 
    * 默认时md5(url)
 
    * 修改
 
        ```python
        class MyHandler(BaseHandler):
            def get_taskid(self, task)
        ```
 
* 重试延迟
 
    * 发生提取错误或脚本错误时，默认情况下该任务将重试3次。
 
    * 默认在30秒，1小时，6小时，12小时后每次执行，并且任何更多重试将推迟24小时
 
    * 修改
 
        ```python
        class MyHandler(BaseHandler):
              retry_delay = {
                  0: 30,
                  1: 1*60*60,
                  2: 6*60*60,
                  3: 12*60*60,
                  '': 24*60*60
              }
        ```
 
* on_finish
 
    ask_queue变为0时将触发该方法
 
## self.crawl API
 
从处理器的newtask_queue接收任务
 
```
- 确定任务是新任务还是需要重新爬网
- 根据优先级对任务进行排序，并将其提供给具有流量控制的提取器（令牌桶算法）
- 处理定期任务，丢失任务和失败的任务，然后重试。
```
 
* url
 
* callback
 
* age
 
* exetime
 
* itag
 
    * 增量标记，自动与之前的标记比较，不同时进行爬取，一般选取页面不同版本时会变化的值
 
* auto_recrawl
 
* params
 
    * 字典格式，给url传参
 
        ```python
        self.crawl(
            'http://httpbin.org/get',
            params={'a': 123, 'b': 'c'})
        等同于
        self.crawl('http://httpbin.org/get?a=123&b=c')
        ```
 
* method
 
    * 请求方法，默认'GET'
 
* data
 
    * POST请求时提交的数据
 
* save
 
    * 方法间传参
 
        ```python
        self.crawl(save{})
 
        response.save[key]
        ```
 
* js_script
 
    * 运行js
 
* js_run_at
 
    * 页面加载之前/后运行js
 
* load_images
 
    * js要不要加载图像
 
## fetcher
 
负责获取网页，然后将结果发送给处理器
 
```
scheduler -> fetcher -> processor
                |
            phantomjs
                |
             internet
```
 
## 处理器
 
运行用户编写的脚本来解析和提取信息
 
捕获异常和日志
 
发送状态和新任务到scheduler
 
结果发送到Result Worker
 
## Result Worker
 
内置的resultdb来保存结果
 
可以改写:
 
```python
1　逻辑实现：my_result_worker.py
from pyspider.result import ResultWorker
 
class MyResultWorker(ResultWorker):
    def on_result(self, task, result):
        assert task['taskid']
        assert task['project']
        assert task['url']
        assert result
        # your processing code goes here
2 将my_result_worker.py放到启动pyspider的文件夹
3 添加命令
$pyspider result_worker --result-cls=my_result_worker.MyResultWorker
```
 
## 响应失败
 
非200响应将被视为提取失败，不会传递给回调
 
```python
def on_start(self):
    self.crawl('http://httpbin.org/status/404', self.callback)
 
@catch_status_code_error 
def callback(self, response):
```
 
## 传参
 
* url传参
 
    ```python
    self.crawl(
        'http://httpbin.org/get',
        params={'a': 123, 'b': 'c'})
    等同于
    self.crawl('http://httpbin.org/get?a=123&b=c')
    ```
 
* POST传参
 
    ```python
    self.crawl(data={})
    ```
 
* 方法间传参
 
    ```python
    self.crawl(save{})
 
    response.save[key]
    ```
 
* 项目间传参
 
    ````python
    self.send_message(self,project_name,{})
 
    def on_message(self,project,msg)
    ````
 
 