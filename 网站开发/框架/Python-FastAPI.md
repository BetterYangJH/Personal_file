# 基础

*   安装

    ```shell
    pip install fastapi/uvicorn
    ```

*   基本框架

    ```python
    from fastapi import FastAPI
    import uvicorn
    from starlette.requests import Request
    from starlette.staticfiles import StaiticFiles
    from starlette.templating import Jinja2Templates
    
    app=FastAPI()
    app.mount("/static",StaiticFiles(directory="static"),name="static")
    tenplates=Jinja2Templates(directory="templates")
    
    @app.get("/{is}")
    async def function(request:Request,id:star):
    	return templates.TemplateResponse(
            "item.html",
            {"request":request,"id":id}
        )
    
    if __name__=='__main__':
    	uvicorn.run(app,host='127.0.0.1',port=8080)
    ```

*   启动服务

    ```shell
    uvicorn main：app --reload
    ```

*   交互文档，接口测试 127.0.0.1:8000/docs

*   API文档 127.0.0.1:8000/redoc

*   返回值
    *   返回字符串 return
    *   返回html  template

# 参数

*   get

    类型：isn't,str,path,UUID,date time,date time.date,date time.time,date time.time delta,froze set,bytes,decimal,emailstr

    ```python
    @app.get("{name}")
    def function(name:类型=默认值）
    	name
    ```

*   post

    从request.body获取的json格式

*   Query,Path,Cookie,Body,Header获取相应内容

*   通过类对post或get参数进行默认值或类型限制

    ```python
    class Name(BaseModel):
    	xxx:类型=默认值
            
    @app.post(path)
    def function(name:Name):
    	name[xxx]
    ```

# 其他

*   路径：从上到下匹配第一个符合的，所以前面特殊，后面通用

*   app.get/post参数
    *   path
    *   summary功能概述
    *   description详细描述
    *   response_model=classname控制返回值

*   json转换：jsonable_encode()

*   中间件

    ```python\
    @app.middleware("http")
    async def function(request:Request,call_next):
    	starttime=time.time()
    	response=await call_next(request)
    	processtime=time.time()-starttime
    	response.headers["X-Process-Time"]=str(processtime)
    	return response
    ```

*   跨域

    ```python
    from fastapi.middleware.cors import CORSMiddelware
    app=FastAPI()
    
    origins=[path1,path2]
    
    app.add_middleware=[
    	CORSMiddleware,
    	allow_origins=origins,
    	allow_credentials=True,
    	allow_method=["*"],
    	allow_headers=["*"]
    ]
    ```