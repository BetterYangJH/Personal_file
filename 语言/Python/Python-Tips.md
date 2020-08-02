

*   单例模式

    ```python
    class Singleton(object):
        _instance = None
        def __new__(cls,*args,**kwargs):
            if not cls._instance:
                cls._instance = super(Singleton,cls).__new__(cls,*args,**kwargs)
            retrun cls._instance
    ```

*   魔法方法

    \_\_str\_\_：print显示的内容，print(ClassName())实际是print(ClassName().\__str__())

    \__repr__：直接调用显示的内容

    \__call__：可调用对象

*   上下文管理：分配和释放资源，如open、connect结束要close

    可用自定义类实现，重写\__entre\_\_、\_\_exit__定义上下文管理器

    使用as，前面的返回值会赋给 as 后的变量

    ```python
    class File(object):
        def __init__(self,path):
            self.fb = open(path,'r+')
        def __entre__(self):
            # 赋给as后面的变量
            return self.fb  
        def __exit__（self,exc_type,exc_val,exc_tb):
            # exc_type错误类型,exc_val错误信息,exc_tb错误对象
            self.fb.close()
            if exc_val:
                return False
            return True
    
    with File(path) as fb:
    	fb.read()
    ```

*   

