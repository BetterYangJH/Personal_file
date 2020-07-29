*   自动任务

    *   Linux

        ```shell
        # 编辑
        crontab 选项 
        # 设置/etc/crontab
        分 时 天 月 周 命令位置 文件位置 【重定向》name 2〉&1 &】
        选项-e编辑-l列表-r删除
        时间可选 *任意 -范围 or或者 
        ```

    *   Windows

        ```shell
        计算机右键-管理-任务计划程序
        程序或脚本：python解释器路径user/appdata/local/programes/python/python3.7/python.exe
        起始于：脚本绝对路径
        ```

*   后台执行

    *   Linux

        ```shell
        nohup command >file 2>&1 & # 过程写入file，错误就输出
        
        nohup：除非kill，永远执行，断开ssh也不影响
        参数：0 stdin 1 stdout 2 stderr
        重定向：> 覆盖（每次启动重新记）>>追加
        &后台执行
        ```

    *   Windows

        ```shell
        start /min command>outfile 2>&1 &
        ```

        