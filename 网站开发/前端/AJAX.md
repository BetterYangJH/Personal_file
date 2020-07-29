# Ajax 

*   全称：Asynchronous Javascript And Xml ，通过 JS 异步请求和响应数据

*   使用场合：搜索建议、表单验证、前后端完全分离、局部刷新

*   AJAX核心对象 - 异步对象(XMLHttpRequest)

    *    xhr，异步对象，代替浏览器向服务器发送异步的请求并接收响应，由JS提供

    *   创建xhr：

        *   主流浏览器(IE7+,Chrome,Firefox,Safari,Opera)全部都支持 XMLHttpRequest。

        *   IE低版本浏览器中(IE6以及以下)，使用 ActiveXObject() 来创建异步对象

            ```js
            if(window.XMLHttpRequest){
            	//支持 XMLHttpRequest
            	var xhr = new XMLHttpRequest();
            }else{
            	//不支持XMLHttpRequest,使用 ActiveXObject 创建异步对象
            	var xhr = new ActiveXObject("Microsoft.XMLHTTP");
            }
            ```

# xhr 的成员 

*   方法 - open()

    *   作用：创建请求
    *   语法 ：open(method,url,asyn)
        ​			method : 请求方式，取值'get' 或 'post'
        ​			url : 请求地址，字符串（类似路由地址）
        ​			asyn : 是否采用异步的方式
        ​			           true  : 异步
        ​					   false : 同步

    ```js
    xhr.open('get','/server',true);
    ```

*   属性 - readyState

    *   作用：请求状态，通过不同的请求状态来表示xhr与服务器的交互进度

    *   状态：由0-4共5个值来表示5个不同的状态，递进

        | 数值 | 说明                       |
        | ---- | -------------------------- |
        | 0    | 请求尚未初始化             |
        | 1    | 已经与服务器建立连接       |
        | 2    | 服务器端已接收请求         |
        | 3    | 请求正在处理中             |
        | 4    | 请求处理完毕，响应已经完成 |

*   属性 - status

    *   作用：服务器端的响应状态码

    *   状态：不同状态码代码不一样的响应功能

        | 数值 | 说明                                     |
        | ---- | ---------------------------------------- |
        | 200  | 表示服务器正确处理所有的请求以及给出响应 |
        | 404  | 请求资源不存在                           |
        | 500  | 服务器内部错误                           |
        | 302  | 输出重定向                               |

*   事件 - onreadystatechange（状态，可以绑定函数，类似onclick）
    *   作用：每当xhr的readyState发生改变的时候都要触发的操作
    *   回调函数：只有当readyState的值为4并且status的值为200的时候，才可以正常的去接收响应数据

*   属性 - responseText
    
*   作用：响应数据
    
*   方法 - send()

    *   作用：通知xhr向服务器端发送请求
    *   语法：send(body)
        *   get请求：body的值为null
            send(null)
        *   post请求：此处为要提交的数据
            send("请求数据")，记得后端method修改

    ```js
    <script>
        function creat_xhr(){
        	if(window.XMLHttpRequest){
            	var xhr = new XMLHttpRequest();
        	}else{
            var xhr = ActiveXObject('Microsoft.XMLHTTP');
        	}
       		return xhr
    	}
    
        var xhr = creat_xhr();
        // 创建请求
        xhr.open('get','/02-get',true);
        // 监听状态
        xhr.onreadystatechange = function(){
    		if(xhr.readystate=4&&xhr.status=200{
               console.log(xhr.responseText)}
        	}
        	// 发送请求
        	xhr.send(null)
    	}
    </script>
    ```

#  JQUERY AJAX 

*   $.get(url,data,callback,type)
    *   作用：使用GET方式请求数据
    *   参数：
        *   url : 异步请求地址
        *   data : 请求提交的数据，可以是字符串，可以是JSON
        *   callback : 请求成功时的回调函数
        *   type : 响应回来的数据的类型
            *   html : 响应回来的文本是html文本
            *   text : 响应回来的文本是普通文本
            *   json : 响应回来的数据是JSON对象

*   $.post(url,data,callback,type)
    *   作用：使用POST方式请求数据
    *   参数：
        *   url : 异步请求地址
        *   data : 请求提交的数据，可以是字符串，可以是JSON
        *   callback : 请求成功时的回调函数
        *   type : 响应回来的数据的类型
            *   html : 响应回来的文本是html文本
            *   text : 响应回来的文本是普通文本
            *   json : 响应回来的数据是JSON对象

*   $.ajax({请求参数的JSON对象});

    *   参数对象中的属性：

        *   url : 字符串，表示异步请求的地址
        *   type : 字符串，请求方式，get 或 post
        *   data : 传递到服务器端的参数
        *   dataType : 字符串，响应回来的数据的格式
            *   'html'
            *   'xml'
            *   'text'
            *   'script'
            *   'json'
            *   'jsonp' : 有关跨域的响应格式

        *   success:回调函数，请求和响应成功时回来执行的操作
        *   error : 回调函数，请求或响应失败时回来执行的操作
        *   beforeSend : 回调函数，发送ajax请求之前执行的操作，如果return false，则终止请求发送

# 跨域

*   在HTTP中，必须是同源地址才能互相发送请求，非同源拒绝请求
    *   同源：相同协议，相同域名，相同端口被视为是"同源"
    *   跨域：非同源的网页，相互发送请求的过程，form和img天生跨域
*   原生解决方案：get
*   jquery解决方案：Ajax

