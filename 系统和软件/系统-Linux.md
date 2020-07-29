# Linux 操作系统

发行版有：Ubuntu、RedHat、CentOS、Debian、Fedora、SuSE、OpenSUSE、Arch Linux、SolusOS 等。

## Linux 操作系统结构

- Linux内核: Linux操作系统的核心代码，是Linux系统的最底层，提供了系统的核心功能并允许进程以一种有序的方式访问硬件。
- 文件系统：通常指称管理磁盘数据的系统，可将数据以目录或文件的型式存储。每个文件系统都有自己的特殊格式与功能
- shell命令：Shell是一个命令行解释器，它使得用户能够与操作系统进行交互，负责接收用户命令，然后调用相应的应用程序，并根据用户输入的指令来反馈给用户指定的信息。
- 应用软件：包含桌面系统和基础的软件操作工具等。
- 启动顺序：
    - 内核引导  - 开机BIOS自检
    - 运行init  - /etc/inittab
    - 系统初始化
    - 建立终端  - ctrl+alt+f1～6切换图形界面命令行
    - 用户登陆

# 主要目录

*   /usr - 安装目录
    *   系统程序
    *   本地安装的程序和其他东西在/usr/local 下
        *   /usr/local/bin内部的命令全局可用，ln -s path/bin /usr/local/bin使某软件的命令全局可用
*   /etc - 配置文件
    *   用户信息文件/etc/ passwd
    *   系统初始化文件/etc/rc等
    *   软件配置信息/etc/redis/redis.conf，/etc/mysql
    *   安装源/etc/apt/source.list
    *   执行文件/etc/init.d/redis-server /etc/init.d/mysql

*   /var - 备份日志等文件
    *   备份文件:/var/lib/redis/.rdb|.aof， /var/lib/mysql/
    *   日志文件: /var/log/redis/redis-server.log
*   /root目录 - 超级用户的目录

*   /home - 普通用户的默认目录，每个用户拥有一个以用户名命名的文件夹
*   /dev - 设备驱动程序，用户通过这些文件访问外部设备
    *   访问/dev/mouse来访问鼠标的输入
*   /bin - 引导启动所需的命令或普通用户可能用的命令(可能在引导启动后)

*   /sbin - 也用于存储二进制文件。其中的大部分文件多是系统管理员使用
*   /lib目录 - 根文件系统上的程序所需的共享库，包含了可被许多程序共享的代码，以避免每个程序都包含有相同的子程序的副本

## ssh服务

ssh是一种安全协议，主要用于给远程登录会话数据进行加密，保证数据传输的安全。在数据传输方面有很多应用。

> 在Linux下SSH服务端是一个在后台运行的程序，响应来自客户端的连接请求。 SSH服务端的讲程名为sshd，负责实时监听远程SSH客户端的远程连接请求，并进行处理。

- 安装 ： sudo apt-get install openssh-server

- 查看ssh服务状态 ： ps -e|grep ssh

- 启动和关闭 ：
  
  >  sudo service ssh start/restart/stop
  >  /etc/init.d/ssh start/restart/stop

#### ssh登录远程主机

> ssh [-p port] username@ip
> 退出： exit 或 ctrl-D

#### scp命令:通过安全、加密的连接在机器间传输文件。

> 把本地文件传输给远程系统：
> scp localfile  root@180.76.234.56:/newfilename
>
> 把本地文件夹传输给远程系统：
> scp -r localfile  root@180.76.234.56:/newfilename  

> 把远程文件传输给本地系统：
> scp root@180.76.234.56:/remotefile /localfile 

### ssh秘钥

每次登录远程主机都需要输入密码是很不便捷的，如果要加速这一步骤，可以利用密钥对进行连接，主要思路是：生成一对公钥私钥，私钥在local主机上，公钥在远程服务器上，每次建立ssh连接自动检查密钥对是否匹配。

#### 生产ssh秘钥步骤

> > 1. 生产秘钥对 ： ssh-keygen  执行以后会在主目录下生成一个.ssh文件夹,其中包含私钥文件id_rsa和公钥文件id_rsa.pub。
> > 2. 在服务器主机上将id_rsa.pub文件的内容附加~/.ssh/authorized_keys文件中，并修改器权限。

## shell脚本

shell在Linux系统下也是脚本语言，和python一样属于解释型语言，在Linux运维和自动化处理方面有很多应用。下面简单介绍下shell脚本。

其实shell脚本的本质是一系列命令的有序集合，创建shell脚本的过程主要分三步：

* 创建 .sh文件，写入若干行shell命令或者操作系统命令
* ss使用chmod修改可执行权限
* 直接在命令行调用执行
