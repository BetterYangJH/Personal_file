# Tips

* 不同表列合并 join on，同表不同列但不能同时取 from x as x1,x as x2

* 行合并 union（比or快）

* 组筛选having，数据筛选where

* 要什么select 

    按什么算group by

    条件where

    最order by

    几个limit

* group by xxx

    xxx的yyy 重点在yyy，对数据分组，where yyy

    yyy的xxx 重点在xxx，对聚合分组，having yyy

* 哪些 distinct

    多少种count(distince)
    
* 每组的极值:max() from xxx group by 字段

    所有的极值(单字段):max() from xxx

    所有的极值(多字段):order by desc limit 1

    ```sql
    name id
    A    1
    B    2
    C    3
    select name,max(id) from t group by id/name
    A 1
    B 2
    C 3
    select max(id) from t
    3
    select name,max(id) from t
    A 3
    select name,id from t order by id desc limit 1;
    C 3
    ```

* count

    * COUNT(字段) 不计算NULL总记录数

    * COUNT(*/1) 计算NULL总记录数

    * COUNT(DISTINCT 字段)不计算NULL去重记录数

* 十进制x转n进制

    * x/n,余数作为倒数第一个数，商y进入下一轮
    * y/n,余数作为倒数第二个数，商y进入下一轮
    * ......
    * y<n时，y直接写作最高位

    ```text
    103
    十进制　－　二进制
    103/2 = 51-1      1
    51/2 = 25-1      11
    25/2 = 12-1     111
    12/2 = 6-0     0111
    6/2 = 3-0     00111
    3/2 = 1-1    100111
    1<2         1100111
    十进制　－　八进制0开头
    103/8 = 12-7      7
    12/8 = 1-4       47
    1<8            0147
    十进制　－　十六进制0x开头
    103/16 = 6-7      7
    6<16           0x67
    ```

* 复制表

    select 字段 into new_table from old_tbale

# MySQL性能优化

### 使用索引

1. 适合做索引的列是出现在`where`子句中经常用作筛选条件或连表子句中作为表连接条件的列。
2. 具有唯一性的列，索引效果好；重复值较多的列，索引效果差。
3. 如果为字符串类型创建索引，指定一个前缀长度，创建短索引。
4. 不要过度使用索引。索引需要占用额外的存储空间而且会影响写操作的性能
5. 索引失效的场景：模糊查询使用前置通配符、使用负向条件查询。

### 存储过程

事先编译好存储在数据库中的一组SQL的集合。减少与数据库服务器之间的通信

### 数据分区

可以存储更多的数据、优化查询，获得更大的吞吐量并快速删除过期的数据。库内分表，仅仅是解决了单表数据过大的问题，但并没有把单表的数据分散到不同的物理机上，因此并不能减轻 MySQL 服务器的压力，仍然存在同一个物理机上的资源竞争和瓶颈，包括 CPU、内存、磁盘 IO、网络带宽等。

分库分表分片

* 水平分表：
    * 取模分表
    * 时间维度分表
* 垂直分表
    * 取模分表
    * 拆分不常用字段
    * 拆分大文本字段
    * 拆分不经常修改字段

1. RANGE分区：基于连续区间范围，把数据分配到不同的分区。

    ```SQL
    CREATE TABLE tb_emp (
        eno INT NOT NULL,
        ename VARCHAR(20) NOT NULL,
        job VARCHAR(10) NOT NULL,
        hiredate DATE NOT NULL,
        dno INT NOT NULL
    )
    PARTITION BY RANGE( YEAR(hiredate) ) (
        PARTITION p0 VALUES LESS THAN (1960),
        PARTITION p1 VALUES LESS THAN (1970),
        PARTITION p2 VALUES LESS THAN (1980),
        PARTITION p3 VALUES LESS THAN (1990),
        PARTITION p4 VALUES LESS THAN MAXVALUE
    );
    ```

2. LIST分区：基于枚举值的范围，把数据分配到不同的分区。

3. HASH分区 / KEY分区：基于分区个数，把数据分配到不同的分区。

    ```SQL
    CREATE TABLE tb_emp (
        eno INT NOT NULL,
        ename VARCHAR(20) NOT NULL,
        job VARCHAR(10) NOT NULL,
        hiredate DATE NOT NULL,
        dno INT NOT NULL
    )
    PARTITION BY HASH(dno)
    PARTITIONS 4;
    ```

### SQL优化

1. 定位低效率的SQL语句 - 慢查询日志。

    - 查看慢查询日志相关配置

        ```SQL
        mysql> show variables like 'slow_query%';
        ```

    - 修改全局慢查询日志配置。

        ```SQL
        mysql> set global slow_query_log='ON'; 
        mysql> set global long_query_time=1;
        ```

        或者直接修改MySQL配置文件启用慢查询日志。

        ```INI
        [mysqld]
        slow_query_log=ON
        slow_query_log_file=/usr/local/mysql/data/slow.log
        long_query_time=1
        ```

2. 通过`explain`了解SQL的执行计划。例如：

    ```SQL
    explain select ename, job, sal from tb_emp where dno=20\G
    ```

3. 通过`show profiles`和`show profile for query`分析SQL。

    MySQL从5.0.37开始支持剖面系统来帮助用户了解SQL执行性能的细节，可以通过下面的方式来查看MySQL是否支持和开启了剖面系统。

    ```SQL
    select @@have_profiling;
    select @@profiling;
    ```

    如果没有开启剖面系统，可以通过下面的SQL来打开它。

    ```SQL
    set profiling=1;
    ```

    接下来就可以通过剖面系统来了解SQL的执行性能，例如：

    ```SQL
    mysql> select count(*) from tb_emp;
    mysql> show profiles;
    mysql> show profile for query 1;
    ```

4. 优化增删改查

    - 优化`insert`语句

        - 在`insert`语句后面跟上多组值进行插入在性能上优于分开`insert`。
        - 如果有多个连接向同一个表插入数据，使用`insert delayed`可以获得更好的性能。
        - 如果要从一个文本文件装载数据到表时，使用`load data infile`比`insert`性能好得多。

    - 优化`order by`语句

        - 如果`where`子句的条件和`order by`子句的条件相同，而且排序的顺序与索引的顺序相同，如果还同时满足排序字段都是升序或者降序，那么只靠索引就能完成排序。

    - 优化`group by`语句

        - 在使用`group by`子句分组时，如果希望避免排序带来的开销，可以用`order by null`禁用排序。

    - 优化嵌套查询

        - MySQL从4.1开始支持嵌套查询（子查询），这使得可以将一个查询的结果当做另一个查询的一部分来使用。在某些情况下，子查询可以被更有效率的连接查询取代，因为在连接查询时MySQL不需要在内存中创建临时表来完成这个逻辑上需要多个步骤才能完成的查询。

    - 优化or条件

        - 如果条件之间是`or`关系，则只有在所有条件都用到索引的情况下索引才会生效。

    - 优化分页查询

        - 分页查询时，一个比较头疼的事情是如同`limit 1000, 20`，此时MySQL已经排序出前1020条记录但是仅仅返回第1001到1020条记录，前1000条实际都用不上，查询和排序的代价非常高。一种常见的优化思路是在索引上完成排序和分页的操作，然后根据返回的结果做表连接操作来得到最终的结果，这样可以避免出现全表查询，也避免了外部排序。

            ```SQL
            select * from tb_emp order by ename limit 1000, 20;
            select * from tb_emp t1 inner join (select eno from tb_emp order by ename limit 1000, 20) t2 on t1.eno=t2.eno;
            ```

            上面的代码中，第2行SQL是优于第1行SQL的，当然我们的前提是已经在`ename`字段上创建了索引。

    - 使用SQL提示

        - USE INDEX：建议MySQL使用指定的索引。
        - IGNORE INDEX：建议MySQL忽略掉指定的索引。
        - FORCE INDEX：强制MySQL使用指定的索引。

    - 尽量选择数据类型占空间少，在where ，group by，order by中出现的频率高的字段建立索引

    - explain 放在查询语句前面可以获取查询计划，建立合适索引

    - 控制使用自定义函数

    - 子查询优化为join查询

    - 尽量避免全盘扫描

        - 尽量避免使用 select * ,不要返回用不到的任何字段 
        - 尽量避免使用like %查询，否则会全表扫描
        - 尽量避免where子句中使用 != ,否则会放弃索引全表扫描
        - 尽量避免NULL 值判断,在number列上设置默认值0
            优化前：select number from t1 where number is null;
            优化后：select number from t1 where number=0;
        - 尽量避免 or 连接条件,可以用union代替
            优化前：select id from t1 where id=10 or id=20;
            优化后： select id from t1 where id=10 union all 
                    select id from t1 where id=20;
        - 尽量避免使用 in 和 not in,否则会全表扫描
            优化前：select id from t1 where id in(1,2,3,4);
            优化后：select id from t1 where id between 1 and 4;

### 配置优化

查看MySQL服务器配置参数的默认值。

```SQL
show variables;
show variables like 'key_%';
show variables like '%cache%';
show variables like 'innodb_buffer_pool_size';
```

查看MySQL服务器运行状态值。

```SQL
show status;
show status like 'com_%';
show status like 'innodb_%';
show status like 'connections';
show status like 'slow_queries';
```

1. 调整`max_connections`：MySQL最大连接数量，默认151。在Linux系统上，如果内存足够且不考虑用户等待响应时间这些问题，MySQL理论上可以支持到万级连接，但是通常情况下，这个值建议控制在1000以内。
2. 调整`back_log`：TCP连接的积压请求队列大小，通常是max_connections的五分之一，最大不能超过900。
3. 调整`table_open_cache`：这个值应该设置为max_connections的N倍，其中N代表每个连接在查询时打开的表的最大个数。
4. 调整`innodb_lock_wait_timeout`：该参数可以控制InnoDB事务等待行锁的时间，默认值是50ms，对于反馈响应要求较高的应用，可以将这个值调小避免事务长时间挂起；对于后台任务，可以将这个值调大来避免发生大的回滚操作。
5. 调整`innodb_buffer_pool_size`：InnoDB数据和索引的内存缓冲区大小，以字节为单位，这个值设置得越高，访问表数据需要进行的磁盘I/O操作就越少，如果可能甚至可以将该值设置为物理内存大小的80%。

### 架构优化

1. 通过拆分提高表的访问效率。

    - 垂直拆分
    - 水平拆分

2. 逆范式理论。数据表设计的规范程度称之为范式（Normal Form），要提升表的规范程度通常需要将大表拆分为更小的表，范式级别越高数据冗余越小，而且在插入、删除、更新数据时出问题的可能性会大幅度降低，但是节省了空间就意味着查询数据时可能花费更多的时间，原来的单表查询可能会变成连表查询。为此，项目实践中我们通常会进行逆范式操作，故意降低范式级别增加冗余来减少查询的时间开销。

    - 1NF：列不能再拆分
    - 2NF：所有的属性都依赖于主键
    - 3NF：所有的属性都直接依赖于主键（消除传递依赖）
    - BCNF：消除非平凡多值依赖

3. 使用中间表提高统计查询速度。

    使用`insert into 中间表 select ... where ...`这样的语句先将需要的数据筛选出来放到中间表中，然后再对中间表进行统计，避免不必要的运算和处理。

4. 主从复制和读写分离

5. 配置MySQL集群

### MySQL存储引擎

**定义**

```
mysql数据库管理系统中用来处理表的处理器
不同引擎是不同操作代码，不同方案，各自擅长不同
```

**基本操作**

```mysql
1、查看所有存储引擎
   mysql> show engines;
2、查看已有表的存储引擎
   mysql> show create table 表名;
3、创建表指定
   create table 表名(...)engine=MyISAM,charset=utf8,auto_increment=10000;
4、已有表指定
   alter table 表名 engine=InnoDB;
```

**常用存储引擎特点** 

- InnoDB - 适合写		

```mysql
1、行级锁，仅对指定的记录加锁，其它进程还可以对同一个表中的其它记录进行操作。
2、支持外键、事务、事务回滚
3、表字段和索引同存储在一个文件中
   1、表名.frm ：表结构
   2、表名.ibd : 表记录及索引文件
```

- MyISAM - 适合查

```mysql
1、表级锁,在锁定期间，其它进程无法对该表进行写操作，包括读写操作。
2、表字段和索引分开存储
   1、表名.frm ：表结构
   2、表名.MYI : 索引文件(my index)
   3、表名.MYD : 表记录(my data)
```

**如何选择存储引擎**

```mysql
1、执行查操作多的表用 MyISAM(使用InnoDB浪费资源)
2、执行写操作多的表用 InnoDB
```

### 字段数据类型选择

- 优先程度   数字 >  时间日期 > 字符串
- 同一级别   占用空间小的 > 占用空间多的

```
字符串在 查询比较排序 时数据 处理慢
占用空间少，数据库占磁盘页少，IO处理就更快
```

- 少于50字节 char(浪费空间但快) > varchar
- 对数据存储精确不要求 float > decimel
- 如果很少被查询可以用 TIMESTAMP（时间戳实际是整形存储）

### 键的设置

- Innodb如果不设置主键也会自己设置隐含的主键，所以最好自己设置
- 尽量设置占用空间小的字段为主键（一般是id int）
- 外键的设置用于保持数据完整性，但是会降低数据导入和操作效率，特别是高并发情况下，而且会增加维护成本
- 虽然高并发下不建议使用外键约束，但是在表关联时建议在关联键上建立索引，以提高查找速度