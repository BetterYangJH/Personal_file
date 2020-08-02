# GitTips

*   配置git

    ```shell
    git config --global user.name用户名
    git config --global user.email邮箱
    git config --global color.ui true命令颜色
    ```

*   配置文件
    *   全局配置：.git/config
    *   当前用户配置：/usr/name/.gitconfig

# Github

## 贡献

1.  Fork到自己仓库
2.  从自己仓库clone到本地
3.  本地进行修改，并推送到自己的分仓库
4.  pull request

# Git服务器

1.  安装git

    ```shell
    apt-get install git
    ```

2.  创建git专用用户，用于隔离管理git服务

    ```shell
    adduser git
    ```

3.  创建公钥文件，内容为白名单账户，白名单可看可改，非白名单可看不可改

    ```shell
    vim /home/git/,ssh/authorized_keys
    ```

4.  初始化git，创建裸仓，只用于文件共享中转站

    ```shell
    git init --bare sample.git
    chown -R git:git sample.git
    ```

5.  禁用shell，出于安全考虑，可以使用ssh登录git，但不能登录shell

    ```shell、
    /etc/passwd
    git:x:1001:1001:,,,:/home/git:/bin/bash
    改为
    git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell
    ```

6.  克隆使用

    ```shell
    git clone git@server:/srv/sample.git
    ```

    

    