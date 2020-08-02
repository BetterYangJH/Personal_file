# shell

系统登入 - 交互式shell - primary shell

执行命令 - shell的子进程 - sub shell

shell脚本 - 非交互式shell - sub shell - 执行 script 中的命令的进程

# 命令行

命令行组成：

*   cursor(游标)：当前插入位置

*   prompt(提示符)：得到prompt才能输入命令，$一般用户，#root用户
*   CR(Carriage Return)：Enter键产生，结束命令行

命令行格式：

```
prompt commandname options arguments CR
```

命令类型

*   内建命令
*   别名
*   $PATH下的外部命令
*   确定路径的指定的外部命令

执行过程

*   shell 根据IFS(Internal Field Seperator - Space、Enter、Tab)将命令行拆为字段
*   处理特殊字符meta
*   替换变量
*   重组命令行