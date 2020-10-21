# 基础

1. 数据库结构

>数据元素 --> 记录 -->数据表 --> 数据库

2.  特点

* 是开源数据库，数据库管理系统使用C和C++编写 
* 能够工作在众多不同的平台上Mac、Windows、Linux、Unix
* 提供了用于C、C++、Python、Java、Perl、PHP、Ruby众多语言的API
* 存储结构优良，运行速度快
* 功能全面丰富
* B-Tree结构构建索引表

3.  MySQL目录

>配置文件：/etc/mysql
>
>命令集： /usr/bin
>
>数据库存储目录 ：/var/lib/mysql(sudo su超级权限才能查看)

4.  启动和连接MySQL服务

>服务端启动
>
>>* 查看MySQL状态: sudo /etc/init.d/mysql status
>>* 启动服务：sudo /etc/init.d/mysql start | stop | restart  或者 service mysql   start|stop|restart

>客户端连接
>
>>命令格式 
>>
>>* mysql -h主机地址 -u用户名 -p密码
>>* 本地连接可省略 -h 选项: mysql -uroot -p123456  
>>    （可以mysql -u root -p，然后输入密码，不会成为明文密码)

* 关闭连接

>>ctrl-D/exit

>用图形界面操作数据库  
>pycharm右侧databases,按数据库填写名称等信息connect，选择表，增删改查，提交

# SQL语句

*   结构化查询语言(Structured Query Language)，一种特殊目的的编程语言，是一种数据库查询和程序设计语言，用于存取数据以及查询、更新和管理关系数据库系统。(几乎所有关系型数据库都是用SQL操作)
*   SQL语句使用特点
    *   SQL语言基本上独立于数据库本身
    *   各种不同的数据库对SQL语言的支持与标准存在着细微的不同
    *   每条命令必须以 ; 结尾
    *   SQL命令（除了数据库名和表名）可以不区分字母大小写
*   DDL：Data Definition Language

> DDL允许用户定义数据，也就是创建表、删除表、修改表结构这些操作。通常，DDL由数据库管理员执行。

- DML：Data Manipulation Language

> DML为用户提供添加、删除、更新数据的能力，这些是应用程序对数据库的日常操作。

- DQL：Data Query Language

> DQL允许用户查询数据，这也是通常最频繁的数据库日常操作

## 数据类型

### 数字类型：

>>* 整数类型（精确值）  
>>    TINY/255，SMALL/65535，MEDIUM/1000W，本身/42亿，BIG
>>
>>* 定点类型（精确值） - DECIMAL  
>>
>>* 浮点类型（近似值） - FLOAT4，DOUBLE 8 
>>
>>* 比特值类型 - BIT  (指0，1值表达2种情况，如真，假)  
>>
>>* 对于精度比较高的东西，比如money，用decimal类型提高精度减少误差。列的声明语法是DECIMAL(M,D)。
>>
>>    M是数字的最大位数（精度）。其范围为1～65，M 的默认值是10。  
>>    D是小数点右侧数字的数目（标度）。其范围是0～30，但不得超过M。  
>>    比如 DECIMAL(6,2)最多存6位数字，小数点后占2位,取值范围-9999.99到9999.99。（小数点后多了四舍五入

----------------------------------

### 字符串类型：

>>CHAR和VARCHAR类型  
>>(TINY/255，本身/65535，MEDIUM/1000W，LONG/4G)BLOB和TEXT类型  
>
>* char 和 varchar
>    char：定长，默认1字符  ,不论存多少，都占用申请的空间大小（可以短不能长）
>    varchar：不定长，效率偏低 ，但是节省空间。
>* text 和blob
>    text用来存储非二进制文本
>    blob用来存储二进制字节串(数据库可以存图片音频视频等)
>* enum 和 set
>    enum用来存储给出的一个值 (枚举选择，多选一)   
>    set用来存储给出的值中一个或多个值(给定若干选项，多选多)
>    (创建时‘a，b’)

### 时间类型数据

>   时间和日期类型:
>
>   >日期DATE/3，日期时间DATETIME/8，时间戳  TIMESTAMP /4   
>   >时间TIME /3 （计时时间）  
>   >年份YEAR/1  
>   >无法满足需求时用str存
>
>   时间格式
>
>   > date ："YYYY-MM-DD"  
>   > time ："HH:MM:SS"  
>   > datetime ："YYYY-MM-DD HH:MM:SS"  1000-9999  (字符串存储)  
>   > timestamp ："YYYY-MM-DD HH:MM:SS"  1970-2038（长数字存储）  
>   >
>   > 注意  
>   > 1、datetime ：以系统时间存储  
>   > 2、timestamp ：以标准时间存储但是查看时转换为系统时区，所以表现形式和datetime相同  
>   > 插入时可以不按格式写，以字符串传入，YYYY-M -D H：M：S 但自动转换以固定格式储存

## 库操作

1.查看已有库/查看创建库

>show databases;/show create database 库名;

2.创建库

>create database 库名 [character set utf8]/[charset=utf8];

3.查看当前所在库

>select database();

4.切换库

>use 库名;

5.删除库

>drop database 库名;

7.库名的命名规则

>* 数字、字母、下划线,但不能使用纯数字（英文全写，下划线隔开）
>* 库名区分字母大小写

## 表操作

1.创建表

>create table 表名 (字段名 数据类型 条件，);

>>* 无符号(没有负数)：unsigned
>>* 不为 NULL ：NOT NULL
>>* 设置默认值：DEFAULT 
>>* 自增的属性：AUTO_INCREMENT（在已有数据基础上+1）。
>>* 列为主键：PRIMARY KEY（字段结束加primary key/整体结尾加primary key（字段））
>>* 日期时间函数
>>    * now()  返回服务器当前日期时间,格式对应datetime类型
>>    * curdate() 返回当前日期，格式对应date类型
>>    * curtime() 返回当前时间，格式对应time类型  
>>        可以select 函数显示

2.查看数据表/查看已有表的字符集

> show tables；/show create table \`表名`;

3.查看表结构

>desc 表名;/G格式化输出

4.查看表内容

>select * from 表名

5.删除表

>drop table \`表名`;

## 表字段的操作(alter)

```SQL
语法 ：alter table 表名 执行动作;

* 添加字段(add)(默认最后一列)(会产生空字段)
    alter table 表名 add 字段名 数据类型;
* 删除字段(drop)
    alter table 表名 drop 字段名;
* 修改数据类型(modify)
    alter table 表名 modify 字段名 新数据类型;
* 修改字段名(change)
    alter table 表名 change 旧字段名 新字段名 新数据类型;
* 表重命名(rename)
    alter table 表名 rename 新表名;
```

## 数据操作

### 插入(INSERT INTO VALUES)

```SQL
insert into 表名 values(记录1),(记录2),...;
insert into 表名(字段1,...) values(记录1),...;
```

### 更新(UPDATA SET)

```SQL
update 表名 set 字段1=值1,字段2=值2,... where 条件;（判断是null用is不是=）
```

### 删除(DROP FROM)

```SQL
delete from 表名 where 条件;
```

### 查询(SELECT)

```SQL
SELECT 
[DISTINCT] <select_list> 去重
FROM <left_table>
<join_type> JOIN <right_table>联合
ON <join_condition>联合条件
WHERE <where_condition>查询条件
LIKE 'condition'模糊查询/REGEXP 'condition'正则查询
GROUP BY <group_by_list>聚合操作
HAVING <having_condition>聚合筛选
ORDER BY <order_by_condition> [ASC [DESC]]排序
LIMIT <limit_number>限制数量

WHERE和聚合不能同时使用
```

#### 时间操作（需要用字符串格式输入比较）

```sql
select * from marathon where birthday>='2000-01-01';
select * from marathon where birthday>="2000-07-01" and performance<="2:30:00";
```

* 日期时间运算（只能 + - ）

    - 语法格式（比较时比较的时间必须是标准格式的字符差un）

        select * from 表名  where 字段名 运算符 (时间-interval 时间间隔单位);

    - 时间间隔单位：   hour | minute |  second | year |  month |  day

```sql
select * from marathon where registration_time>(now()-interval 7 day);
```

#### 筛选语句（where）

* 通过一定的运算条件进行数据的筛选

    算术运算符>比较运算符>逻辑运算符>位运算符

#### 设置别名（as）

* 在sql语句中as用于给字段或者表设置别名

    > ```sql
    > select name as 姓名,age as 年龄 from class_1;
    > select * from class_1 as c where c.age > 17;
    > ```

#### 模糊查询和正则查询（LIKE/REGEXP）

*   百分号 %来表示任意0/n个字符，下划线_表示任意一个字符。

*   只支持部分正则元字符（不区分大小写）

#### 排序ORDER BY

*   将查询数据排序后再返回数据：

```sql
SELECT field1, field2,...fieldN from table_name1 where field1
ORDER BY field1 [ASC [DESC]]
```

默认情况ASC表示升序，DESC表示降序

#### 分页LIMIT

*   LIMIT ：限制由 SELECT 语句返回的数据数量 或者 UPDATE,DELETE语句的操作数量

#### 联合查询UNION （比or快）

*   操作符用于连接两个以上的 SELECT 语句的结果组合到一个结果集合中
*   多个 SELECT 语句会删除重复的数据。

```sql
SELECT expression1, expression2, ... expression_n
FROM tables
[WHERE conditions]
UNION [ALL | DISTINCT]
SELECT expression1, expression2, ... expression_n
FROM tables
[WHERE conditions];

DISTINCT: 可选，删除结果集中重复的数据。默认情况下 UNION 操作符已经删除了重复数据，所以 DISTINCT 修饰符对结果没啥影响。
ALL: 可选，返回所有结果集，包含重复数据。
```

```sql
select * from class_1 where sex='m' UNION ALL select * from class_1 where age > 9;
```

#### 子查询(可以跨表)

* 定义 ： 当一个select语句中包含另一个select 查询语句，则称之为有子查询的语句
    子查询出现的位置：

    * from 之后 ，此时子查询的内容作为一个新的表内容（必须as别名），再进行外层select查询

        ```SQL
        select name from (select * from class_1 where sex='m') as s where s.score > 90;
        需要将子查询结果集重命名一下，方便where子句中的引用操作
        ```

    * where字句中，此时select查询到的内容作为外层查询的条件值

        ```SQL
        select *  from class_1 where age = (select age from class_1 where name='Tom');
        子句返回的结果需要一个明确值，即需要时某个记录的某个元素，不能是多行或者多列
        ```

#### 函数

```sql
# 提取字符串部分
P: SUBSTR()
M: SUBSTRING()
# 数据类型转换
P: CAST()
M: CONVERT()
# 当前日期
P: CURRENT_DATE()
M: CURDATE()
```

### 聚合操作

select FUN(字段) as 别名 from 表

#### 聚合函数（fun）

| 方法          | 功能                 |
| ------------- | -------------------- |
| avg(字段名)   | 该字段的平均值       |
| max(字段名)   | 该字段的最大值       |
| min(字段名)   | 该字段的最小值       |
| sum(字段名)   | 该字段所有记录的和   |
| count(字段名) | 统计该字段记录的个数 |

> 注意： 此时select 后只能写聚合函数或分组的字段，无法查找其他字段，聚合函数可以，并列。

#### 分组聚合（group by）

给查询的结果进行分组


所有国家的男英雄中 英雄数量最多的前2名的 国家名称及英雄数量

```mysql
select country,count(id) as number from sanguo 
where gender='M' group by country
order by number DESC
limit 2;
```

>  使用分组时select 后的字段为group by分组的字段和聚合函数，不能包含其他内容。group by也可以同时依照多个字段分组，如group by A，B 此时必须A,B两个字段值均相同才算一组。

#### 聚合筛选（having）

对分组聚合后的结果进行进一步筛选

```mysql
eg1 : 找出平均攻击力大于105的国家的前2名,显示国家名称和平均攻击力
select country,avg(attack) from sanguo 
group by country
having avg(attack)>105
order by avg(attack) DESC
limit 2;
```

注意

>- having语句通常与group by联合使用。
>- having语句存在弥补了where关键字不能与聚合函数联合使用的不足,where只能操作表中实际存在的字段,having操作的是聚合函数生成的显示列

#### 去重语句（distinct）

不显示字段重复值

```mysql
eg1 : 表中都有哪些国家
  select distinct name,country from sanguo;
eg2 : 计算一共有多少个国家
  select count(distinct country) from sanguo;
```


注意

>- distinct和from之间所有字段都相同才会去重

#### 聚合运算

查询表记录时做数学运算

都是数字的话可以各个字段之间+-*/

```mysql
eg1: 查询时显示攻击力翻倍
  select name,attack*2 from sanguo;
eg2: 更新蜀国所有英雄攻击力 * 2
  update sanguo set attack=attack*2 where country='蜀国';
```

## 索引操作

- **优点**

    - 加快数据检索速度,提高查找效率

- **缺点**

    - 占用数据库物理存储空间  
    - 当对表中数据更新时,索引需要动态维护,降低数据写入效率


###  常用索引分类

*  普通索引(MUL) 

> 普通索引 ：字段值无约束,KEY标志为 MUL

* 唯一索引(UNI)

> 唯一索引(unique) ：字段值不允许重复,但可为 NULL,KEY标志为 UNI

* 主键索引（PRI）

> 一个表中只能有一个主键字段, 主键字段不允许重复,且不能为NULL，KEY标志为PRI。通常设置记录编号字段id,能唯一锁定一条记录

*   外键索引

### 创建索引


* 创建表时直接创建索引

```mysql
create table 表名(
字段名 数据类型，
字段名 数据类型，
index(字段名),
unique(字段名)
);

unique
唯一，可以为null，多个字段
primery key
唯一，不可以为null，一个字段
```

* 在已有表中创建索引：

```mysql
create [unique] index 索引名 on 表名(字段名);
```

### 查看索引

```mysql
1、desc 表名;  --> KEY标志为：MUL 、UNI。
2、show index from 表名;
```

> * 扩展： 借助性能查看选项去查看索引性能（查看打开profiling后每一条命令的执行时间）
>
>     ```sql
>     show variables like 'profiling'；
>     set  profiling = 1； 打开功能 （项目上线一般不打开）
>     show profiles  查看语句执行信息
>     ```

### 删除索引

```mysql
drop index 索引名 on 表名;
```

### 已有表添加主键索引或自增长属性

```mysql
alter table 表名 add primary key(id);
alter table 表名 modify id int auto_increment;
```

### 创建复合主键

```mysql
primary key（uid,pid）
此时两个字段只要不同时相同即可，允许其中一个相同。
```

### 删除主键索引或自增长属性

```mysql
# 要先删除自增长，因为它只作用于主键字段
alter table 表名 modify id int;  
alter table 表名 drop primary key;
```

## 外键约束和表关联关系

### 外键约束（foreign key）

* 约束 : 约束是一种限制，它通过对表的行或列的数据做出限制，来确保表的数据的完整性、唯一性

* 建立表与表之间的某种约束的关系，由于这种关系的存在，能够让表与表之间的数据，更加的完整，关连性更强，为了具体说明创建如下部门表和人员表（会影响表之间关联查询效率）。

* 主表和从表：若同一个数据库中，B表的外键与A表的主键相对应，则A表为主表，B表为从表。

- foreign key 外键的定义语法：

    ```sql
    # 在从表中
    [CONSTRAINT 外键名称] FOREIGN KEY [从表关联字段] REFERENCES 主表（字段）
    [ON DELETE {RESTRICT | CASCADE | SET NULL | NO ACTION}]
    [ON UPDATE {RESTRICT | CASCADE | SET NULL | NO ACTION}]
    如果不指定别名，mysql会自动生成一个名字,可以通过show create table [tb]查看
    
    操作
    CREATE TABLE 表名 (字段设置,constraint dept_fk foreign key(dept_id) references dept(id));
    ALTER TABLE 表名 add constraint dept_fk foreign key(dept_id) references dept(id);
    alter table person drop foreign key dept_fk;
    
    级连动作  
    * restrict(默认)
      on delete restrict  on update restrict
      从表有相关记录，不允许主表删除记录/更改字段
    * cascade ：数据级联更新
      on delete cascade   on update cascade
      当主表删除记录，从表中关联主表对相应字段的记录也会删除
    *set null
      on delete set null    on update set null
      当主表删除记录/更改主键字段值时，从表外键字段值变为null
    * no action
      同 restrict，都是立即检查外键限制
    ```

### 表关联(不能有重复的外键名)

#### 表关联创建

- 一对一关系 
    主 primary key  
    从 unique，foreign key


 ``` sql
st_id int unique,
foreign key(st_id) references student(id) 
on delete cascade 
on update cascade
 ```


- 一对多关系  
    主 primary key  
    从 foreign key

- 多对多关系

    主 primary key  
    从 primary key  
    关系表 ptimary key（联合主键）分别外键关联

```sql
  PRIMARY KEY (`aid`,`tid`),
  CONSTRAINT `athlete_fk` FOREIGN KEY (`aid`) REFERENCES `athlete` (`aid`)，
  CONSTRAINT `item_fk` FOREIGN KEY (`tid`) REFERENCES `item` (`tid`)
);
```

#### 表关联查询

表的关联查询与外键约束之间***并没有必然联系***，但是基于外键约束设计的具有关联性的表往往会更多使用关联查询查找数据。

##### 多表查询

```sql
select  字段1,字段2...（一般用表名.字段查询） from 表1,表2... [where 条件]
没有where时会有笛卡尔积现象

笛卡尔积
笛卡尔积就是将A表的每一条记录与B表的每一条记录强行拼在一起。所以，如果A表有n条记录，B表有m条记录，笛卡尔积产生的结果就会产生n*m条记录。
```

##### 内连接

```sql
SELECT 字段列表 FROM 表1 INNER JOIN  表2 ON 表1.字段 = 表2.字段;
```

##### 外链接

- 左连接  : 左表为主表，显示左表所有内容，先显示右表中与左表匹配的项，不匹配的填充Null

```sql
SELECT 字段列表 FROM 表1  LEFT JOIN  表2 ON 表1.字段 = 表2.字段;
```

- 右连接 ：右表为主表，显示右表所有内容，先显示左表中与右表匹配的项，不匹配的填充Null

```sql
SELECT 字段列表 FROM 表1  RIGHT JOIN  表2 ON 表1.字段 = 表2.字段;
```

## 视图（封装多步操作以复用）

### 视图概念

> 视图是存储的查询语句
>
> 展示表数据的所有或一部分
>
> 视图中，用户可以对记录进行增删改查
>
> 不能有索引，触发器，默认值
>
> 可以根据视图创建视图

### 视图操作

* 创建视图

    ```sql
    CREATE [OR REPLACE] VIEW [view_name] AS [SELECT ……];
    
    OR REPLACE : 可选，如果添加原来有同名视图的情况下会覆盖掉原有视图
    SELECT_STATEMENT ：SELECT语句
    原表名称修改会断开连接，类似硬连接，名称改回原名继续链接
    ```

* 删除视图

    ```sql
    drop view [IF EXISTS] view_name;
    
    ```

IF EXISTS 表示如果存在，这样即使没有指定视图也不会报错。

  ```
  
* 修改视图

  ```sql
alter view  c1 as select name,age,score from class_1;
  ```

* 视图表的增删改查操作

    视图的增删改查操作与一般表的操作相同，使用insert update delete select即可，但是原数据表的约束条件仍然对视图产生作用。

### 视图作用

* 作用
    1. 是对数据的一种重构，不影响原数据表的使用。
    2. 简化高频复杂操作的过程，就像一种对复杂操作的封装。
    3. 提高安全性，可以给不同用户提供不同的视图
    4. 让数据更加清晰
* 缺点
    1. 视图的性能相对较差，从数据库视图查询数据可能会很慢
    2. 表依赖关系处理麻烦，根据数据库的基础表创建一个视图。每当更改视图或者原表时，另一个也会修改。


## 函数和存储过程

存储过程和函数是事先经过编译并存储在数据库中的一段sql语句集合

### 函数创建和调用

```sql
创建
myslq -> delimiter 自定义符号　　
myslq -> create function 函数名(名字 类型,2,3……) returns 返回类型
myslq -> begin
myslq -> 函数体
myslq -> return val;(中间函数体都需要;)
myslq -> end  自定义符号
myslq -> delimiter ;
调用
myslq -> select 函数名(数据)

注意：
如果函数体只有一条语句, begin和end可以省略, 同时delimiter也可以省略
```

```sql
e.g. 无参数的函数调用
delimiter $$
create function st() returns int 
begin 
return (select score from class_1 order by score desc limit 1); 
end $$
delimiter ;

select st();
```

```sql
e.g. 含有参数的函数调用
delimiter $$
create function queryNameById(uid int(10)) 
returns varchar(20)
begin
return  (select name from class_1 where id=uid);
end $$
delimiter ;

select queryNameById(1);
```

### 存储过程创建和调用

创建存储过程语法与创建函数基本相同，但是没有返回值。

```sql
创建
myslq -> delimiter 自定义符号　　
myslq -> create procedure 存储过程名(形参列表)
myslq -> begin
myslq -> 储存过程
myslq -> end  自定义符号
myslq -> delimiter ;
调用
myslq -> call 存储过程名(数据)

形参列表 ：[ IN | OUT | INOUT ] 形参名 类型
in 输入
接受常量/变量--内部能看--内部修改外面不变
传入的参数在存储过程内部使用即可，但是在存储过程内部的修改无法传递到外部（类似局部变量）

out 输出
接受变量--内部不能看--内部修改外部变
接收的变量不能够在存储过程内部使用（内部为NULL），但是可以在存储过程内对这个变量进行修改。因为定义的变量是全局的，所以外部可以获取这个修改后的值。（相当与python可变类型传参）

inout 输入和输出
接受变量--内能看--内部修改外部变 
这个变量可以在存储过程内部使用。在存储过程内部的修改也会传递到外部。
```

### 存储过程和存储函数操作

1. 调用存储过程

```
call 存储过程名字（[存储过程的参数[,……]])
```

2. 调用存储函数

```
select 存储函数名字（[存储过程的参数[,……]])
```

3. 使用show status语句查看存储过程和函数的信息

```
show {procedure|function} status [like’存储过程或存储函数的名称’]
显示内容：数据库、名字、类型、创建者、创建和修改日期
```

4.  使用show create语句查看存储过程和函数的定义：

```
show create  {procedure|function}  存储过程或存储函数的名称
```

5. 删除存储过程或存储函数

```
drop {PROCEDURE|FUNCTION} [IF EXISTS] name
```

6.  变量

```sql
创建set @name = 
使用@name

输入两个id，返回分数差值
mysql> delimiter $$
mysql> create function fc1(uid1 int,uid2 int) returns int
    -> begin
    -> set @val1 = (select score from cls1 where id=uid1);
    -> set @val2 = (select score from cls1 where id=uid2);
    -> return (@val1-@val2);
    -> end $$
mysql> delimiter ;

mysql> select fc1(2,3);

输入id返回成绩
mysql> delimiter $$
mysql> create procedure pc(in uid int,out num int)
    -> begin
    -> declare val int;
    -> select age into val from cls1 where id = uid;
    -> set num = val;
    -> end $$
mysql> delimiter ;

mysql> set @age=0;
mysql> call pc(3,@age);
mysql> select @age;
```

### 函数和存储过程区别

1. 储存过程无returns和return，函数有
2. 定义function -> procedure，调用select -> call
3. 函数一般用于查，储存过程用于增删改
4. 函数有且只有一个返回值，而存储过程不能有返回值，用变量传递。
5. 函数只能有输入参数in，而存储过程可以有in,out,inout多个类型参数。
6. 存储过程一般是作为一个独立的部分来执行(call调用)。而函数可以作为查询语句的一个部分来调用.

## 事务（保持原子性）

### 事务概述。

* 定义

 > 一件事从开始发生到结束的过程


* 事务四大特性：原子性、一致性、隔离性、持久性
    * 原子性：一个事务中的所有操作要么全部完成，要么全部不完成，不会结束在中间某个环节。事务在执行过程中发生错误，会被恢复到事务开始前的状态
    * 一致性：在事务开始之前和事务结束以后，数据库的完整性没有被破坏。
    * 隔离性：数据库允许多个并发事务同时对其数据进行读写和修改的能力。
    * 持久性：事务处理结束后，对数据的修改就是永久的，即便系统故障也不会丢失。
* 事务隔离级别
    * 读未提交（Read uncommitted）
    * 读已提交（read committed）不会脏读
    * 可重复读（repeatable read）不会不可重复读(默认)
    * 串行化（Serializable）不会幻读
    * 高级别影响性能，低级别影响结果
* 事务隔离弊端：
    * 脏读：一个事务读取了另一个事务回滚未提交的数据。
    * 不可重复读：一个事务多次查询同一数据却返回了不同的数据值
    * 幻读：在一个事务读的过程中，另外一个事务可能插入了新数据记录，影响了该事务读的结果
    * 不可重复读侧重于修改，幻读侧重于新增或删除。
    * 不可重复读锁行，幻读锁表


### 事务操作


1. 开启事务

```mysql
   mysql>begin; # 方法1
   mysql>start transaction; # 方法2
```

2. 开始执行事务中的1条或者n条SQL命令（一定是写操作，因为读操作可以同步不影响）  （修改在当前事务实时显示，提交之前在别的终端看不到）
3. 终止事务

```mysql
   mysql>commit; # 事务中SQL命令都执行成功,提交到数据库,结束!
   mysql>rollback; # 有SQL命令执行失败,回滚到初始状态,和begin之间全部作废不修改
```

### 事务四大特性

1. 原子性（atomicity）


>一个事务必须视为一个不可分割的最小工作单元，整个事务中的所有操作要么全部提交成功，要么全部失败回滚，不可能只执行其中的一部分操作


2. 一致性（consistency）

> 事务完成时，数据必须处于一致状态，数据的完整性约束没有被破坏，事务在执行过程中发生错误，则回到事务开始前的状态，就像这个事务从来没有执行过一样。

3. 隔离性（isolation）

> 一个事务所做的修改在最终提交以前，对其他事务是不可见的。多个事务相互独立。 
> 当前事务对数据进行修改时，其他事务操作共同数据的会阻塞，直到当前事物提交该数据  
> 当前事务对数据进行修改时，其他事务无法查询该数据的变化，其他事务操作该记录中其他数据时，该数据也会改变

4. 持久性（durability）

> 一旦事务提交，则其所做的修改就会永久保存到数据库中。此时即使系统崩溃，修改的数据也不会丢失。

* 注意: 

    * 事务只针对于表记录操作(增删改)有效,对于库和表的操作无效。  

    * 读操作会因为隔离特性产生‘幻读’，‘脏读’等现象

    * create\delete\insert等语句隐式commit，不能rollback

        想要rollback需要借助savepoint name/rollback to name

        或者begin后声明SET autocommit=0

## 数据库优化

### 数据库设计范式

设计关系数据库时，遵从的规范要求

> 目前关系数据库有六种范式：第一范式（1NF）、第二范式（2NF）、第三范式（3NF）、巴斯-科德范式（BCNF）、第四范式(4NF）和第五范式（5NF，又称完美范式）。（一般不会都遵循）

各种范式呈递次规范（遵循n，必定会遵循n-1），越高的范式数据库冗余越小。但是范式越高也意味着表的划分更细，一个数据库中需要的表也就越多，此时多个表联接在一起的花费是巨大的，尤其是当需要连接的两张或者多张表数据非常庞大的时候，表连接操作几乎是一个噩梦，这严重地降低了系统运行性能。所以通常数据库设计遵循第一第二第三范式，以避免数据操作异常,又不至于表关系过于复杂。


范式简介：

- 第一范式： 数据库表的每一列都是不可分割的原子数据项，而不能是集合，数组，记录等组合的数据项。简单来说要求数据库中的表示二维表，每个数据元素不可再分。
- 第二范式： 第二范式（2NF）要求数据库表中的每个实例或记录必须可以被唯一地区分，所有属性依赖于主属性。即选取一个能区分每个实体的属性或属性组，作为实体的唯一标识，每个属性都能被主属性筛选。（例如主键）
- 第三范式： 在第二范式的基础上属性不传递依赖，即每个属性不依赖于其它非主属性。要求一个关系中不包含已在其它关系的非主关键字信息。（多个表不出现相同字段/外键关联/依赖）

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

### explain语句

使用 EXPLAIN 关键字可以模拟优化器执行SQL查询语句，从而知道MySQL是如何处理你的SQL语句的。这可以帮你分析你的查询语句或是表结构的性能瓶颈。

```sql
explain select * from class_1 where id <5;
```

EXPLAIN主要字段解析：

* table：显示这一行的数据是关于哪张表的
* type：

```
- system、const： 可以将查询的变量转为常量. 如id=1; id为 主键或唯一键.
- eq_ref： 访问索引,返回某单一行的数据.(通常在联接时出现，查询使用的索引为主键或唯一键)
- ref： 访问索引,返回某个值的数据.(可以返回多行) 通常使用=时发生 
- range： 这个连接类型使用索引返回一个范围中的行，比如使用>或<查找东西，并且该字段上建有索引时发生的情况
- index： 以索引的顺序进行全表扫描，优点是不用排序,缺点是还要全表扫描 
- ALL： 全表扫描，应该尽量避免
```

* possible_keys：显示可能应用在这张表中的索引。

* key：实际使用的索引。如果为NULL，则没有使用索引。
* key_len：使用的索引的长度。在不损失精确性的情况下，长度越短越好
* rows：MySQL认为必须检索的用来返回请求数据的行数


### SQL语句优化

-  尽量选择数据类型占空间少，在where ，group by，order by中出现的频率高的字段建立索引
-  explain 放在查询语句前面可以获取查询计划，建立合适索引
-  控制使用自定义函数
-  子查询优化为join查询
-  尽量避免全盘扫描
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

### 表的拆分

垂直拆分 ： 表中列太多，分为多个表，每个表是其中的几个列。将常查询的放到一起，blob或者text类型字段放到另一个表

水平拆分 ： 减少每个表的数据量，通过hash key进行划分然后拆成多个表

## 数据库备份

###  数据备份

1. 备份命令格式

>mysql dump -u用户名 -p 源库名 > ~/name.sql
>
>> --all-databases  备份所有库
>> db_name            备份单个库
>> -B 库1 库2 库3   备份多个库
>> 库名 表1 表2 表3 备份指定库的多张表

2. 恢复命令格式

>mysql -uroot -p 目标库名 < name.sql

### **表的复制**

1、表能根据实际需求复制数据  
2、复制表时不会把KEY属性复制过来

**语法**

```mysql
create table 表名 select 查询命令;
```

## 用户权限管理

**开启MySQL远程连接**

```mysql
更改配置文件，重启服务！
1.sudo -i
2.cd /etc/mysql/mysql.conf.d
3.cp mysqld.cnf mysqld.cnf.bak
4.vi mysqld.cnf #找到44行左右,加 # 注释
   #bind-address = 127.0.0.1
   可以加默认值character_set_server = utf8
5.保存退出
6.service mysql restart
7.修改用户表host值 
  use mysql;
  update user set host='%' where user='root';
8.刷新权限
  flush privileges;
```

**添加授权用户**

```mysql
1. 用root用户登录mysql
   mysql -uroot -p123456
2. 添加用户 % 表示自动选择可用IP
   create user '访问名称'@'host' identified by '访问密码';（host处一般写%，表示任意IP访问）
3. 授权
   grant 权限列表 on 库.表 to "用户名"@"%" identified by "密码" with grant option;(可以用*.*表示所有表)
4. 刷新权限
   flush privileges;
```

**权限列表**

```
all privileges 、select 、insert ... ... 
库.表 ： *.* 代表所有库的所有表
```

**示例**

```mysql
1、添加授权用户work,密码123,对所有库的所有表有所有权限
  mysql>grant all privileges on *.* to 'work'@'%' identified by '123' with grant option;
```

## log

* Mysql的基本存储结构是**页**(记录都存在页里边)，所以MySQL是先把这条记录所在的**页**找到，然后把该页加载到内存中，将对应记录进行修改

* MySQL里边还有几种常见的`log`
    * undo log - 记录事务操作相反的语句(插入数据就记录删除数据)，保证MySQL的原子性，回滚的基础
    * binlog - 记录所有增删改语句，主从复制，删库所有恢复。MySQL用什么引擎，都会有的，事务执行后
    * redo log - 记录数据库物理变化，磁盘页数据变化，原数据在时恢复。MySQL的InnoDB引擎所产生的，事务开始时

## 索引

* 查询优化

    用于提高 WHERE 条件的数据行匹配时的搜索速度

* 单列索引会自动选取最严格的，需要多个列建立索引选取复合索引，而不是多个单列索引

* 使用like 'a%' 会使用索引、like'%a'、like'%a%'不会

* 索引数据结构

    Hash索引和B+ Tree索引，InnoDB默认B+树。

* B+ 和Hash索引比较优缺点

    哈希索引适合等值查询，但是不无法进行范围查询 

    哈希索引没办法利用索引完成排序 

    哈希索引不支持多列联合索引的最左匹配规则 如果有大量重复键值得情况下，哈希索引的效率会很低，因为存在哈希碰撞问题

* B+ Tree的叶子节点都可以存哪些东西？

    储存整行数据，主键索引，也被称之为聚簇索引

    储存主键的值，非主键索引，也被称之为非聚簇索引

    聚簇索引查询更快，叶子节点直接就是我们要查询的整行数据

    而非主键索引的叶子节点是主键的值，查到主键的值以后，还需要再通过主键的值再进行一次查询(回表)，但并不是必须回表，比如数据就是索引

* 创建索引的时候考虑因素？

    查询概率比较高，经常作为where条件的字段设置索引

* 联合索引

    多个字段之间把识别度最高的字段放到最前面。

    MySQL索引查询会遵循最左前缀匹配的原则。所以当我们创建一个联合索引的时候，如(key1,key2,key3)，相当于创建了（key1）、(key1,key2)和(key1,key2,key3)三个索引。

* MySQL 5.6中，对索引优化：Index Condition Pushdown Optimization）

> 索引下推，默认开启，使用SET optimizer_switch = 'index_condition_pushdown=off';可以将其关闭。
>
> 例如： people表中（a，b，c）构成一个索引，SELECT * FROM people WHERE a='1' AND b LIKE '%2%' AND c LIKE '%3%';
>
> 之前：通过a='1'查询对应的数据，返回到MySQL服务端，然后基于b LIKE '%2%' AND c LIKE '%3%'来判断数据是否符合条件。
>
> 使用了索引下推：返回符合a='1'的索引，然后根据b LIKE '%2%' AND c LIKE '%3%'来判断索引是否符合条件。如果符合条件，则根据该索引来定位对应的数据，如果不符合，则直接reject掉。可以在有like条件查询的情况下，减少回表次数。

* 创建了索引，但是执行的时候并没有通过索引

    查询优化器：一条SQL语句的查询，可以有不同的执行方案，至于最终选择哪种方案，需要通过优化器进行选择，选择成本最低的方案。 

    1、根据条件找出所有可能使用的索引，计算执行查询的代价 

    2、计算全表扫描的代价 

    3、对比各种执行方案的代价，找出成本最低的那一个

* where后面条件不使用索引列时，会全表扫描，迁移时会所有行锁

## 引擎

* MyISAM（ 非聚集）
    * 适合查，表锁
    * B+Tree，叶节点data域存放数据记录地址。
    * 内部维护了计数器，count更快

* InnoDB（ 聚集索引）
    * 适合写，行锁(基于索引)
    * B+Tree，叶节点data域存放数据记录。
    * 检索辅助索引获得主键，然后用主键到主索引中检索获得记录。
    * InnoDB的数据文件本身要按主键聚集，所以InnoDB要求表必须有主键（MyISAM可以没有）
    * 支持事务外键

* innodb为什么要用自增id作为主键：
    * 使用自增主键，插入新的记录，会顺序添加到当前索引节点的后续位置，当一页写满，就会自动开辟一个新的页
    * 如果使用非自增主键（如身份证号），每次新纪录都要被插到现有索引页得中间某个位置， 频繁的移动、分页操作造成了大量的碎片，得到了不够紧凑的索引结构，后续需要优化填充页面。

# 与python交互

## Python

### 模块

* pymysql
* psycopg2


### 使用流程

1. 建立数据库连接(db = 库.connect(...))

    >   host ：主机地址,本地 localhost（自己计算机可以不写）
    >   port ：端口号（自己计算机可以不写，,默认3306）
    >   user ：用户名
    >   password ：密码
    >   database ：库
    >   charset ：编码方式,推荐使用 utf8

2. 创建游标对象(cur = db.cursor())

3. 游标方法: cur.execute("insert ....")

    >   cur.execute(sql命令,[传参列表]) 执行SQL命令（不能传递语句中的表名，字段名，关键字等内容）
    >   cur.executemany(sql命令,[data]) 根据数据列表项多次执行SQL命令，一般用于写操作。
    >   cur.fetchone() 获取查询结果集的第一条数据，查找到返回一个元组否则返回None
    >   cur.fetchmany(n) 获取前n条查找到的记录，返回结果为元组嵌套元组， ((记录1),(记录2))。
    >   cur.fetchall() 获取所有查找到的记录，返回结果形式同上。

4. 提交到数据库或者获取数据 : db.commit()/db.rollback()

    >   写操作时配合try-commit-except-rollback

5. 关闭游标对象 ：cur.close()

6. 断开数据库连接 ：db.close()

### cursor方法

```python
execute()     执行一个数据库的查询命令
fetchone()      取得结果集的下一行
fetchmany(size) 获取结果集的下几行
fetchall()      获取结果集中的所有行
rowcount()      返回数据条数或影响行数
close()         关闭游标对象
```

## Django操作

*   对象操作：ORM映射，用类代替SQL语句

*   终端操作：django shell操作
*   原生操作：MyModel.objects.raw(sql语句)
*   具体操作见django文档

# Tips

| 特性         | InnoDB       | MRG_MYISAM | MEMORY | MyISAM |
| ------------ | ------------ | ---------- | ------ | ------ |
| 存储限制     | 有           | 没有       | 有     | 有     |
| 事务         | 支持         |            |        |        |
| 锁机制       | 行锁         | 表锁       | 表锁   | 表锁   |
| B树索引      | 支持         | 支持       | 支持   | 支持   |
| 哈希索引     |              |            | 支持   |        |
| 全文检索     | 支持（5.6+） |            |        | 支持   |
| 集群索引     | 支持         |            |        |        |
| 数据缓存     | 支持         |            | 支持   |        |
| 索引缓存     | 支持         | 支持       | 支持   | 支持   |
| 数据可压缩   |              |            |        | 支持   |
| 内存使用     | 高           | 低         | 中     | 低     |
| 存储空间使用 | 高           | 低         |        | 低     |
| 批量插入性能 | 低           | 高         | 高     | 高     |
| 是否支持外键 | 支持         |            |        |        |

*   ```sql
    -- 查询所有学生信息
    select * from tb_student;
    
    -- 查询所有课程名称及学分(投影和别名)
    select couname, coucredit from tb_course;
    select couname as 课程名称, coucredit as 学分 from tb_course;
    
    -- 查询所有学生的姓名和性别(条件运算)
    select stuname as 姓名, case stusex when 1 then '男' else '女' end as 性别 from tb_student;
    select stuname as 姓名, if(stusex, '男', '女') as 性别 from tb_student;
    
    -- 查询所有女学生的姓名和出生日期(筛选)
    select stuname, stubirth from tb_student where stusex=0;
    
    -- 查询所有80后学生的姓名、性别和出生日期(筛选)
    select stuname, stusex, stubirth from tb_student where stubirth>='1980-1-1' and stubirth<='1989-12-31';
    select stuname, stusex, stubirth from tb_student where stubirth between '1980-1-1' and '1989-12-31';
    
    -- 查询姓"杨"的学生姓名和性别(模糊)
    select stuname, stusex from tb_student where stuname like '杨%';
    
    -- 查询姓"杨"名字两个字的学生姓名和性别(模糊)
    select stuname, stusex from tb_student where stuname like '杨_';
    
    -- 查询姓"杨"名字三个字的学生姓名和性别(模糊)
    select stuname, stusex from tb_student where stuname like '杨__';
    
    -- 查询名字中有"不"字或"嫣"字的学生的姓名(模糊)
    select stuname, stusex from tb_student where stuname like '%不%' or stuname like '%嫣%';
    
    -- 查询没有录入家庭住址的学生姓名(空值)
    select stuname from tb_student where stuaddr is null;
    
    -- 查询录入了家庭住址的学生姓名(空值)
    select stuname from tb_student where stuaddr is not null;
    
    -- 查询学生选课的所有日期(去重)
    select distinct seldate from tb_record;
    
    -- 查询学生的家庭住址(去重)
    select distinct stuaddr from tb_student where stuaddr is not null;
    
    -- 查询男学生的姓名和生日按年龄从大到小排列(排序)
    select stuname as 姓名, datediff(curdate(), stubirth) div 365 as 年龄 from tb_student where stusex=1 order by 年龄 desc;
    
    -- 查询年龄最大的学生的出生日期(聚合函数)
    select min(stubirth) from tb_student;
    
    -- 查询年龄最小的学生的出生日期(聚合函数)
    select max(stubirth) from tb_student;
    
    -- 查询男女学生的人数(分组和聚合函数)
    select stusex, count(*) from tb_student group by stusex;
    
    -- 查询课程编号为1111的课程的平均成绩(筛选和聚合函数)
    select avg(score) from tb_record where cid=1111;
    
    -- 查询学号为1001的学生所有课程的平均分(筛选和聚合函数)
    select avg(score) from tb_record where sid=1001;
    
    -- 查询每个学生的学号和平均成绩(分组和聚合函数)
    select sid as 学号, avg(score) as 平均分 from tb_record group by sid;
    
    -- 查询平均成绩大于等于90分的学生的学号和平均成绩
    -- 分组以前的筛选使用where子句 / 分组以后的筛选使用having子句
    select sid as 学号, avg(score) as 平均分 from tb_record group by sid having 平均分>=90;
    
    -- 查询年龄最大的学生的姓名(子查询/嵌套的查询)
    select stuname from tb_student where stubirth=( select min(stubirth) from tb_student );
    
    -- 查询年龄最大的学生姓名和年龄(子查询+运算)
    select stuname as 姓名, datediff(curdate(), stubirth) div 365 as 年龄 from tb_student where stubirth=( select min(stubirth) from tb_student );
    
    -- 查询选了两门以上的课程的学生姓名(子查询/分组条件/集合运算)
    select stuname from tb_student where stuid in ( select stuid from tb_record group by stuid having count(stuid)>2 );
    
    -- 查询学生姓名、课程名称以及成绩(连接查询)
    select stuname, couname, score from tb_student t1, tb_course t2, tb_record t3 where stuid=sid and couid=cid and score is not null;
    
    -- 查询学生姓名、课程名称以及成绩按成绩从高到低查询第11-15条记录(内连接+分页)
    select stuname, couname, score from tb_student inner join tb_record on stuid=sid inner join tb_course on couid=cid where score is not null order by score desc limit 5 offset 10;
    
    select stuname, couname, score from tb_student inner join tb_record on stuid=sid inner join tb_course on couid=cid where score is not null order by score desc limit 10, 5;
    
    -- 查询选课学生的姓名和平均成绩(子查询和连接查询)
    select stuname, avgmark from tb_student, ( select sid, avg(score) as avgmark from tb_record group by sid ) temp where stuid=sid;
    
    select stuname, avgmark from tb_student inner join ( select sid, avg(score) as avgmark from tb_record group by sid ) temp on stuid=sid;
    
    -- 查询每个学生的姓名和选课数量(左外连接和子查询)
    select stuname, ifnull(total, 0) from tb_student left outer join ( select sid, count(sid) as total from tb_record group by sid ) temp on stuid=sid;
    ```

*   查看成功操作数据库的行为

    mysql中select * from django_migrations\G;

*   乐观锁：假设不会发生冲突，提交操作时才检查数据完整性

    1.在数据提交的时候才去锁定，所以不会产生任何的锁和死锁，

    2.读取出数据时，将此版本号一同读出，之后更新时，对此版本号加一。此时，将提交数据的版本数据与数据库表对应记录的当前版本信息进行比对，如果提交的数据版本号大于数据库表当前版本号，则予以更新，否则认为是过期数据。

    3.当两个不同的事务同时读取到一条数据并进行修改时，就会报错，解决方案，尽量从业务方面减小事务块，事务块越大，由乐观锁引起的问题的概率就越大

*   悲观锁：假设一定发生冲突，屏蔽一切可能影响数据完整性的操作

    1.  先取锁再访问 为数据处理的安全性提供了保证
    2.  效率方面，让数据库产生了额外的开销 ，增加了死锁的机会
    3.  对于只读事务处理，不会产生并发冲突，加悲观锁，指挥增加系统的负载，降低了并行性
    
*   数据库隔离级别，每个级别会引发问题，mysql默认是哪个级别

    脏读：事务B读取事务A还没有提交的数据 
    不可重复读：两次事务读的数据不一致 
    幻读:事务A修改了数据，事务B也修改了数据，这时在事务A看来，明明修改了数据，昨不一样

    |          | 隔离级别 | 脏读 | 不可重复读 | 幻读 |
    | :------: | :------: | :--: | :--------: | :--: |
    | 未提交读 | 最低级别 |  是  |     是     |  是  |
    | 已提交读 |  语句级  |  否  |     是     |  是  |
    |  事务级  |  事务级  |  否  |     否     |  是  |
    |  串行化  | 最高级别 |  否  |     否     |  否  |

* 索引有B+索引和hash索引，各自的区别 
    * hash索引，等值查询效率高， 不能排序 ，不能进行范围查询
    * B+索引 ，数据有序 ，范围查询

* 索引的底层实现（B+树，为何不采用红黑树，B树） 
    红黑树，增加删除会频繁的调整，来保证红黑树的性质，浪费时间 
    B树，查询性能不稳定，查询结果高度不致， 
    B树，每个结点保存指向真实数据的指印，相比B+树每一层每屋存储的元素更多，显得更高一点。

* 聚集索引和非聚集索引区别 
    聚集索引，数据按索引顺序存储，中子结点存储真实的物理数据 
    非聚集索引，存储指向真正数据行的指针

* 为什么使用B-/+Tree 
    索引查找过程中就要产生磁盘I/O消耗,主要看IO次数，和磁盘存取原理有关。 根据B-Tree的定义，可知检索一次最多需要访问h个节点。数据库系统的设计者巧妙利用了磁盘预读原理， 将一个节点的大小设为等于一个页，这样每个节点只需要一次I/O就可以完全载入 
    局部性原理与磁盘预读

* 数据库的主从复制 

    * 异步复制

        默认,容易造成主库数据和从库不一致 
        一个数据库为Master,一个数据库为slave,通过Binlog日志slave两个线程，一个线程去读master binlog日志，写到自己的中继日志 
        一个线程解析日志，执行sql 
        master启动一个线程，给slave传递binlog日志

    * 半同步复制 
        只有把master发送的binlog日志写到slave的中继日志，这时主库 
        才返回操作完成的反馈，性能有一定降低

    * 并行复制 
        slave 多个线程去请求binlog日志

* 数据库连接池的作用 
    维护一定数量的连接，减少创建连接的时间 
    更快的响应时间 
    统一的管理

* 从m开始显示n条

    select distinct Salary ......limit n offset m

    把name相同的放在一起执行函数，如果都不相同的字段用group by 表示全都操作一遍

    select fun ...group by name

* when ’值‘ then A else B end)，

* 注释

    ```sql
    SELECT prod_name --这是一条注释
    FROM Products;
    
    /* SELECT prod_name, vend_id
    FROM Products; */
    ```


* 小图驱动大图：视图

* 游标

    ```sql
    DECLARE cur1 CURSOR FOR SELECT id,data FROM test.t1
    OPEN CURSOR name
    FETCH name into name
    CLOSE name
    ```

* 变量

    ```sql
    # 全局
    SET @name同时操作
    # 局部
    DECLARE name char(10)
    SET 操作
    ```

* 循环

    ```sql
    WHILE
    ENDD WHILE
    ```

* 检查约束

    ```sql
    CREATE TABLE tb_emp7(salary FLOAT,CHECK(salary>0 AND salary<100));
    ALTER TABLE tb_emp7　ADD CONSTRAINT check_id　CHECK(id>0);
    ALTER TABLE <数据表名> DROP CONSTRAINT <检查约束名>;
    ```


# 其他

* 慢查询

    ```sql
    --查看设定
    show variables like 'long%'
    --设置阈值
    set long_query_time=0.0001
    --查看设置
    show variables like 'slow%'
    --开启慢查询日志
    set global slow_query_long=ON
    --查看慢查询日志
    use mysql;
    select * from slow_log\G;
    
    slow_query_log=ON --捕获执行时间超过数值语句
    slow_query_log_file --记录日志的文件名
    log_queries_not_using_indexes=ON --捕获未使用索引的语句
    ```

* mysql设置文件

    ```sql
    show variables\G;                  
    ```

* 字段

    ```python
    - CHAR和VARCHAR
    CHAR:长度是不可变,英文占1字节，汉字占2字节
    VARCHAR:长度是可变,英文汉字都占2字节
    - NOW和CURRENT_DATE
    NOW:年份，月份，日期，小时，分钟和秒
    CURRENT_DATE:年份，月份，日期
    ```

* drop、delete与truncate的区别

    ```python
    -drop删除表库
    -delete删除数据
    -reuncate清空表
    ```

* 存储过程

    * 预编译的 SQL 语句
    * 比单纯 SQL 语句执行要快。

* 表链接

    * 内连接：复合显示
    * 外连接
        * 左外连接：左表全部显示，右表匹配的显示
        * 右外连接：右表全部显示，左表匹配的显示
        * 全外连接：不匹配的显示

    * 交叉连接： 笛卡尔效应

* 语句优化

    * 使用join代替子查询；
    * 用 exists 代替 in ；
    * 用Where代替 HAVING 
    * 不要用select *
    * 不要用!= <>
    * 不要用 null 值判断
    * 不要用 or 来连接条件
    * 拆分大的delete或insert语句；
    * 使用limit对查询结果的记录进行限定；

* 表结构优化

    * 每张表设置一个主键ID
    * int < date,time < char,varchar < blob
    * 使用合理的字段属性长度
    * 给空字段设置默认值
    * 建立合适的索引

* union、union all的区别

    ```python
    - union　筛选掉重复记录
    - union all　不筛选
    ```

* varchar(20)和int(20)中

    ```python
    - varchar 最多存储20个
    - int 最多显示20个
    ```

* 储存过程和触发器

    ```python
    储存过程　－　主动调用，更快
    触发器　－　自动调用，不快
    ```

* explain

    ```python
    # 使用
    explain sql ;
    # 参数
    table：数据是关于哪张表的
    type：连接使用了何种类型。
    	从最好到最差的连接类型为
        const -主键索引
        eq_reg -唯一性索引
        ref　-非唯一性索引
        range　-范围索引
        index -全表索引
        ALL -全表数据
    possible_keys：可能应用在这张表中的索引
    key： 实际使用的索引
    key_len：使用的索引的长度
    ref：显示索引的哪一列被使用了
    rows：MySQL认为必须检查的用来返回请求数据的行数
    Extra：关于MySQL如何解析查询的额外信息
    ```

# 触发器

## 基础概念

* 定义 (百度百科)

    触发器（trigger）是SQL server 提供给程序员和数据分析员来保证数据完整性的一种方法，它是与表事件相关的特殊的[存储过程](https://baike.baidu.com/item/存储过程/1240317)，它的执行不是由程序调用，也不是手工启动，而是由事件来触发，[比如](https://baike.baidu.com/item/比如)当对一个表进行操作（ insert，delete， update）时就会激活它执行。触发器经常用于加强数据的完整性约束和业务规则等。

* 简化理解

    Mysql中声明的，在增/删/改等操作之前/之后必须执行的固定操作。可以理解你在某宝购买商品时，某宝必须要对你购物车的数据表进程操作，在操作这个表数据之前，必须在用户表比对你的登录信息

* 优势：

    保证数据同步完整性

* 比较

    * 触发器：执行速度大致和普通语句相同，事件触发自动调用
    * 储存过程：预先编译，执行快，人工调用

## 操作命令

* 查看触发器

    ```sql
    show triggers from databaseName;
    ```

* 删除触发器

    ```sql
    drop trigger if exists databaseName.triggerName;  
    ```

* 创建触发器

    ```sql
    create trigger 
    triggerName --触发器名称
    
    time --触发时机，after/before(常用)
    event --触发事件，insert/update/delete
    on tableName --作用的表名
    for each row --固定搭配
    begin  
        sql语句;
        [old --表示增删改之前数据]
        [new --表示增删改之后数据]
    end
    ```

    * insert类型触发器

        ```sql
        test表插入记录前将新数据的key设置为value
        ……
        before insert on test
        for each row
        begin
             set new.key='value';    
        end;
        ```

    * delete类型触发器

        ```sql
        test表更新数据时用tempkey储存记录原先记录的key的值
        ……
        before delete on test
        for each row
        begin
             declare tempkey char(10);
             set tempkey = old.key;    
             -- old用来表示将要或已经被删除的原数据。
        end;
        ```

    * update类型触发器

        ```sql
        test表更新数据时用tempkey储存记录原先记录的key的值，并和新数据的key拼接作为新的key值执行更新
        ……
        before update on test
        for each row
        begin
             declare tempkey char(10);
             set tempkey = old.key;     
             set new.key= tempkey||new.key; 
        end;
        ```

 