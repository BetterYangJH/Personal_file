# 基础知识
 
* 用途
    * 分布式的实时文件存储
    * 分布式的实时分析搜索引擎
    * 用于全文搜索、结构化搜索、分析以及将这三者混合使用
* 使用方式
    * 通过RESTful API、客户端、命令行(映射成请求)
* 数据存储
    * 均衡分布在集群各个节点的分片中
    * 可以扩展到上百台服务器，处理 PB 级结构化或非结构化数据
* 基于 [Apache Lucene(TM)](https://lucene.apache.org/core/) 的开源搜索引擎
    * Lucene 是迄今为止最先进、性能最好的、功能最全的搜索引擎库
    * 使用 Java 来作为开发语言并将其直接集成到你的应用中
    * Elasticsearch解决Lucene问题：
        * 多节点数据通信
        * 服务器宕机数据不丢失(高可用，HA)
        * 高性能的建立索引以及执行搜索
* 端口配置
    * 版本：2.4.6
    * 端口：127.0.0.1：9200
    * 配置：elastucsearch.ym
 
# 安装及启动
 
## 安装JAVA
 
```python
1 获得官方提供的最新版本的 Java
2 解压安装包jdk-8u231-linux-x64
    tar -xzvf jdk-8u231-linux-x64
3 将解压后的文件夹移到/usr/lib目录下
    cd  /usr/lib
    sudo mkdir jdk
    sudo mv jdk1.8.0_231 /usr/lib/jdk
4 配置java环境变量
    sudo vi ~/.bashrc
    添加
    # set java env
    export JAVA_HOME=/usr/lib/jdk/jdk1.8.0_231
    export PATH=$JAVA_HOME/bin:$PATH
5 执行命令使修改立即生效
    source ~/.bashrc
```
 
## 安装Elasticsearch
 
我们所使用的版本是6.6.2，该版本较为稳定。我们首先安装 es：
 
```shell
tar -zxvf elasticsearch-6.6.2.tar.gz
```
 
解压后的目录文档如图所示：
 
![image-20200107182928832](images/es_2.png)
 
打开 elasticsearc-6.6.2/config/elasticsearch.yml 文件：
 
![image-20191028102057816](images/es_3.png)
 
如果需要其他 ip 访问服务，则将 127.0.0.1 修改为 0.0.0.0
 
出于系统安全考虑的设置 ，elasticsearch 不允许 root 用户启动，所以在启动之前需要创建一个非root用户：
 
```shell
创建一个新用户，用于启动elasticsearch
1）创建新用户es
    useradd es
2)设置权限(如不设置后期es服务会因为无法写入log日志而宕机)
    chown -R es:es elasticsearch路径
3)切换至用户es
    su es
4)修改文件夹权限
    chmod 770 elasticsearch路径
```
 
配置完毕后，运行 es，执行命令 elasticsearch-6.6.2/bin/elasticsearch
 
注意：后台启动使用 elasticsearch-6.6.2/bin/elasticsearch -d
 
![image-20200102150556326](images/es_start.png)
 
会出现 started ... 的字样则，在浏览器出入 127.0.0.1:9200，会出现如下 json 样式：
 
```python
{
  "name" : "XPjxWjR",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "Xr3au7fvSFaxRcJq9hhmNA",
  "version" : {
    "number" : "6.6.2",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "3bd3e59",
    "build_date" : "2019-03-06T15:16:26.864148Z",
    "build_snapshot" : false,
    "lucene_version" : "7.6.0",
    "minimum_wire_compatibility_version" : "5.6.0",
    "minimum_index_compatibility_version" : "5.0.0"
  },
  "tagline" : "You Know, for Search"
}
```
 
至此，es 服务启动完毕。
 
# 原理
 
- 反向索引又叫倒排索引，是根据文章内容中的关键字建立索引。
- 搜索引擎原理就是建立倒排索引。
- Elasticsearch 中的索引(Index)、类型(type)和文档(Document)的概念比较重要，类似于 MySQL 中的数据库、表和行，仅仅是类似，但并不能划等号。
 
#### 什么是全文检索？
 
例如数据库里面的数据共有1000W条，要做一次关于 SKU 副标题是否含有“手提包”关键字搜索的需求，如果按照关系型数据库的思路进行搜索的话，需要对这1000W条的数据进行一次扫描，需要匹配caption字段所有的字符，确认是否包含搜索的关键词，而且还不能将搜索词拆解开进行搜索。
 
#### 关系型数据库搜索和es搜索的区别
 
##### 关系型数据库：
 
![mysql作为搜索引擎](images/mysql作为搜索引擎.png)
 
##### Elasticsearch搜索
 
提到Elasticsearch首先提到的就是倒排索引，什么是倒排索引呢？
 
![Es搜索](images/Es搜索.png)
 
利用倒排索引进行搜索，假设有10W个SKU且根据sku_name进行搜索，倒排索引也就是根据这10W个name生成的，假设有100W个分词，映射在倒排索引中也就是100W行。但这并不意味着我们就要进行100W次搜索，例如我搜索“红袖”，利用上图生成的倒排索引，只需要查找一次就能找出需要的数据。
 
#### Elasticsearch 基础概念
 
##### 1. Index(索引)
 
是具有稍微类似特征文档的集合。类似于关系型数据库中的 database。
 
##### 2. Type(类型)
 
在 **Index**中，可以定义一个或多个类型。一个类型是索引中一个逻辑的种类/分区，它的语义完全取决于您自己。一般情况下，一个类型被定义成一组常见字段的文档。类似于关系型数据库中的 table。
 
##### 3. Document(文档)
 
是索引信息的基本单位。一个document可以是一条客户数据，一条商品分类数据，一条订单数据，通常用JSON数据结构表示，每个index下的type中，都可以去存储多个document。一个document里面有多个field，每个field就是一个数据字段。
 
```python
# document
{
  "sku_id": 1,
  "sku_name": "红袖添香红色大尺寸",
  "sku_caption": "红袖添香红色大尺寸真好看啊啊啊啊啊"
}
```
 
##### 4. Near Realtime(近实时)
 
**Elasticsearch **是一个近实时的搜索平台。这意味着从您索引一个文档开始直到它可以被查询时会有轻微的延迟时间（通常为一秒）。
 
##### 5. Cluster(集群)
 
是一个或者多个节点的集合，它们一起保存数据并且提供所有节点联合索引以及搜索功能。集群存在一个唯一的名字身份且默认为 “**elasticsearch**”。这个名字非常重要，因为如果节点启动时通过它自己的名字加入到集群中的话，那么一个节点只能是一个集群中的一部分。
 
##### 6. Node(节点)
 
是一个单独的服务器，它是集群的一部分，存储数据，参与集群中的索引和搜索功能。一个节点通过启动时默认分配的一个随机的 UUID名称来识别。默认节点会去加入一个名称为 “**elasticsearch**”的集群
 
##### 7. Shard(分区)
 
primary shard简称shard，单台机器无法存储大量数据，es可以将一个索引中的数据切分为多个 shard，分布在多台服务器上存储。有了shard就可以横向扩展，存储更多数据，让搜索和分析等操作分布到多台服务器上去执行，提升吞吐量和性能。
 
##### 8. Replica(副本)
 
replica shard简称replica。为每个shard创建多个replica副本。replica可以在shard故障时提供备用服务，保证数据不丢失，多个replica还可以提升搜索操作的吞吐量和性能。默认每个索引10个shard，5个primary shard，5个replica shard，最小的高可用配置，是2台服务器。
 
![shard和replica](images/shard和replica.png)
 
# 基础架构
 
#### Elasticsearch对复杂分布式机制的透明隐藏特性
 
Elasticsearch是一套分布式系统，分布式是为了应对大数据量。Elasticsearch隐藏了复杂的分布式机制：
 
- 分片：我们之前随随便便就将一些document插入到es集群中去了，我们没有关心过数据是如何进行分配的、数据到哪个shard中去了
 
- 集群发现机制(cluster discovery)：如果启动一个新的es进程，那么这个es进程会作为一个node并且发现es集群，然后自动加入进去
- shard负载均衡：假设现在有3个节点，总共有25个shard要分配到3个节点上去，es会自动进行均分分配，以保证每个节点的均衡的读写负载请求
 
#### 节点变化时的rebalance
 
比如现在有4个node，其中3个node中有一个shard，1个node中有2个shard，但是这个时候如果有一个新的node加入进来，则es会自动把其中一个shard分配到刚加入的node上去
 
#### master节点
 
一个es集群中总会有一个node是master，默认情况自动选出一台node
 
- 管理es集群的元数据：比如说索引的创建和删除、维护索引元数据；节点的增加和移除、维护集群的数据
- master节点不承载所有的请求，只会处理一些最基础的元数据信息，所以不存在单点瓶颈问题
 
#### 节点平等的分布式架构
 
- 节点对等，每个节点都能接收所有的请求
- 自动请求路由：任何一个节点接收到请求后，都可以把这个请求自动路由到相关节点上去处理该请求
- 响应收集：最原始节点会从其他节点接收响应数据，然后把这些数据返回给客户端
 
![es基础架构](images/es基础架构.png)
 
#### Primary Shard 和 Replica Shard
 
* 集群－节点(主从)－shard(primary replica)－document
 
- 一个索引(index)包含多个shard，每个shard都是一个最小工作单元，承载部分数据，具有完整的建立索引和处理请求的能力
 
    ![shard和replica基础](images/shard和replica基础.png)
 
- 增减节点时，shard会自动在集群中中负载均衡
 
    ![es负载均衡](images/es负载均衡.png)
 
- primary shard和replica shard，每个document肯定只存在于某一个primary shard以及其对应的replica shrad中，不可能存在于多个primary shard
 
- replica shard是primary shard的副本，负责容错，以及承担读请求负载
 
- primary shard的数量在创建索引固定，replica shard的数量随时修改
 
- primary shard的默认数量是5，replica shrad默认数量是1
 
- primary shard不能和自己的replica shard放在同一个节点上（否则节点宕机时，primary shard和replica shard都丢失了，起不到容错的作用。），但是可以和其它primary shard的replica shard放在同一个节点上
 
    ![shard和replica存放机制](images/shard和replica存放机制.png)
 
#### 单节点环境下创建index
 
- 单node环境下，创建一个index：有3个primary shard、3个replica shard
- 集群状态是yellow
- 这个时候，只会将3个primary shard分配到仅有的一个node上去，另外3个replica shard是无法分配的
- 集群可以正常工作，但是一旦出现节点宕机，数据全部丢失，而且集群状态变为不可用，无法承担任何请求
 
#### 两个节点环境下replica shard是如何分配的
 
此时的情况，1个node、3个primary shard、3个replica shard
 
![两个node环境下replica shard是如何分配的](images/两个node环境下replica shard是如何分配的.png)
 
#### Elasticsearch横向扩容
 
##### primary shard 和 replica shard自动负载均衡
 
目前情况：2个node， 3个primary shard，3个replica shard
 
<img src="images/es_4.png" alt="image-20191225153601928" style="zoom:80%;" />
 
如果此时给es集群增加一个node，es会自动对shard和replica进行负载均衡
 
<img src="images/es_5.png" alt="image-20191225154009074" style="zoom:80%;" />
 
扩容之后，每个节点的shard数量减少，意味着每个shard可以占用的node上的资源就会更多，性能会更好
 
扩容的极限，6个shard(3个primary shard，3个replica shard)，最多扩容到6台机器，每个shard可以占用单台服务器的所有资源，性能最好
 
#### Elasticsearch容错机制
 
**master选举、replica容错、数据恢复**
 
目前es集群情况：3个node，9个shard(3个primary shard,6个replica shard)
 
<img src="images/es集群master宕机.png" alt="es集群master宕机" style="zoom:80%;" />
 
**容错第一步：master选举，自动选举另外一个node成为新的master，承担起master的责任来**：
 
<img src="images/es_6.png" alt="image-20191225155532900" style="zoom:80%;" />
 
此时，node02通过自动选举变成master节点
 
**容错第二步：新master将丢失的primary shard的某个replica shard提升为primary shard，此时集群状态会变为Yellow，因为所有的primary shard都变成了active status，但是，少了一个replica shard，所以不是所有的replica shard都是active**
 
<img src="images/es_7.png" alt="image-20191225160556918" style="zoom:80%;" />
 
**容错第三步：重启故障的node01，node02节点将会把缺失的副本都copy一份到该node上去，而且该node会使用之前已有的shard数据，只是同步一下宕机之后发生的改变**
 
<img src="images/es_8.png" alt="image-20191225161053825" style="zoom:80%;" />
 
此时es集群的状态为green，因为所有的primary shard和replica shard都是active状态
 
#### Elasticsearch中的Mapping映射
 
##### 什么是映射类型？
 
从Elasticsearch的第一版开始，每个文档都存储在单个索引中并分配了单个映射类型。 映射类型用于表示要建立索引的文档或实体的类型，例如，电商这个大的索引可能具有用户类型和订单类型。
 
每种映射类型都可以具有自己的字段，因此用户类型可以具有user_name字段和email字段，而订单类型可以具有order_id字段，商品名称字段，以及与用户类型类似的 user_name 字段，因为某一个订单是属于某一个用户的。
 
每个文档都有一个包含类型名称的_type元字段，并且可以通过在URL中指定类型名称将搜索限制为一种或多种类型，例如：
 
```python
GET /dadashop/user,order/_search
{
  "query": {
    "match": {
      "user_name": "guoxiaonao"
    }
  }
}
```
 
##### 为什么要删除Type类型？
 
最初，我们谈到“索引”类似于SQL数据库中的“数据库”，而“类型”等同于“表”。
 
这是一个不好的类比，导致了错误的假设。在关系型数据库中，表彼此独立。一个表中的列与另一表中具有相同名称的列无关。映射类型的字段不是这种情况。
 
在Elasticsearch索引中，在不同映射类型中具有相同名称的字段在内部由相同的Lucene字段支持。换句话说，使用上面的示例，用户类型中的user_name字段存储在与订单类型中的user_name字段完全相同的字段中，更通俗的讲好比是两个变量指向同一块内存区域。。
 
最重要的是这种现象导致数据稀疏并干扰Lucene有效压缩文档的能力。
 
由于这些原因，我们决定从Elasticsearch中删除映射类型type的概念。
 
##### 映射类型的替代
 
###### 1. 依据文档类型创建索引
 
第一种选择是为每个文档类型创建一个索引。代替将推文用户和订单存储在单个dadashop索引中，可以将用户存储在用户索引中，将订单存储在订单索引中。索引彼此完全独立，因此索引之间的字段类型不会发生冲突。
 
这种方法有两个好处：
 
- 数据更可能是密集的，因此可以从Lucene中使用的压缩技术中受益。
- 在全文搜索中用于评分的术语统计更可能是准确的，因为同一索引中的所有文档都代表一个实体。
 
每个索引的大小可以根据将包含的文档数量进行适当调整：可以为用户使用较少数量的primary shard，并为订单使用较大数量的primary shard。
 
###### 2. 自定义类型字段
 
一个集群中可以存在多少个primary shard是有限制的，因此我们可能不希望浪费整个shard来存这些文档。在这种情况下，可以实现自定义类型字段，该字段的工作方式与旧_type相似。
 
**原来的方式：**
 
```python
PUT /dadashop
{
  "mappings": {
    "user": {
      "properties": {
        "user_name": { "type": "keyword" },
        "email": { "type": "keyword" }
      }
    },
    "order": {
      "properties": {
        "sku_name": { "type": "text" },
        "user_name": { "type": "keyword" }
      }
    }
  }
}
 
PUT /dadashop/user/zhangsan
{
  "user_name": "zhangsan",
  "email": "zhangsan@tedu.com"
}
 
PUT /dadashop/order/1
{
  "user_name": "zhangsan",
  "sku_name": "红袖添香红色大尺寸"
}
 
GET /dadashop/order/_search
{
  "query": {
    "match": {
      "user_name": "zhangsan"
    }
  }
}
```
 
**自定义类型的做法：**
 
```python
PUT /dadashop
{
  "mappings": {
    "_doc": {
      "properties": {
        "type": { "type": "keyword" },
        "user_name": { "type": "keyword" },
        "email": { "type": "keyword" },
        "sku_name": { "type": "text" }
      }
    }
  }
}
 
PUT /dadashop/_doc/user-zhangsan
{
  "type": "user",
  "user_name": "zhangsan",
  "email": "zhangsan@tedu.cn"
}
 
PUT /dadashop/_doc/order-1
{
  "type": "order",
  "user_name": "zhangsan",
  "sku_name": "红袖添香红色大尺寸"
}
 
GET /dadashop/_search
{
  "query": {
    "bool": {
      "must": {
        "match": {
          "user_name": "zhangsan"
        }
      },
      "filter": {
        "match": {
          "type": "order"
        }
      }
    }
  }
}
```
 
##### 关于Type各个版本的区别
 
**Elasticsearch 5.6.0**
在索引上设置index.mapping.single_type：true将启用按索引的单一类型行为，该行为将在6.0中强制执行。
**Elasticsearch 6.x**
在5.x中创建的索引将像在5.x中一样继续在6.x中运行。
在6.x中创建的索引仅允许每个索引使用一种类型。该类型可以使用任何名称，但是只能有一个。首选的类型名称是_doc，因此索引API具有与7.0中相同的路径：PUT {index} / _ doc / {id}和POST {index} / _ doc
**Elasticsearch 7.x**
不建议在请求中指定类型。 例如，索引文档不再需要文档类型。 对于显式ID，新的索引API是PUT {index} / _ doc / {id}，对于自动生成的ID，则是POST {index} / _ doc。
 
更多mapping内容请见：https://www.elastic.co/guide/en/elasticsearch/reference/6.6/removal-of-types.html#_index_per_document_type
 
#### 基于version进行乐观锁并发控制
 
##### 宏观认识：
 
第一次创建一个document的时候，它的version内部版本号就是1；以后，每次对这个document执行修改或者删除操作，都会对这个_version版本号自动加1；
 
```python
{
  "_index" : "spu",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 1, # version版本号初始为1
  "_seq_no" : 0,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "sku_id" : 1,
    "sku_name" : "adidas bag big red",
    "spu_id" : 1,
    "price" : 100
  }
}
```
 
我们随后讲该文档删除 DELETE /spu/_doc/1，删除后再次创建，待创建完成后再次发送GET请求：
 
```python
PUT /spu/_doc/1
{
    "sku_id" : 1,
    "sku_name" : "adidas bag big red",
    "spu_id" : 1,
    "price" : 100
}
 
GET spu/_doc/1
{
  "_index" : "spu",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 3, # 版本号变为3
  "_seq_no" : 2,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "sku_id" : 1,
    "sku_name" : "adidas bag big red",
    "spu_id" : 1,
    "price" : 100
  }
}
```
 
我们会发现，在删除一个document之后，可以从一个侧面证明，它不是立即物理删除掉的，因为它的一些版本号等信息还是保留着的。先删除一条document，再重新创建这条document，其实会在delete version基础之上，再把version号加1。
 
###### 实验：
 
（1）PUT document：
 
```python
PUT /spu/_doc/4
{
    "sku_id" : 4,
    "sku_name" : "lv bag big gray",
    "spu_id" : 2,
    "price" : 350
}
```
 
（2）模拟两个客户端，都获取到了同一条数据：
 
```python
GET /spu/_doc/4
```
 
（3）其中一个客户端，先更新了一下这个数据，更新时带上版本号，将价格改为400：
 
```python
POST /spu/_doc/4/_update?version=1
{
  "doc": {
    "price": 400
  }
}
```
 
es中的数据的版本号，跟客户端中的数据的版本号是相同的，才能修改
 
（4）另外一个客户端尝试基于version=1去进行数据，结果失败：
 
```python
POST /spu/_doc/4/_update?version=1
{
  "doc": {
    "price": 500
  }
}
```
 
结果：
 
```python
{
  "error": {
    "root_cause": [
      {
        "type": "version_conflict_engine_exception",
        "reason": "[_doc][4]: version conflict, current version [3] is different than the one provided [1]",
        "index_uuid": "Sj4FQVWqSD-9EbNeCLcCpQ",
        "shard": "2",
        "index": "spu"
      }
    ],
    "type": "version_conflict_engine_exception",
    "reason": "[_doc][4]: version conflict, current version [3] is different than the one provided [1]",
    "index_uuid": "Sj4FQVWqSD-9EbNeCLcCpQ",
    "shard": "2",
    "index": "spu"
  },
  "status": 409
}
```
 
##### External version
 
每个索引的文档都有一个版本号。 默认情况下，使用内部版本控制，该版本从1开始，并随着每次更新（包括删除）而递增。 可以选择将版本号设置为外部值。 要启用此功能，应将version_type设置为external。 提供的值必须是大于或等于0且小于9.2e + 18左右的值。
 
使用外部版本类型时，系统检查传递给索引请求的版本号是否大于当前存储文档的版本。 如果为true，将为文档建立索引并使用新的版本号。 如果提供的值小于或等于存储的文档的版本号，则将发生版本冲突，并且索引操作将失败。
 
###### 演示
 
（1）PUT document：
 
```python
PUT /spu/_doc/5
{
    "sku_id" : 5,
    "sku_name" : "lv bag big red",
    "spu_id" : 2,
    "price" : 1000
}
```
 
（2）模拟两个客户端，都获取到了同一条数据：
 
```python
GET /spu/_doc/5
```
 
（3）其中一个客户端，先更新了一下这个数据，更新时带上版本号，将价格改为400：
 
```python
PUT /spu/_doc/5?version=30&version_type=external
{
  "doc": {
    "price": 100
  }
}
```
 
此时，version版本号变为30
 
（4）另外一个客户端尝试基于version=2去进行数据，结果失败：
 
```python
PUT /spu/_doc/5?version=2&version_type=external
{
  "doc": {
    "price": 1000
  }
}
```
 
结果：
 
```python
{
  "error": {
    "root_cause": [
      {
        "type": "version_conflict_engine_exception",
        "reason": "[_doc][5]: version conflict, current version [30] is higher or equal to the one provided [2]",
        "index_uuid": "Sj4FQVWqSD-9EbNeCLcCpQ",
        "shard": "1",
        "index": "spu"
      }
    ],
    "type": "version_conflict_engine_exception",
    "reason": "[_doc][5]: version conflict, current version [30] is higher or equal to the one provided [2]",
    "index_uuid": "Sj4FQVWqSD-9EbNeCLcCpQ",
    "shard": "1",
    "index": "spu"
  },
  "status": 409
}
```
 
**注意：**EXTERNAL不支持update API
 
# 原生操作
 
* 查信息
 
    所有命令 /_cat
 
    索引相关  /_cat/indices?v （v表头）
 
    集群相关  /_cat/aliases?v
 
    节点相关  /_cat/master?v
 
    分片相关  /_cat/allocation?v
 
* 操作数据　－　http://127.0.0.1:9200/索引/类型/文档
 
    操作哪一层写到哪一层
 
    新增、更新POST:有就更新，没有就新增
 
    查询GET
 
    删除DELETE
 
#### 集群管理
 
es提供了一套api，叫做cat api，可以查看es中各种各样的数据
 
- ##### 集群的健康状态
 
    GET /_cat/health?v
 
```shell
epoch      timestamp cluster       status node.total node.data shards pri relo init unassign pending_tasks max_task_wait_time active_shards_percent
1577092575 09:16:15  elasticsearch yellow          1         1     16  16    0    0       15             0                  -                 51.6%
 
green：每个索引的primary shard和replica shard都是active状态的
yellow：每个索引的primary shard都是active状态的，但是部分replica shard不是active状态，处于不可用的状态
red：不是所有索引的primary shard都是active状态的，部分索引有数据丢失了
```
 
其他 Elasticsearch 的 HTTP API 请见：https://www.elastic.co/guide/en/elasticsearch/reference/current/rest-apis.html
 
#### 新增文档，建立索引
 
**方式一：**
 
格式：
 
```python
PUT /index/type/doc_id
{
  "json数据"
}
```
 
演示：
 
```python
PUT /spu/sku/1
{
  "sku_id": 1,
  "sku_name": "adidas bag big red",
  "spu_id":1
}
```
 
结果：
 
```python
{
  "_index" : "spu",
  "_type" : "sku",
  "_id" : "1",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 0,
  "_primary_term" : 1
}
```
 
结果解析：
 
```
_index：文档所属的index
_type：文档所属的类型
_id：文档id
_version：文档的版本号，增删改都会影响版本号，具体细节在后文展示
result：语句执行结果
_shards：请求打到了几个切片上
    total：总共打到的shard数量
    successful：成功的shard数量
    failed：失败的shard数量
_seq_no：严格递增的顺序号，每个文档一个，Shard级别严格递增，保证后写入的Doc的_seq_no大于先写入的Doc的_seq_no
_primary_term：文档所在的shard编号，每当Primary Shard发生重新分配时，比如重启，Primary选举等，_primary_term会递增1。主要是用来恢复数据时处理当多个文档的_seq_no一样时的冲突，避免Primary Shard上的写入被覆盖
```
 
疑问1：为什么total为2，而successful为1呢？
 
```python
原因：
首先查看下当前index的配置为，查看的方式为：
GET /spu/_settings
结果：
{
  "spu" : {
    "settings" : {
      "index" : {
        "creation_date" : "1577095111409",
        "number_of_shards" : "5", # shard的数量
        "number_of_replicas" : "1", # replica的数量
        "uuid" : "ee-S38QUROOORotPOvDUsQ",
        "version" : {
          "created" : "6060299"
        },
        "provided_name" : "spu"
      }
    }
  }
}
可以看出shard的个数为5，replica的数量为1。所以该PUT请求会打到一个primary shard上进行处理，但是PUT操作是新增数据的写操作，所以该shard对应的replica也会同步数据。所以PUT操作会打到两个shard上。为什么只成功了一个呢？因为我们是单节点启动，shard和该shard的replica无法部署在同一个节点上，所以replica的状态并不是active，同步数据失败。
所以PUT操作的结果才为1个successful，0个failed。
```
 
疑问2：那如何修改shard和replica的数量呢？
 
```python
只要index创建了，shard的数量就不能修改了，所以shard只能在创建index时指定，但是replica的数量还可以修改。
方式：
PUT /spu/_settings
{
  "settings": {
    "number_of_replicas": 0
  }
}
 
结果：
{
  "acknowledged" : true
}
 
修改完之后再次查询：
GET /spu/_settings
 
结果：
{
  "spu" : {
    "settings" : {
      "index" : {
        "creation_date" : "1577095111409",
        "number_of_shards" : "5",
        "number_of_replicas" : "0", # replica数量变为0
        "uuid" : "ee-S38QUROOORotPOvDUsQ",
        "version" : {
          "created" : "6060299"
        },
        "provided_name" : "spu"
      }
    }
  }
}
```
 
注意：es会自动建立index和type，不需要提前创建，而且es默认会对document每个field都建立倒排索引，让其可以被搜索。
 
我们继续添加几个sku文档
 
```python
PUT /spu/sku/2
{
  "sku_id": 2,
  "sku_name": "lv bag small red",
  "spu_id":2
}
 
PUT /spu/sku/3
{
  "sku_id": 3,
  "sku_name": "lv bag small gray",
  "spu_id":2
}
```
 
**方式二：**
 
格式：
 
```python
POST /index/type
{
  "json数据"
}
```
 
演示：
 
```python
POST /test_index/test_type
{
  "test_field": "test_value"
}
```
 
结果：
 
```python
{
  "_index" : "test_index",
  "_type" : "test_type",
  "_id" : "fFEzPG8BLe2aiv-c0kqK",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 0,
  "_primary_term" : 1
}
```
 
post方式与put的区别，自动生成的id，长度为20个字符，URL安全，base64编码，GUID，分布式系统并行生成时不可能发生冲突
 
#### 检索文档
 
格式：
 
```python
GET /index/type/doc_id
```
 
演示：
 
```python
GET /spu/sku/1
```
 
结果：
 
```python
{
  "_index" : "spu",
  "_type" : "sku",
  "_id" : "1",
  "_version" : 1,
  "_seq_no" : 0,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "sku_id" : 1,
    "sku_name" : "adidas bag big red",
    "spu_id" : 1
  }
}
```
 
结果解析：
 
```
_index：文档所属的index
_type：文档所属的类型
_id：文档id
_version：文档的版本号，增删改都会影响版本号，具体细节在后文展示
_seq_no：文档序列号，具体内容在后文
_primary_term：文档所在的shard编号
found：搜索是否成功
_source：搜索内容
```
 
失败结果演示：
 
```python
{
  "_index" : "spu",
  "_type" : "sku",
  "_id" : "100",
  "found" : false
}
```
 
#### 修改—替换文档
 
格式：
 
```python
PUT /index/type/doc_id
```
 
演示：sku_name中的部分内容从red改到green
 
```python
PUT /spu/sku/1
{
  "sku_id": 1,
  "sku_name": "adidas bag big green",
  "spu_id":1
}
```
 
结果：
 
```python
{
  "_index" : "spu",
  "_type" : "sku",
  "_id" : "1",
  "_version" : 2,
  "result" : "updated",
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 1,
  "_primary_term" : 1
}
```
 
结果解析：
 
```
主要看一下_version，_seq_no两个字段，他们都增加了1：
_seq_no：严格递增的顺序号，每个文档一个，shard级别严格递增，保证后写入的Doc的_seq_no大于先写入的Doc的_seq_no。任何类型的写操作，包括index、create、update和Delete，都会生成一个_seq_no。这个是不可改的
_version：_version这个版本号是可以改的，可以使用version_type=external，具体内容在后文说明。
```
 
必须和源数据field数相同，既只有一部分不同，否则会被覆盖
 
失败演示：
 
```python
PUT /spu/sku/1
{
  "sku_name": "adidas bag big green 1111"
}
结果：_source中的数据发生了覆盖
{
  "_index" : "spu",
  "_type" : "sku",
  "_id" : "1",
  "_version" : 3,
  "_seq_no" : 2,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "sku_name" : "adidas bag big green 1111"
  }
}
```
 
#### 修改—更新文档
 
格式：
 
```python
POST /index/type/doc_id/_update
```
 
演示：
 
```python
POST /spu/sku/1/_update
{
  "doc": {
    "sku_name": "adidas bag big green 2333"
  }
}
```
 
执行结果：
 
```python
{
  "_index" : "spu",
  "_type" : "sku",
  "_id" : "1",
  "_version" : 7,
  "result" : "updated",
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 6,
  "_primary_term" : 1
}
```
 
查询数据：GET /spu/sku/1
 
```python
{
  "_index" : "spu",
  "_type" : "sku",
  "_id" : "1",
  "_version" : 7,
  "_seq_no" : 6,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "sku_id" : 1,
    "sku_name" : "adidas bag big green 2333",
    "spu_id" : 1
  }
}
```
 
注意：与PUT请求的区别就是可以不用带上所有的 field，可以指定 field 进行修改
 
#### 删除文档
 
格式：
 
```python
DELETE /index/type/doc_id
```
 
演示：
 
```python
DELETE /spu/sku/1
```
 
结果：
 
```python
{
  "_index" : "spu",
  "_type" : "sku",
  "_id" : "1",
  "_version" : 8,
  "result" : "deleted",
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 7,
  "_primary_term" : 1
}
```
 
再次查询：GET /spu/sku/1，删除成功，"found" : false
 
```python
{
  "_index" : "spu",
  "_type" : "sku",
  "_id" : "1",
  "found" : false
}
```
 
#### 搜索文档
 
##### (1) 使用 REST request URI，search参数都是以http请求的query string来附带的
 
- 搜索全部sku
 
  格式：
 
  ```python
  GET /index/type/_search
  ```
 
  演示：
 
  ```python
  GET /spu/sku/_search
  ```
 
  结果：
 
  ```python
  {
    "took" : 5,
    "timed_out" : false,
    "_shards" : {
      "total" : 5,
      "successful" : 5,
      "skipped" : 0,
      "failed" : 0
    },
    "hits" : {
      "total" : 2,
      "max_score" : 1.0,
      "hits" : [
        {
          "_index" : "spu",
          "_type" : "sku",
          "_id" : "2",
          "_score" : 1.0,
          "_source" : {
            "sku_id" : 2,
            "sku_name" : "lv bag small red",
            "spu_id" : 2
          }
        },
        {
          "_index" : "spu",
          "_type" : "sku",
          "_id" : "3",
          "_score" : 1.0,
          "_source" : {
            "sku_id" : 3,
            "sku_name" : "lv bag small gray",
            "spu_id" : 2
          }
        }
      ]
    }
  }
  ```
 
  结果解析：
 
  ```
  took：耗费了几毫秒
  timed_out：是否超时，这里是没有
  _shards：数据拆成了5个分片，所以对于搜索请求，会打到所有的primary shard（或者是它的某个replica shard也可以）
  hits.total：查询结果的数量，2个document
  hits.max_score：score的含义，就是document对于一个search的相关度的匹配分数，越相关，就越匹配，分数也高
  hits.hits：包含了匹配搜索的document的详细数据
  ```
 
- 搜索sku_name中带有 lv 的sku，并按照sku_id倒序排序
 
  演示：
 
  ```python
  GET /spu/sku/_search?q=sku_name:lv&sort=sku_id:desc
  ```
 
  结果：
 
  ```python
  {
    "took" : 6,
    "timed_out" : false,
    "_shards" : {
      "total" : 5,
      "successful" : 5,
      "skipped" : 0,
      "failed" : 0
    },
    "hits" : {
      "total" : 2,
      "max_score" : null,
      "hits" : [
        {
          "_index" : "spu",
          "_type" : "sku",
          "_id" : "3",
          "_score" : null,
          "_source" : {
            "sku_id" : 3,
            "sku_name" : "lv bag small gray",
            "spu_id" : 2
          },
          "sort" : [
            3
          ]
        },
        {
          "_index" : "spu",
          "_type" : "sku",
          "_id" : "2",
          "_score" : null,
          "_source" : {
            "sku_id" : 2,
            "sku_name" : "lv bag small red",
            "spu_id" : 2
          },
          "sort" : [
            2
          ]
        }
      ]
    }
  }
  ```
 
 
##### (2) Query DSL
 
Elasticsearch提供标准RESTful风格的查询DSL(Domain Specified Language，特定领域的语言)来定义查询。可以将查询 DSL 看作是由两种子句组成的查询的 AST (Abstract Syntax Tree) ：
**Leaf query clauses**
叶查询语句（可以理解为SQL里的where查询）在特定字段中查找特定值，例如 match，term或 range查询，这些查询同时也可以单独使用
**Compound query clauses**
复合查询语句可以组合Leaf Query和Compound Query，用于以组合多个查询 ，或者更改其行为
 
**案例演示：**
 
(1) 查询所有sku
 
演示：
 
```python
GET /spu/sku/_search
{
  "query": {"match_all": {}}
}
```
 
结果：
 
```python
{
  "took" : 3,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 2,
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "spu",
        "_type" : "sku",
        "_id" : "2",
        "_score" : 1.0,
        "_source" : {
          "sku_id" : 2,
          "sku_name" : "lv bag small red",
          "spu_id" : 2
        }
      },
      {
        "_index" : "spu",
        "_type" : "sku",
        "_id" : "3",
        "_score" : 1.0,
        "_source" : {
          "sku_id" : 3,
          "sku_name" : "lv bag small gray",
          "spu_id" : 2
        }
      }
    ]
  }
}
```
 
(2) 查询名称包含lv的sku，并按照sku_id降序排序
 
演示：
 
```python
GET /spu/sku/_search
{
  "query": {
    "match": {
      "sku_name": "lv"
    }
  },
  "sort": [
    {
      "sku_id": {
        "order": "desc"
      }
    }
  ]
}
```
 
结果：
 
```python
{
  "took" : 25,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 2,
    "max_score" : null,
    "hits" : [
      {
        "_index" : "spu",
        "_type" : "sku",
        "_id" : "3",
        "_score" : null,
        "_source" : {
          "sku_id" : 3,
          "sku_name" : "lv bag small gray",
          "spu_id" : 2
        },
        "sort" : [
          3
        ]
      },
      {
        "_index" : "spu",
        "_type" : "sku",
        "_id" : "2",
        "_score" : null,
        "_source" : {
          "sku_id" : 2,
          "sku_name" : "lv bag small red",
          "spu_id" : 2
        },
        "sort" : [
          2
        ]
      }
    ]
  }
}
```
 
(3) 指定要查询出来sku的sku_name和sku_id
 
演示：
 
```python
GET /spu/sku/_search
{
  "query": {"match_all": {}},
  "_source": ["sku_id", "sku_name"]
}
```
 
结果：
 
```python
{
  "took" : 3,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 2,
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "spu",
        "_type" : "sku",
        "_id" : "2",
        "_score" : 1.0,
        "_source" : {
          "sku_id" : 2,
          "sku_name" : "lv bag small red"
        }
      },
      {
        "_index" : "spu",
        "_type" : "sku",
        "_id" : "3",
        "_score" : 1.0,
        "_source" : {
          "sku_id" : 3,
          "sku_name" : "lv bag small gray"
        }
      }
    ]
  }
}
```
 
注意：更加适合生产环境的使用，可以构建复杂的查询
 
##### (3) Filter
 
query关注点：此文档与此查询子句的匹配程度如何？
 
filter关注点：此文档和查询子句匹配吗？
 
**案例演示：**
 
搜索sku的sku_name含有lv并且sku_id大于2
 
演示：
 
```python
GET /spu/sku/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "sku_name": "lv"
          }
        }
      ],
      "filter": {
        "range": {
          "sku_id": {
            "gt": 2
          }
        }
      }
    }
  }
}
```
 
结果：
 
```python
{
  "took" : 34,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1,
    "max_score" : 0.2876821,
    "hits" : [
      {
        "_index" : "spu",
        "_type" : "sku",
        "_id" : "3",
        "_score" : 0.2876821,
        "_source" : {
          "sku_id" : 3,
          "sku_name" : "lv bag small gray",
          "spu_id" : 2
        }
      }
    ]
  }
}
```
 
##### (4) 高亮搜索—Highlight Search
 
**案例演示：**
 
演示：
 
```python
GET /spu/sku/_search
{
  "query": {
    "match": {
      "sku_name": "gray"
    }
  },
  "highlight": {
    "pre_tags" : [ "<b>" ],
    "post_tags" : [ "</b>" ],
    "fields": {
      "sku_name": {}
    }
  }
}
```
 
结果：
 
```python
{
  "took" : 2,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1,
    "max_score" : 0.2876821,
    "hits" : [
      {
        "_index" : "spu",
        "_type" : "sku",
        "_id" : "3",
        "_score" : 0.2876821,
        "_source" : {
          "sku_id" : 3,
          "sku_name" : "lv bag small gray",
          "spu_id" : 2
        },
        "highlight" : {
          "sku_name" : [
            # 在highlight中会将match中的关键字高亮显示
            "lv bag small <b>gray</b>"
          ]
        }
      }
    ]
  }
}
```
 
注意：pre_tags和post_tags可加可不加，如果不加会使用默认的高亮模式 :
 
```python
"highlight" : {
  "sku_name" : [
    "lv bag small <em>gray</em>"
  ]
}
```
 
##### (5) 聚合操作
 
###### 计算每个spu下的商品数量
 
首先将之前adidas的sku添加到index中
 
```python
PUT /spu/sku/1
{
  "sku_id": 1,
  "sku_name": "adidas bag big red",
  "spu_id":1
}
```
 
演示：
 
```python
GET /spu/sku/_search
{
  "aggs": {
    "group_by_spu": {
      "terms": { "field": "spu_id" }
    }
  }
}
```
 
注意：由于结果显示无用的信息较多，添加 "size": 0 即可过滤掉无用的信息
 
```python
GET /spu/sku/_search
{
  "size": 0,
  "aggs": {
    "group_by_spu": {
      "terms": { "field": "spu_id" }
    }
  }
}
```
 
结果：
 
```python
{
  ......
  "aggregations" : {
    "group_by_spu" : {
      "doc_count_error_upper_bound" : 0,
      "sum_other_doc_count" : 0,
      "buckets" : [
        {
          "key" : 2,
          # spu_id 为 2的sku的数量为2
          "doc_count" : 2
        },
        {
          "key" : 1,
          # spu_id 为 1的sku的数量为1
          "doc_count" : 1
        }
      ]
    }
  }
}
```
 
 
 
###### 计算每组spu下的平均价格
 
首先我们将这三个sku添加price属性，利用post的_update接口来更新文档
 
```python
POST /spu/sku/1/_update
{
  "doc": {
    "price": 100
  }
}
 
POST /spu/sku/2/_update
{
  "doc": {
    "price": 150
  }
}
 
POST /spu/sku/3/_update
{
  "doc": {
    "price": 300
  }
}
```
 
演示：
 
```python
GET /spu/sku/_search
{
  "aggs": {
    "group_by_spu": {
      "terms": {
        "field": "spu_id"
      },
      "aggs": {
        "spu_avg_price": {
          "avg": {
            "field": "price"
          }
        }
      }
    }
  }
}
```
 
结果：
 
```python
{
  "took" : 39,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 3,
    "max_score" : 0.0,
    "hits" : [ ]
  },
  "aggregations" : {
    "group_by_spu" : {
      "doc_count_error_upper_bound" : 0,
      "sum_other_doc_count" : 0,
      "buckets" : [
        {
          # 根据 spu_id 来计算平均价格
          "key" : 2,
          "doc_count" : 2,
          "spu_avg_price" : {
            "value" : 225.0
          }
        },
        {
          # 根据 spu_id 来计算平均价格
          "key" : 1,
          "doc_count" : 1,
          "spu_avg_price" : {
            "value" : 100.0
          }
        }
      ]
    }
  }
}
```
 
##### (6) Bulk API
 
Bulk API 可以在一次 API 调用中包含多个索引操作，例如更新索引，删除索引等，相当于批量操作。
 
格式：
 
```python
POST /_bulk
{ "index" : { "_index" : "test", "_type" : "_doc", "_id" : "1" } }
{ "field1" : "value1" }
{ "delete" : { "_index" : "test", "_type" : "_doc", "_id" : "2" } }
{ "create" : { "_index" : "test", "_type" : "_doc", "_id" : "3" } }
{ "field1" : "value3" }
{ "update" : {"_id" : "1", "_type" : "_doc", "_index" : "test"} }
{ "doc" : {"field2" : "value2"} }
 
```
 
示例：
 
```python
POST /_bulk
{"index":{"_index":"test_index","_type":"_doc","_id":"100"}}
{"test_field1":86,"test_field2":"test big red"}
{"index":{"_index":"test_index","_type":"_doc","_id":"110"}}
{"test_field1":87,"test_field2":"test big green"}
 
```
 
现在提一下这种不同寻常的json的：x-ndjson
 
```
action_and_meta_data\n
optional_source\n
action_and_meta_data\n
optional_source\n
....
action_and_meta_data\n
optional_source\n
```
 
（1）不用将其转换为json对象，不会出现内存中的相同数据的拷贝，直接按照换行符切割json
（2）对每两个一组的json，读取meta，进行document路由
（3）直接将对应的json发送到node上去
 
最大的优势在于，不需要将json数组解析为一个JSONArray对象，形成一份大数据的拷贝，浪费内存空间，尽可能地保证性能
 
##### (7) Mutil Get API
 
Multi get API根据索引，类型，（可选）和id（以及可能的路由）返回多个文档。 响应包括一个docs数组（如果特定的get失败，则在响应中包含包含此错误的对象）。 成功获取的结构与get API提供的文档的结构类似
 
格式：
 
```python
GET /_mget
{
    "docs" : [
        {
            "_index" : "test",
            "_type" : "_doc",
            "_id" : "1"
        },
        {
            "_index" : "test",
            "_type" : "_doc",
            "_id" : "2"
        }
    ]
}
```
 
或者将index和type添加到url中：
 
```python
GET /test/_doc/_mget
{
    "docs" : [
        {
            "_id" : "1"
        },
        {
            "_id" : "2"
        }
    ]
}
----------------------------------
GET /test/_doc/_mget
{
    "ids" : ["1", "2"]
}
```
 
#### 删除机制
 
当文档被替换/删除后，不会立即进行物理删除，es会将老的文档标记为deleted，然后在es内部会新增给定的一个文档，当我们创建越来越多的文档的时候，es会在适当的时机在后台自动删除标记为deleted的document
 
# Python操作
 
## 安装 Elasticsearch 模块
 
```shell
pip3 install elasticsearch==6.3.1
```
 
## 基本操作
 
### 添加数据
 
```python
from elasticsearch import Elasticsearch
 
# 默认host为localhost,port为9200.但也可以指定host与port
# 在__init__方法中可以修改以下数据，根据情况自定义：
# def __init__(self, hosts=None, transport_class=Transport, **kwargs):
es = Elasticsearch()
 
# 添加或更新数据,index，doc_type名称可以自定义，id可以根据需求赋值,body为内容
es.index(index="test_index", doc_type="test_type", id=1, body={"name":"python","addr":"海淀"})
# 继续添加内容
es.index(index="test_index", doc_type="test_type", id=1, body={"name":"python","addr":"西二旗"})
```
 
### 查询数据
 
**注意：es添加，修改或删除记录需要一点点时间**
 
ES 的读取分为 get 和 search 两种操作，这两种读取操作有较大的差异，GET 必须指定三元组：index, type, id。也就是说，根据文档 id 从正排索引中获取内容。而 search 不指定 id，根据关键词从倒排索引中获取内容。
 
```python
# 获取索引为test_index, 文档类型为test_type, result为一个字典类型
result = es.search(index="test_index",doc_type="test_type")
for item in result["hits"]["hits"]:
    print(item["_source"])
 
# 搜索id=1的文档
result = es.get(index="test_index",doc_type="test_type",id=1)
print(result)
```
 
### 删除数据
 
```python
# 删除id=1的数据
result = es.delete(index="test_index",doc_type="test_type",id=1)
```
 
## 高阶查询
 
```python
body = {"query":{xxx}}
es.search(index,doc_type,body=body)
# 执行查询并获得获取数据量
es.count(index="test_index",doc_type="test_type")
```
 
* 字段查找
 
```python
# match:单字段
body = {
  "query":{
    "match":{
      "name":"python"
    }
  }
}
 
# multi_match:多字段
body = {
  "query":{
    "multi_match":{
      "query":"海淀",
      "fields":["name","addr"]
    }
  }
}
 
# id查找
body = {
  "query":{
    "ids":{
      "type":"test_type",
      "values":["1","2"]
    }
  }
}
```
 
* 范围查找
 
```python
# 切片式查找
body = {
    "query":{
        "match_all":{}
    },
    "from":2, # 从第2条数据开始
    "size":4 # 获取4条数据
}
```
 
* 模糊查找
 
```python
# 前缀查找
body = {
  "query":{
    "prefix":{
      "name":"p"　#查询前缀为p的所有name
    }
  }
}
# 后缀查找
body = {
  "query":{
    "wildcard":{
      "name":"p"　#查询后缀为p的所有name
    }
  }
}
```
 
* 全匹配查找
 
```python
body = {
  "query":{
    "bool":{
      "must":[
        {
          "term":{
            "name":"python"
          }
        },
        {
          "term":{
            "addr":"海"
          }
        }
      ]
    }
  }
}
# 获取name="python"addr=海淀的所有数据
 
默认分词器会将中文拆分成一个个的单个汉字
term 搜索是精确查询
match 搜索是模糊查询，底层使用的是term查询，将查询结果集根据_score进行排序，筛选出评分高的一项当作最终查询文档。
 
搜索“海淀”，会被分析为“海”和“淀”进行搜索。
match得到“海淀”,“x海”,“海x”,“x淀”,“淀x”
term得到的是空的结果，因为没有“海”和“淀”的精确查询
但是对于非文本类型，如 bool 和数字类型，或者大于小于等比较操作，使用term会非常的精准
```
 
* 聚合查询
 
数据一旦写入，索引的类型就会固定，test_index 索引第一个传入的文档类型是 string 类型，所以该索引就会关闭 Numeric detection (数字探测)
 
```python
# 最小/最大/和
body = {
  "query":{
    "match_all":{}
  },
  "aggs":{        # 聚合查询，aggs不能变
    "key":{   # 自定义名称
      "min/max/sum":{     # 最小，字段名不可自定义
        "field":"age"    # 查询"age"的最小值
      }
    }
  }
}
 
```
 
**更多的搜索用法：https://elasticsearch-py.readthedocs.io/en/master/api.html**
 
# Django操作
 
## haystack
 
同时支持whoosh，solr，Xapian，Elasticsearch四种全文检索引擎的第三方app，但是仅支持 elasticsearch 1.x版本和2.x版本，而且整个框架的可扩展性不强
 
### 需要添加索引的app下添加search_indexes.py
 
```python
from haystack import indexes
from .models import yourclass
 
class yourclassIndex(indexes.SearchIndex, indexes.Indexable):
# 每个SearchIndex都必须有且仅有⼀个document=True的字段。它向Haystack和搜索引擎指示哪个字段是要进⾏搜索的主要字段。
# use_template是指明在模板中被声明需要产⽣索引；
    text = indexes.CharField(document=True, use_template=True)
 
    def get_model(self):
    """返回建⽴索引的模型类"""
        return SKU
# 4. 修改return 可以修改返回查询集的内容，⽐如返回时，有什么条件限制
    def index_queryset(self, using=None):
    """返回要建⽴索引的数据查询集"""
    return self.get_model().objects.filter(is_launched=True)
```
 
### 创建数据模板：搜索的字段
 
```python
search/indexes/app/类名_text.txt
 
{{object.name}}
```
 
### 创建索引
 
```shell
$ python3 manage.py makemigrations
$ python3 manage.py migrate
$ python3 manage.py rebuild_index
```
 
## elasticsearch
 
有JSON接口的数据储存，全部在所需创建索引的app文件夹内进行
 
```shell
pip3 install elasticsearch==6.3.1
```
 
### es_conf.py－
 
mysql和elasticsearch的配置文件
 
```python
import os
# 保存配置文件路径，log文件的保存路径与之相同
BASE_DIR = os.path.dirname(os.path.abspath(__file__))
 
# mysql
HOST = "127.0.0.1"
USER = "root"
PASSWD = "123456"
DB_NAME = "dadashop"
PORT = 3306
 
# elasticsearch
INDEX_NAME = "dadashop"
INDEX_TYPE = "_doc"
ES_IP = "127.0.0.1"
 
# 控制每次批处理数据量
MAXIMUM = 100
```
 
### common.py
 
日志写入功能，验证ip/port有效性
 
```python
# 1.日志写入功能
def write_log(content):
    """
    写入日志文件
    :param content: 写入内容
    :return:
    """
    path = os.path.join(es_conf.BASE_DIR, "es_output.log")  # 日志文件
    with open(path, mode='a+', encoding='utf-8') as f:
        content = time.strftime('%Y-%m-%d %H:%M:%S') + ' ' + content
        print(content)
        f.write(content + "\n")
```
 
```python
# 2.验证ip有效性
def valid_ip(ip):
    """
    验证ip是否有效：256.256.256.256无效ip
    :return: bool
    """
    try:
        # 判断 python 版本
        if sys.version_info[0] == 2:
            ipaddress.ip_address(ip.strip().decode("utf-8"))
        elif sys.version_info[0] == 3:
            ipaddress.ip_address(ip)
        return True
    except Exception as e:
        print(e)
        return False
```
 
```python
# 3.验证port有效性
def check_tcp(ip, port, timeout=1):
    """
    检测tcp端口
    :param ip: ip地址
    :param port: 端口号
    :param timeout: 超时时间
    :return: bool
    """
    flag = False
    try:
        socket.setdefaulttimeout(timeout)  # 整个socket层设置超时时间
        cs = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        address = (str(ip), int(port))
 
        # 开始连接,功能与connect(address)相同，但是成功返回0，失败返回errno的值
        status = cs.connect_ex((address))
        cs.settimeout(timeout)
 
        if not status:
            flag = True
 
        return flag
    except Exception as e:
        print(e)
        return flag
```
 
### mysql_obj.py
 
封装mysql操作方法
 
```python
import pymysql
from common import write_log
 
class Mysql(object):
    # mysql 端口号,注意：必须是int类型
    def __init__(self,host,user,passwd,db_name,port=3306):
        self.host = host
        self.user = user
        self.passwd = passwd
        self.db_name = db_name
        self.port = port
 
    def select(self,sql):
        """
        执行sql命令
        :param sql: 命令
        :return: 元祖
        """
        try:
            conn = pymysql.connect(
                host=self.host,
                user=self.user,
                passwd=self.passwd,
                port=self.port,
                database=self.db_name,
                charset='utf8',
                cursorclass=pymysql.cursors.DictCursor
            )
            cur = conn.cursor()  # 创建游标
            cur.execute(sql)  # 执行sql命令
            res = cur.fetchall()  # 获取执行的返回结果
            cur.close()
            conn.close()  # 关闭mysql 连接
            return res
        except Exception as e:
            print(e)
            return False
 
    def update(self, sql):
        """
        更新操作，比如insert, delete,update
        :param sql: sql命令
        :return: bool
        """
        try:
            conn = pymysql.connect(
                host=self.host,
                user=self.user,
                passwd=self.passwd,
                port=self.port,
                database=self.db_name,
            )
            cur = conn.cursor(cursor=pymysql.cursors.DictCursor)  # 创建游标
            sta = cur.execute(sql)  # 执行sql命令，返回影响的行数
            if isinstance(sta,int):  # 判断返回结果, 是数字就是正常的
                pass
            else:
                write_log('错误，远程执行sql: %s 失败'%sql, "red")
                return False
 
            conn.commit()  # 主动提交，否则执行sql不生效
            cur.close()
            conn.close()  # 关闭mysql 连接
            return sta
        except Exception as e:
            print(e)
            return False
```
 
### es_bulk.py
 
封装es操作方法
 
Bulk API可以在一次API调用中包含多个索引操作，例如更新索引，删除索引等，相当于批量操作。
 
在es_bulk.py中创建ElasticObj类，包含几下几个功能：
 
- create_index：用于创建es索引
- bulk_insert：mysql数据批量插入es
- has_table：从mysql读取数据时判断关系表是否存在
- has_es_conf：判断mysql和es的ip和端口是否异常
- read_mysql_es：核心方法，用于mysql读取数据并写入es
 
0. 需要导入的库
 
```python
import time
from elasticsearch import Elasticsearch
from elasticsearch import helpers
import es_conf
from mysql_obj import Mysql
from common import write_log, valid_ip, check_tcp
```
 
1. 创建es索引
 
```python
class ElasticObj:
    def __init__(self, timeout=3600):
        """
        :param timeout: 超时时间
        """
        self.index_name = es_conf.INDEX_NAME  # 索引名称
        self.index_type = es_conf.INDEX_TYPE  # 索引类型
        self.es_ip = es_conf.ES_IP  # es ip
 
        # 无用户名密码状态
        self.es = Elasticsearch([self.es_ip], port=9200, timeout=timeout)
 
    # 创建es索引
    def create_index(self):
        """
        创建索引
        :return: bool
        """
        # 创建映射
        # 使用python API 创建索引跟使用Restful基本一致
        _index_mappings = {
            # 设置字段
            "mappings": {
                self.index_type: {
                    "properties": {
                        "type": {"type": "keyword"},
                        "id": {"type": "keyword"},
                        "name": {
                            "type": "text",
                            "analyzer": "ik_max_word",
                            "search_analyzer": "ik_smart"
                        },
                        "caption":{
                            "type": "text",
                            "analyzer": "ik_max_word",
                            "search_analyzer": "ik_smart"
                        }
                    }
                }
            }
        }
        # 判断索引不存在时
        # es.indices：查看当前es服务器下index
        if self.es.indices.exists(index=self.index_name) is not True:
            # 创建索引
            try:
                res = self.es.indices.create(index=self.index_name, body=_index_mappings)
            except Exception as e:
                print(e)
 
            if not res:
                write_log("错误，创建索引{}失败".format(self.index_name))
                return False
 
            write_log("正常，创建索引{}成功".format(self.index_name))
            return True
        else:
            write_log("正常，索引{}已存在".format(self.index_name))
            return True
```
 
2. es 数据批量插入
 
```python
    # 数据批量插入
    def bulk_insert(self, table, data_list):
        """
        批量写入数据
        :param table: 表名
        :param data_list: 数据列表
        :return: bool
        """
        # 批量插入
        start_time = time.time()  # 开始时间
        actions = []  # 临时数据列表
        i = 0  # 计数值
 
        try:
            # 循环数据列表
            for data in data_list:
                action = {
                    "_index": self.index_name,
                    "_type": self.index_type,
                    "_id": data["id"],
                    "_source": {
                        'type': "sku",
                        'id': data['id'],
                        'name': data['name'],
                        'caption': data['caption'],
                    }
                }
                i += 1
                actions.append(action)  # 添加到列表
                if len(actions) == es_conf.MAXIMUM:  # 列表数量达到100时
                    helpers.bulk(self.es, actions)  # 批量插入数据
                    del actions[0:len(action)]  # 删除列表元素
 
            if i > 0:  # 不足100时,插入剩余数据
                helpers.bulk(self.es, actions)
 
            end_time = time.time()  # 结束时间
            t = round((end_time - start_time), 2)  # 计算耗时
            write_log("正常，{} 表写入ES {}条数据，用时{}s".format(table, i, t))
            return True
        except Exception as e:
            print(e)
            return False
```
 
3. 判断mysql表是否存在
 
```python
    def has_table(self, db_name, target_table):
        """
        mysql表是否存在
        :return: bool
        """
        mysql_obj = Mysql(es_conf.HOST, es_conf.USER, es_conf.PASSWD, es_conf.DB_NAME, es_conf.PORT)
        sql = "select count(1) from {}.{}".format(db_name, target_table)
        res = mysql_obj.select(sql)
 
        if res is False:
            write_log("错误，mysql中 {}.{} 不存在".format(db_name, target_table))
            return False
        else:
            return True
```
 
4. 判断mysql和es的ip和端口是否异常
 
```python
    def has_es_mysql_conf(self):
        """
        判断配置文件中的mysql和es端口是否正常
        :return:
        """
        if not valid_ip(es_conf.HOST):
            write_log("错误，MySQL IP配置异常")
            return False
 
        if not valid_ip(es_conf.ES_IP):
            write_log("错误，ES IP配置异常")
            return False
 
        if not check_tcp(es_conf.HOST, es_conf.PORT):
            write_log("错误，MySQL {} 端口异常".format(es_conf.PORT))
            return False
 
        if not check_tcp(es_conf.ES_IP, 9200):
            write_log("错误，ES 9200 端口异常")
            return False
 
        return True
```
 
5. 读取 goods_sku 表的数据，并写入es
 
```python
    def read_mysql_es(self):
        """
        读取goods_sku表的数据，并写入es
        :return: bool
        """
        # 判断配置文件中的mysql和es 端口是否正常
        if not self.has_es_mysql_conf():
            return False
 
        # 创建索引
        if self.create_index() is False:
            return False
 
        max = es_conf.MAXIMUM  # 一次性查询多少条
 
        mysql_obj = Mysql(es_conf.HOST, es_conf.USER, es_conf.PASSWD, es_conf.DB_NAME, es_conf.PORT)
        res = self.has_table(es_conf.DB_NAME, "goods_sku")
        # mysql table not exist
        if not res:
            return False
        id = 0  # 每一次查询后的最大id
        while True:
            sql = "select id, name, caption from goods_sku where id > %s order by id limit %s" % (id, max)
            print("======run sql======= ", sql, " ======run sql======= \n")
            data_list = mysql_obj.select(sql)
            print("=======sql result======== ", data_list, " =======sql result======== \n")
            if not data_list:  # 当结果为空时,结束循环
                write_log("警告，执行sql: %s 记录为空，无需写入es" % (sql))
                break  # 跳出循环
 
            last_row = data_list[-1]  # 最后一行记录
            id = last_row['id']  # 修改最大id
 
            res = self.bulk_insert('goods_sku', data_list)
            if not res:
                write_log("错误，goods_sku 写入ES 失败")
                return False
 
        write_log("正常，goods_sku 表全部写入ES成功")
        return True
```
 
### views.py
 
```python
class GoodsSearchView(View):
    def post(self, request):
        """
        首页查询功能
        :param request:
        :return:
        """
        # 127.0.0.1:8000/v1/goods/search
        index_name = es_conf.INDEX_NAME  # 索引名称
        index_type = es_conf.INDEX_TYPE  # 索引类型
        es_ip = es_conf.ES_IP  # es ip
        es = Elasticsearch([es_ip], port=9200, timeout=3600)
        # 分页大小可自定义
        page_size = 9
        query = request.POST.get('q').strip()
        # print("========  query type: {}, query: {}========".format(type(query), query))
        if query:
            # es搜索
            # multi_match:在name和caption里匹配包含query关键字的数据
            body = {
                "query": {
                    "bool": {
                        "should": [
                            {
                                "multi_match": {
                                    "query": query,
                                    "fields": ["name", "caption"]
                                }
                            },
                            {
                                "term": {
                                    "id": query
                                }
                            }
                        ]
                    }
                }
            }
 
            # 查询name和addr包含 query 关键字的数据
            search_res = es.search(index=index_name, doc_type=index_type, body=body)
            # for item in search_res["hits"]["hits"]:
            #     print("--------  search_res : {} ------------".format(item['_source']))
        else:
            result = {'code': 40201, 'error': '搜索内容为空，请重新输入'}
            return JsonResponse(result)
 
        paginator = Paginator(search_res["hits"]["hits"], page_size)
        try:
            page = paginator.page(int(request.POST.get('page', 1)))
        except:
            result = {'code': 40200, 'error': '页数有误，小于0或者大于总页数'}
            return JsonResponse(result)
 
        sku_list = []
        # print(len(page.object_list))
        for result in page.object_list:
            # print(result['_source'])
            sku_id = result['_source']['id']
            try:
                sku_obj = SKU.objects.get(id=sku_id)
            except Exception as e:
                print(e)
                result = {"code": 40202, "error": "您要查询的商品不存在"}
                return JsonResponse(result)
            sku = {
                'skuid': sku_obj.id,
                'name': sku_obj.name,
                'price': sku_obj.price,
            }
            # 获取图片
            sku_image = str(sku_obj.default_image_url)
            sku['image'] = sku_image
            sku_list.append(sku)
        result = {"code": 200, "data": sku_list, 'paginator': {'pagesize': page_size, 'total': len(search_res["hits"]["hits"])}, 'base_url': settings.PIC_URL}
        return JsonResponse(result)
```
 
### admin.py
 
es数据的实时更新，与具体数据同步
 
重写 admin.ModelAdmin 中的 save_model 以及 delete_model 方法：
 
```python
@admin.register(SKU)
class SKUAdmin(BaseModel):
    def save_model(self, request, obj, form, change):
        # 入库的动作要在最开始执行，否则无法获取obj的相关数据
        super().save_model(request, obj, form, change)
        body = {
            "type": "sku",
            "id": obj.id,
            "name": obj.name,
            "caption": obj.caption,
        }
        try:
            # 这里的id=obj.id还有一个作用，如果不指定id则无法对文档进行更新操作
            es.index(index=es_conf.INDEX_NAME, doc_type=es_conf.INDEX_TYPE, id=obj.id, body=body)
        except Exception as e:
            print(e)
 
    def delete_model(self, request, obj):
        try:
            es.delete(index=es_conf.INDEX_NAME, doc_type=es_conf.INDEX_TYPE, id=obj.id)
        except Exception as e:
            print(e)
 
        # 注意：删除model的动作要在最后，不然无法获取obj的相关数据
        super().delete_model(request, obj)
```
 
# 项目启动流程
 
(1) 启动项目之前，首先启动es服务
 
```shell
./bin/elasticsearch -d
```
 
注意：-d代表后台启动
 
(2) 将mysql中的数据按照我们给定的索引规格导入es中，执行数据批处理脚本：
 
```python
python3 goods/es_bulk.py
```
 
(3) 当console中出现以下日志，代表数据导入完成：
 
```python
2020-x月-x日 x时:x分:x秒 正常，DDSC_SKU 表全部写入ES成功
```
 
(4) 启动项目
 
```python
python3 manage.py runserver
```
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 