# 基础

*   服务端运行的js，基于chrome js的运行时的平台，异步，单进程单线程
*   基础语法参照JavaScript

-   npm install，uninstall，list，search，update
    -   install不加参数默认依赖到的全安装
    -   安装在node_module
    -   镜像cnpm --registry=https://registry.npm.taobao.org相当于pip -i

*   每个包内package.json定义包属性

*   发布自己的包
    *   npm adduser 
    *   npm init 
    *   npm publish

*   执行

    node server.js

# 对象变量函数

*   全局对象：所有地方都能访问，js的windows，nodejs的global
*   原型：js文件的最外一层，文件内函数外
*   构造函数：function name(){定义属性和函数}
*   全局变量：
    *   最外层变量：用户不能定义最外层变量，因为用户代码都在当前模块，模块本身不是最外层上下文
    *   全局对象属性：定义一个全局变量时，这个变量同时也会成为全局对象的属性
        *   __filename当前脚本名称
        *   __dirname当前脚本目录

*   process：类似sys，捕捉进程状态，改变输入输出

    *   process.on（条件，function）
        *   条件：exit退出，beforeExit异步调用没有进程时node不退出，uncaughtException发生异常，Singnle信号

    *   改变输入输出process.stdin/out/err.write/read
    *   改变参数process.args.forEach
    *   版本/版本加依赖process.version/versions
    *   进程号/名process.pid/title
    *   CPU/系统process.arch/platform

*   全局函数

    *   var st=setTimeout（fun，ms）一次性定时器

    *   clearTimeout（st）解除定时器

    *   var ci=setInterval（fun，ms）循环定时器

    *   clearInterval（ci）接触定时器

    *   console打印台输出

        *   log普通info蓝色惊叹号warn黄色惊叹号error红色惊叹号 console.log（“1%d3） 1%d3

            console.log（“1%d3”，2） 123

    *   time（）开始计时

    *   timeEnd（）结束计时并输出

    *    dir（obj）检查对象并输出属性

    *   assert（表达式，msg）表达式为false时输出msg

*   回调函数：外层函数返回值传入到回调函数

    function（args，function（err，res）｛｝）

*   模块

    *   引用第三方
        *   安装npm install -g
        *   引用require（name）

    *   文件相互引用

        ```js
        暴露 file.js中
        exports.name=function 
        引用 其他文件中
        var xxx=require（file.js）
        xxx.name()
        xxx相当于exports
        
        暴露 file.js中
        function（this.name）
        module.export=function
        引用 其他文件中
        var X = require（file）
        xxx =new X（）
        xxx.name
        X相当于exports，xxx相当于对象
        ```

## 常用模块

*   http:搭建服务器

    ```js
    var http=require('https')
    http.createServer(function(request,response){
      写响应头
      response.writeHead(200,{'Content-Type':'text/plain'})
      写内容
      response.write(string)
      response.end()
    }).listen(port)
    ```

*   url，querystring路径解析

    ```js
    url=''
    path=url.parse(url).pathname  port之后所有路径
    query=url.parse(url).query 查询字符串
    querystring.parse(query)[] 获取指定查询字符串
    ```

*   fs文件操作：每个方法都有同步xxxSync和异步xxx无阻塞版本，异步第一个参数为错误信息

    ```js
    打开文件open（path，mode，callback(err，fd){})
    mode：默认读readFile默认写writeFile
    
    文件属性stat(path，callback)
    截取文件ftruncate(path，callback)
    删除文件unlink(path，callback)
    关闭文件close(fd)
    创建读取删除目录mkdir/readdir/rmdir(path,[option],callback)
    ```

*   正则

    ```js
    var reg=RegExp()
    var str=''
    str.match(reg)[]
    ```

*   url常用函数

*   callbackify异常处理

*   inherits继承
    *   基于原型，通过原型复制实现，不是基于类
    *   只继承原型中函数showName，不继承构造函数function Base的属性和函数
    *   inspect将任意对象转化成字符串，能显示对象信息
    *   isArray/iaRegExp/isDate

# 其他

*   多进程

    ```js
    const child_process=require('child_process');
    var workerProcess=child_process(
        'node support.js'+I,function(error,stdout,at deer){});
    workerProcess.on('exit',function(code){}))
    
    或
    var workerProcess=child_process.spawn('node',['support.js',i]);
    workerProcess.stdout.on('data',function(){})
    workerProcess.stderr.on('data',function(){})
    workerProcess.on('close',function(){})
    ```