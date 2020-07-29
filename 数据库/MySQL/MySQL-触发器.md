# 触发器triggers

## 基础概念

触发器（trigger）是保证数据完整性的一种方法，是与表事件相关的特殊的存储过程，由事件来触发，不能手工启动。可以理解为在增/删/改等操作之前/之后必须执行的固定操作。经常用于加强数据的完整性约束和业务规则等。比如购买商品前要比对登录信息

-   触发器：执行速度大致和普通语句相同，事件触发自动调用
-   储存过程：预先编译，执行快，人工调用

## 操作命令

-   查看触发器

    ```sql
    show triggers from databaseName;
    ```

-   删除触发器

    ```sql
    drop trigger if exists databaseName.triggerName;  
    ```

-   创建触发器

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

    -   insert类型触发器

        ```sql
        test表插入记录前将新数据的key设置为value
        ……
        before insert on test
        for each row
        begin
             set new.key='value';    
        end;
        ```

    -   delete类型触发器

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

    -   update类型触发器

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