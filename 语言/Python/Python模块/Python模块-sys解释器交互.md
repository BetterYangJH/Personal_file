***常用于对解释器的适配***

| 命令                     | 作用                     |
| :----------------------- | :----------------------- |
| sys.args                 | 命令行参数列表           |
| sys.path                 | 模块搜索路径             |
| sys.modules              | 已加载的模块字典         |
| sys.builtin_module_names | 当前解释器内置模块       |
| sys.exec_prefixpython    | 库路径前缀               |
| sys.executablepython     | 解释器可执行文件绝对路径 |
| sys.maxsize              | 容器最大长度             |
| sys.getrecursionlimit    | 解释器最大递归深度       |
| sys.copyright            | 解释器权限               |
| sys.version              | 版本信息                 |
| sys.exc_info             | 正在处理的异常信息       |
| sys.exit                 | 引发systemexit异常退出   |
| sys.getdefaultencoding   | 默认字符串编码格式       |
| sys.getrefcount(obj)     | obj的引用计数            |
| sys.getsizeof            | 对象内存大小             |
| sys.path                 | 操作脚本路径相关         |

# Tips

*   sys.path 解释器相关路径，比如环境变量，import搜索路径

    os.path 脚本相关路径，比如绝对路径，当前路径