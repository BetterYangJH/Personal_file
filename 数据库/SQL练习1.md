# SQL练习(1)－部门符合条件的员工

-   题目

    找出不属于这个部门，但比这个部门所有人都工龄长的员工姓名和部门

-   结构

    ```text
    部门表dept
      -部门名称name
      -员工编号eid
    员工表empl
      -员工编号id
      -员工姓名name
      -员工入职时间birth
    ```

-   解析

    ```text
    不属于部门A，但比部门A所有员工工龄都要大的人
    不属于部门B，但比部门B所有员工工龄都要大的人
    ```

-   思路

    ```text
    1 找出每个部门工龄最大的(入职时间最早)max
    2 找出每个部门最大工龄中最小的min
    3 比这个工龄大且不是在这个min所在的部门的都符合题意要求
    (min所在部门最大工龄就是min,min比其他最大工龄都小，所以该部门不可能有工龄比其他部门所有人都大的)
    ```

-   举例

    ```text
    工龄：
    A 1year--4year
    B 2year--6year
    C 3year--7year
    1 找出每个部门工龄最大的(入职时间最早)
    A4 B6 C7
    2 找出每个部门最大工龄中最小的
    A4
    3 比这个工龄大且不是在这个min所在的部门的都符合题意要求
    B4-6 C4-7都符合要求
    ```

-   sql语句

    ```sql
    --dept(name,eid) empl(id,name,birth)
    --1 聚合成一张表info(id,name,birth,dname)
    CREATE VIEW info as SELECT id,e.name,birth,d.name as dname FROM empl as e LEFT JOIN dept as d ON e.id=d.eid
    --2 找出每个部门最大工龄,既入职最早的tmax(birth,dname)
    SELECT min(birth) as birth,dname FROM info GROUP BY dname
    --3 找出最大工龄里最小的tmin(birth,dname)
    SELECT * FROM tmax order by birth desc limit 1; 
    --4 找出员工表里不在tmin部门，且工龄比tmin大的信息
    SELECT i.name,i.dname FROM info as i,tmin as t
    WHERE i.birth<t.birth
    
    --5 总结
    SELECT i.name,i.dname FROM info as i,(SELECT * FROM (SELECT min(birth) as birth,dname FROM (SELECT id,e.name,birth,d.name as dname FROM empl as e LEFT JOIN dept as d ON e.id=d.eid) as info GROUP BY dname) as maxbirth ORDER BY birth DESC LIMIT 1) as t WHERE i.birth<t.birth;
    ```

-   测试用例

    ```sql
    CREATE TABLE dept(
    	id int primary key,
    	name char(1) not null
    );
    CREATE TABLE empl(
    	id int primary key,
    	name varchar(10) not null,
        birth date not null
    );
    INSERT INTO dept VALUES
    ('A',1),('A',2),('A',3),
    ('B',4),('B',5),('C',6);
    +------+------+
    | name | eid  |
    +------+------+
    | A    |    1 |
    | A    |    2 |
    | A    |    3 |
    | B    |    4 |
    | B    |    5 |
    | C    |    6 |
    +------+------+
    INSERT INTO empl VALUES
    (1,'Monica','1990-01-01'),
    (2,'Rachel','1990-01-02'),
    (3,'Fibby','1990-01-03'),
    (4,'Janes','1989-01-01'),
    (5,'Joee','1991-01-01'),
    (6,'Chandler','1992-01-01');
    +------+----------+------------+
    | id   | name     | birth      |
    +------+----------+------------+
    |    1 | Monica   | 1990-01-01 |
    |    2 | Rachel   | 1990-01-02 |
    |    3 | Fibby    | 1990-01-03 |
    |    4 | Janes    | 1989-01-01 |
    |    5 | Joee     | 1991-01-01 |
    |    6 | Chandler | 1992-01-01 |
    +------+----------+------------+
    --查询结果
    +--------+-------+
    | name   | dname |
    +--------+-------+
    | Monica | A     |
    | Rachel | A     |
    | Fibby  | A     |
    | Janes  | B     |
    | Joee   | B     |
    +--------+-------+
    ```