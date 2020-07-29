# MongoDB

*   mongodb数据库连接

*   genetic-pool连接池

```js
// 创建连接
const MongoClient=require('mongodb').MongoClient
const GenericPool=require('generic-pool')

function create(){
	return MongoClient.connect(
		'mongodb://name:password@ip:port/database an',
		{useUnifiedTopology;true}
  )
}
function destroy(client){
	client.close()
}

const nameFactory={create:create(),destroy:destroy()}
const nameOpts={max:10,min:1}
const namePool=GenericPool.createPool(nameFactory,nameOpts)
const mongoPool={namePool:namePool}
module.exports=mongoPool

// 使用
var namePool=require('./mongo_pool')
var dnCon=namePool.acquire()

dbCon.then((client)=>{
    client.db(name).collection(name)
        .find().toArray(function(err,result){})
})

//增
insertOne({},func)insertMany([{},{}],func)
//改:有更新无新增
updateOne/Money({where},{$set:{update}},func)
//删
deleteOne/Money(where,func)
//查
find({}).toArray(func)
```

*   使用双数据库查询：闭包函数

# MySQL

```js
var mysql=require('mysql')
var connection=MySQL.createConnection({
	host:
	user:
	password
	database
})
Connection.connect()

var sql='insert into values(?,?)
var params=[xxx,yyy]
connection.query(sql,Parma,function(err,result){})
```

