# 基础

*   实现操作系统虚拟化，在宿主程序中的隔离进程运行程序及依赖，共享资源

* ```python
    * 虚拟机：物理CUP+管理程序+
    [(虚拟CPU+内核+app1+app2),(虚拟CPU+内核+app1+app2)……]
    * 容器：物理CUP+内核+
    [(app+配置)，(app１+配置)……]
    ```
    
    ![img](https://upload-images.jianshu.io/upload_images/6534887-f73947baaea7ccc7.png?imageMogr2/auto-orient/strip|imageView2/2/w/441/format/webp)

    ![img](https://upload-images.jianshu.io/upload_images/6534887-53a8d68d6e0f3af0.png?imageMogr2/auto-orient/strip|imageView2/2/w/441/format/webp)

*   结构

    1 `Docker Registry`  - n仓库 `(Repository)` (public，private)

    1仓库 - n标签 `(Tag)`

    1标签 - 1镜像 

    1镜像 - n容器（镜像 + 读写层=容器 ，容器读写，镜像只读）


![img](https://img2018.cnblogs.com/blog/1100338/201810/1100338-20181010205425908-509725301.jpg) 

*   镜像

    *   作为容器的模板，不包含动态数据，全静态
    *   包含程序，库，资源，配置，参数
    *   分层储存架构
    *   生成时一层层构建，构建完不再改变

*   容器

    *   有自己独立命名空间
    *   容器删除，其储存层销毁 ->无法持久化数据
    *   不在自己储存层读写，自己储存层无状态化
    *   文件读写在数据卷(Volume)绑定宿主目录，使数据卷生命周期独立于容器

*   结构：`C/S` (客户端/服务器)。 

    服务端：构建、运行和分发 `Docker` 镜像

    本地服务端：`Docker` 客户端和服务端可以运行在一台机器上

    远程服务端：通过 `RESTful` 、 `stock` 或网络接口通信 

* 核心组件

    *   **Docker Client**：客户端，提供命令行界面工具
    *   **Docker daemon**：服务器组件，后台运行
        *   Docker Server
        *   Engine
        *   Job
            *   向 `Docker Registry` 获取镜像
            *   通过 `graphdriver` 执行容器镜像的本地化操作
            *   通过 `networkdriver` 执行容器网络环境的配置
            *   通过 `execdriver` 执行容器内部运行的执行工作
        *   Docker Server分发路由调用函数 - Engine创建job完成请求，类似多进程
    *   **Docker Image**：镜像：文件+配置
    *   **Docker Registry**：仓库
    *   **Docker Container**：容器

* Dockerfile： 镜像的配置文件 ，用于快速创建自定义的镜像

    ```python
    # Docker Hub 上 pull 下 python 2.7 的基础镜像
    FROM python:2.7
    # 显示维护者的信息
    MAINTAINER Angel_Kitty <angelkitty6698@gmail.com>
    
    # copy 当前目录到容器中的 /app 目录下
    COPY . /app
    # 指定工作路径为 /app
    WORKDIR /app
    
    # docker build时执行，一般安装依赖
    # RUN <command>&&<command>
    # 分开多个run会添加多个层，不如一个run多个命令，
    RUN pip install -r requirements.txt
    # 暴露 5000 端口
    EXPOSE 5000
    
    # docker run时候执行
    # 一个Dockerfile只能有一个ENTRYPOINT/CMD，如果有多个，则最后一个生效,
    # docker run有参数用run参数，没有参数用CMD
    ENTRYPOINT ["executable"]
    CMD [“executable”,“param1”,“param2”] (exec 格式, 推荐的格式)
    CMD [“param1”,“param2”] (作为 ENTRYPOINT 的默认参数)
    CMD command param1 param2 (shell 格式)
    # 相当于容器一旦创建先执行python3 app.py
    ENTRYPOINT ["python3"]
    CMD ["param"]
    ```

*   安装

    ```python
    # 环境
    https://docs.docker.com/install/
    # 软件
    $ sudo apt-get update
    $ sudo apt-get install docker-ce docker-ce-cli containerd.io
    $ apt-cache madison docker-ce
    $ sudo apt-get install docker-ce=<VERSION_STRING> docker-ce-cli=<VERSION_STRING> containerd.io
    # 查看结果
    $ sudo docker run hello-world
    ```

*    镜像库 

    ```html
    https://hub.docker.com/
    ```

# 操作

*   镜像

    ```shell
    # 创建镜像
    $ docker pull name  # 拉取远程: 保存到/var/lib/docker
    $ docker build -t name .  # Dockerfile目录下执行
    # 查看镜像：
    $ docker images
    # 保存镜像(类似快照)
    $ docker commit port name
    # 备份镜像
    $ docker save 镜像名：镜像tag>name.tar
    # 导入备份
    $ docker load -input name.tar
    # 删除镜像：先停止容器再删除镜像
    $ docker stop container_name/container_id
    $ docker rm container_name/container_id
    $ docker rmi image_name
    ```

*   容器

    *   新容器

        ```shell
        $ docker run 
        —name 容器名 建议姓名加时间
        [-v localdir：容器路径] 本地目录映射，绝对路径，多盘多v不能在一个里，创建容器后不能改
        [-p localport：容器内port]本地端口映射，多口g多p，外部用local，里面写内部port
        [-d] 后台运行不进入
        -it repository：tag 镜像名标签
        [bash]启动容器后指定的命令
        ```

    *   已有容器

        ```shell
        # 查看容器及状态
        $ docker ps -a
        # 停止启动
        $ docker stop/start+前三位id
        # 进入
        $ docker exec -it id bash
        $ docker attach name/id
        docker run；创建和启动一个新的容器实例，操作对象是镜像，选项较多，如果你要创建和启动一个容器，只能用run；
        docker exec: 在已运行的容器中，执行命令，操作对象是容器，如果你要进入已运行的容器，并且执行命令，用exec；
        docker attach: 同样操作的是已运行的容器，可以将本机标准输入（键盘输入）输到容器中，也可以将容器的输出显示在本机的屏幕上，如果你想查看容器运行过程中产生的标准输入输出，用attach；
        # 关闭
        $ docker exit
        # 删除:必须已经stop
        $ docker rm
        ```

    *   其他

        ```shell
        # 远程服务器通讯
        $ docker -H 服务器IP地址 info
        # 查看文件
        $ docker name
        # 查看配置信息
        $ docker version/info
        
        Docker Daemon和Docker Client的启动都是通过可执行文件docker来完成 
        docker的main函数来解析相应flag参数，并最终完成响应启动 
        # 启动Docker-CE
        $ systemctl start docker
        # 启动 Docker Daemon 
        $ docker –d
        # 重启 Docker Daemon
        $ systemctl daemon-reload
        ```

![img](https://img2018.cnblogs.com/blog/1100338/201810/1100338-20181014202945937-1677031749.png) 

# 案例

```shell
# 拉取最新版的镜像
$ docker pull ubuntu
# 查看本地镜像
$ docker images
# 运行容器，并且可以通过 exec 命令进入容器
$ docker run -itd --name ubuntu-test ubuntu
$ docker run -itd --name mysql-test -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql
$ docker run -itd --name redis-test -p 6379:6379 redis
# 查看容器的运行信息
$ docker ps
```

