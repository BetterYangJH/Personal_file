命令行启动简易服务器，网页访问指定host和port，可以以树状结构显示启动服务器的文件夹内的文件，并进行下载，常用于文件分享

```shell
python -m http.server port
```

文件夹内有 index.html 时，访问首页 localhost:port 会显示 index.html

文件夹内没有 index.html 时，以树状结构显示文件夹内容列表

常用于文件分享、调试前端

