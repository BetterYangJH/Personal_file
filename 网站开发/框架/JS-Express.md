# 基础

*   创建
    *   安装express：npm install express --save
        *   安装exprss到bode_module
        *   同时安装
            *   body-parser 处理json，raw，text和url
            *   coockie-parser 处理cookie
            *   multer 处理enctype=multipart/form-data的表单数据

*   创建文件：

    ```js
    var express =require("express");
    var app=express();
    
    app.get(path,function(req,res){
    	res.send(str);
    });
    
    app.use("/public",express.static("public"));
    var server=app.listen(port)
    ```

*   返回字符串res.send

    返回文件res.sendFile

    不能返回数字，要转换成字符串

*   启动node www或npm start

# 结构

*   网关配置bin/www

*   路由分发app.js，统一管理

    ```js
    var indexRouter=require("./routes/index");
    app.use("/",indexRouter);
    ```

*   视图函数routes，文件区分功能，暴露接口

    ```js
    router.get/post("url",function(req,res,next){});
    ```