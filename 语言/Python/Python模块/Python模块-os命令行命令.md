***常用于操作系统，类似shell脚本***

# 属性

| 命令      | 作用             |
| --------- | ---------------- |
| os.name   | 操作系统名       |
| os.getenv | 环境变量         |
| os.getcwd | 当前工作目录     |
| os.stat   | 文件属性         |
| os.utime  | 文件访问修改时间 |
| os.pardir | 当前目录父目录   |
| os.walk   | 递归获取路径     |

# 方法

| 命令          | 作用              |
| ------------- | ----------------- |
| os.system     | 执行原生shell命令 |
| os.exit       | 终止当前进程      |
| os.putenv     | 设置环境变量      |
| os.access     | 验证权限          |
| os.chmod      | 更改权限          |
| os.chown      | 更该所有者        |
| os.chdir      | 改变工作目录      |
| os.mkdir      | 创建目录          |
| os.makedirs   | 递归创建目录      |
| os.remove     | 删除文件          |
| os.removedirs | 删除文件夹        |
| os.rmdir      | 删除空目录        |
| os.rename     | 重命名            |
| os.renames    | 递归重命名        |

# os.path相关

| 命令                               | 作用                                    |
| ---------------------------------- | --------------------------------------- |
| os.path.exists                     | 文件是否存在                            |
| os.path.abspath                    | 绝对路径                                |
| os.path.dirname                    | 文件路径                                |
| os.path.commonprefix               | 多个路径最长共有前缀                    |
| os.path.basename                   | 文件名                                  |
| os.path.split                      | 分割路径为(dirname,basename)            |
| os.path.splitext                   | 分割路径为文件名+拓展名                 |
| os.path.getatime                   | 最近访问时间                            |
| os.path.getmtime                   | 最近修改时间                            |
| os.path.getctime                   | 文件创建时间                            |
| os.path.getsize                    | 文件大小                                |
| os.path.isabs                      | 是否是路径                              |
| os.path.isfile                     | 是否是文件                              |
| os.path.isdir                      | 是否是目录                              |
| os.path.islink                     | 是否是链接                              |
| os.path.ismount                    | 是否是挂载点                            |
| os.path.join                       | 符合规则的拼接路径                      |
| os.path.relpath                    | 计算相对路径                            |
| os.path.samefile                   | 判断目录文件是否相同                    |
| os.path.walk                       | 遍历,返回目录，内层目录，内层非目录文件 |
| os.path.supports_unicode_filenames | 是否支持unicode路径名                   |

# Tips

*   获取文件夹所有子文件绝对路径

    ```python
    for root,dirs,files in os.walk(path):
        for file in files:
            os.path.join(root,file)
    ```

*   os.walk：目录，目录下目录，目录下非目录= os.walk()

    os.path.walk：os.path.walk(目录，回调函数(回调参数，目录，文件列表)，回调参数)

    os.listdir：目录下非目录= os.listdir()