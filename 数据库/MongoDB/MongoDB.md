一种基于磁盘存储的非关系型数据库,数据以键值对方式存储，值为JSON文档。和redis相比redis基于内存，数据类型丰富(字符串、列表、哈希、集合、有序集合)

# 命令

## 基础

```shell
# 启动
进入bin执行./mongodb -dbpath=自建data目录
# 不占用命令窗口
linux： nohup mongodb >
windows： start /min mongodb >
# 进入
进入bin执行 ./mongo

# 将bin添加环境变量可全局访问
ls -n path/bin /usr/local/bin
```

## 库操作

```shell
# 增
$ use name    
# 删
$ db.dropDatabase()
# 查
$ show dbs　#查看库
$ db.stats() #库状态
# 进入
$ use name
$ mongo name
```

## 集合操作

```shell
# 增
$ db.createCollection("name")
# 删
$ db.name.drop()
# 查
$ show collections;　- 查看当前库中所有集合
# 重命名
$ db.name.renameCollection(new_name)
# 统计文档数量
$ db.name.count()
```

## 文档操作

```shell
# 增
$ db.name.insert()

# 删
$ db.name.update({},{$unset:{"istop":true}}); 
# 清空
$ db.name.remove({})
# 删除指定文档
$ db.name.remove({"":""})

# 改(<filter>, <update>, <options>)
$ db.name.update({},{$set:{"":""}}); #更改字段值,没有新增
$ db.name.update({},{"":""});  #把文档所有数据覆盖
$ db.name.update({},{$set:{"":""}}); #只更新第一条
$ db.name.update({},{$set:{"":""}},{multi: true}) #全更
$ db.name.update({},{"":"","":""},{upsert:true}) #有跟新没有追加

$ db.name.update({},{$inc:{"":num}});#增加
$ db.name.update({},{$mul:{"":num}});#相乘
$ db.name.update({},{$rename:{"rank":"score"}});# 改名

# 查看文档({选择})
$ db.name.find() #查看所有
$ db.name.find({'':'','':''}) #包含指定字段指定值的文档
$ db.name.find({'':/regex/) #正则匹配
# 条件查询({'':{条件}})
$ db.name.find({'':{$gt/gte/lt/lte:num}}) #大于小于等于
$ db.name.find({'':{$in:[]}}) #范围查找
$ db.name.find({"": {$exists: true} }); #存在字段
$ db.name.find({}, {title:true/1}); #指定字段
$ db.name.find({$or: [{'':''}, {'':''}] }); #与或非
# 查询结果处理(().fun操作)
$ db.name.find().sort({rank:1/-1}); #升序/降序
$ db.name.find().limit(3);　#分页
$ db.name.find().skip(3);　#从skip数量之后显示
$ db.name.find().pretty();　#格式化显示
$ db.name.findOne(); #只显示第一条，等同于limit(1)
# 比较文档，列出字段所有不重复数据
$ db.distinct('name')
```

## 索引

```shell
# 查看索引
$ db.posts.getIndexes();
# 创建索引(1升序-1降序)
$ db.posts.createIndex({rank:-1});
$ db.posts.createIndex({title:1}, {unique:true});# 唯一
# 删除索引
$ db.posts.dropIndex({rank:-1});
```

## 储存

```shell
# 备份
$ mongodump -d ku-name
# 恢复
$ mongorestore --drop
```

# python交互

```python
# MongoDB
import pymongo
conn = pymongo.MongoClient('address',27017)
db = conn['']
myset = db['']
myset.命令
# updateOne有则更新无则新增
```
