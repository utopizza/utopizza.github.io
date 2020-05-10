# 技术笔记(9)-MySql语法小结



一、数据库

1.数据库的选定

```
use testDB;
```

2.数据库的创建（后面两个是设置字符集和排序方式）：

```
create database(if not exist) testDB (character set charset)(collate collation);
```

3.数据库的删除：

```
drop database testDB;
```

4.数据库的更变：

```
alter database testDB(character set charset)(collate collation);
```

二、数据表

1.存储引擎（查看存储引擎：show engines）：

 - ARCHIVE——插入 
 - BLACKHOLE——删除 
 - EXAMPLE——示例 
 - Falcon——事务 
 - FEDERATED——访问远程数据
 - InnoDB——外键 
 - MEMORY——内存数据表 
 - MERGE——联合 
 - MyISAM——默认 
 - NDB——MySQL 
 - Cluster专用
 - CSV——使用逗号分隔数据项

2.数据表的创建：

(1).数据表选项（选择存储引擎，否则默认为MyISAM）：

```
create table testTB( ... ) engine=InnoDB;
```

(2).创建全新的数据表：

```
create table if not exist testTB( ... ) engine=InnoDB;
```

(3).创建临时数据表：

```
create temporary table testTB( ... )
```

(4).使用查询结果或者其它数据表创建数据表：

i.如果要确保新表和原表的数据列的属性、索引都完全一致：

```      
create table testTB like exampleTB;
insert into testTB select * from exampleTB;
```

ii.如果只要保证列名和数据一致，不需复制数据列的属性和索引：

```
create table testTB select * from exampleTB;
```

iii.为了弥补上一点，可以使用cast()函数强制使用特定的属性：

```       
create table ... select cast(... as int) as ... , cast(... as char) as ... ;
```

(5).使用MERGE数据表，设置插入方式（要联合的表必须是结构完全一致的MyISAM数据表）：

```
create table test_4( ... )  engine=MERGE union=(test_1,test_2,test_3)  insert_method=last;
```

(6).使用分区数据表：

```
create table testTB
(
   id int not null,
   name varchar(20) null
)
partition by range(id)
(
   partition p1 values less than 10,
   partition p2 values less than 20,
   partition p3 values less than 30,
   partition p4 values less than 40,
   partition p5 values less than MAXVALUE,
);
```

(7).使用federated数据表：

```
create table federated_testDB( ... )  
engine=FEDERATED  
connection='mysql://wys:79135QW@hostname/testDB/testTB';
```

为了避免暴露连接字符串中的信息，可以：

```
create server testDB_server
foreign data wrapper mysql
options
(
  user 'wys'
  password '79135qw'
  host '...'
  database 'testDB'
);

create table federated_testTB( ... ) 
engine=FEDERATED
connection='testDB_server/testTB'
```

3.数据表的删除：

```
drop table if exist testTB;
```

4.数据表的索引：

(1).创建索引：

```
// 在create内部
create table testTB( 
id int auto_increment primary key not null,
...
index index_name(id),...auto 
);

// 或者在create外部
create table testTB(...);
alter table testTB add index_name(id);
```

(2).删除索引：

```
drop index index_name on testTB; 
//或者 
alter table testTB drop index index_name;
```

5.改变数据表的结构：

(1).改变数据列的类型：

```
alter table testTB modify id varchar(100); 
// 或者 
alter table testTB change id id_newname varchar(100);
```

(2).改变存储引擎：

```
alter table testTB engine=InnoDB;
```

(3).重新命名数据表：

```
alter table testTB RENAME TO new_testTB;
```

(4).把一个数据表从一个数据库移动到另一个数据库：

```
rename table testDB.testTB to testDB_2.testDB
```

三、数据检索

1.获取数据库的元数据（各种show）：

```
show database;
show tables from ...;
show colums from ... like ...;
```

2.利用联结操作对多表进行检索：

```
select ... from ...
inner/left/right/cross join ... on ...=...
where ...
group by ...
order by ...
having ...
limit ...
```

3.使用子查询对多表进行检索：

(1).in和not in：是否存在或者不存在子查询中
(2).all和any和some：all所有；any任何一个；some和any等价
(3).exist和not exist：判断子查询是否有结果集

4.使用union对多表进行检索：

```
(select ... from ...) 
union 
(select ... from ...) 
union 
(select ... from ...) 
...
```

5.使用视图（可用视图完成一些必要的数学运算）：

```
ctreate view testView as select ... from ...
```

6.涉及多个数据表的删除和更新操作

```
//删除表t1中id与表t2匹配的行
delete t1 from t1 inner join t2 on t1.id=t2.id;

// 删除表t1中id不与表t2匹配的行
delete t1 from t1 left join t2 on t1.id=t2.id where t2.id is null;

//同时删除表t1和表t2中id匹配的行
delete t1,t2 from t1 inner join t2 on t1.id=t2.id;

// 或者使用using
delete from t1 using t1 inner join t2 on t1.id=t2.id;
delete from t1 using t1 left join t2 on t1.id=t2.id where t2.id is null;
delete from t1,t2 using t1 inner join t2 on t1.id=t2.id;
```

四、事务处理（需要使用的存储引擎是InnoDB或者Falcon）

```
start transaction; // 或者set autocommit=0
insert into ...
delete from ...
commit; // set autocommit=1; 对应于上面的括号内的语句
```

1.事务的特点：

- a.原子性，事务内容要么全部成功，要么全部失败
- b.隔离性，事务之间是隔离的，比如在某事务向数据库插入一些数据的时候，如果事务还没结束，即使某些数据已经插入去了，但是这些数据对其他所有用户是不可见的，直到事务完成提交
- c.稳定性，数据在事务开始和结束后都是稳定的状态
- d.可靠性，如果事务执行成功，它的影响将被永久保存到数据库

2.备注：在默认的情况下，MySQL从自动提交（autocommit）模式运行，这种模式会在每条语句执行完毕后把它当做一个事务进行提交，如果使用了 ` set autocommit=0 ` 语句后，这个自动提交将被禁止，这样的结果是，除非遇到一些显式提交语句（如：commit，rollback...），或者隐式提交前一个事务，然后在本语句重新开始事务的DLL语句（如：alter table，create index，drop database，drop index...），系统会认为接下来的所有的语句都属于一个事务

3.使用事务保存点：使事务部分回滚

```
create table testTable(id int) engine=InnoDB;
start transaction;
insert into testTable(id) values(1);
SAVEPOINT my_savepoint;
insert into testTable(id) values(2);
rollback to SAVEPOINT my_savepoint;
insert into testTable(id) values(3);
commit;
select * from testTable;
// 此时表中的数据只有1和3，因为2被rollback了
```

4.事务的隔离性：InnoDB存储引擎提供4种隔离级别：

 - read uncommited——允许某个事务看到其他事务尚未提交的数据改动 read
 - commited——只允许某个事务看到其他事务已经提交的数据改动 repeatable
 - read（默认级别）——即使在本事务进行中，有其他事务在同时修改了数据行，本事务每次执行同一个select的结果不变
 - serializable——更彻底的隔离，如果某个事务正在读取某些数据行，那么在它完成之前，其他事务对这些数据行不可改动

修改级别：

```
set global transaction isolation level ...;（全局有效）
set sessoin transaction isolation level ...;（本次会话有效）
set transaction isolation level ...;（下一次事务有效）
```

5.事务问题的非事务解决方案：

a.明确的锁定数据表

```
LOCK tables testTable write;
select ... from ...;
update ... set ...;
UNLOCK tables;
```

b.使用相对更新操作，不使用绝对更新操作（即在一个语句中，使用当前值进行差额修改的方法，而不是直接设置为一个绝对值）

```
update ... set ... = ... + ... where ... 
```

五、外键的创建和使用

1.语法

2.InnoDB存储引擎目前只能识别和支持以下外键定义语法

1).constraint：用来给外键约束起一个名字
2).foreign key：列出子表的被索引数据列
3).references：列出父表和父表中的被索引数据列
4).on delete：用来设定父表里的数据行被删除时，子表中对应的数据应该发生什么事件，其子句可选择如下：
  
  - on delete (no action) restrict：拒绝在父表里删除那些与子表仍有数据关联的数据行（默认）
  - on delete cascade：删除父表数据行时，子表里相关的数据行也被删除
  - on delete set null：删除父表数据行时，子表里相关的数据行的索引列将被设置为NULL（隐含着外键不能定义为主键的意思，因为主键不能为NULL）

5).on update：同delete

六、使用FULLTEXT索引

七、存储过程

1.复合语句和语句分隔符：使用复合语句的时候，必须先使用delimiter命令把mysql程序的语句分隔符重定义为另一个字符或字符串，它必须是存储例程里没有定义过的。在定义完了存储过程之后，切记把mysql程序的语句终止符重定义为分号

```
delimiter $（或者字符串asdfs，erer等等都可以）
create procedure test()
begin
   select ... from ... ;
end $
delimiter ;
call test();
```

2.存储函数（只能有一个返回值）

```
delimiter $
create function test_function(id int)
returns int
reads sql data
begin
    return (select ... from ... where ...=id);
end $
delimiter ;
```

3.存储过程（返回结果集）。和存储函数不同，存储过程不能使用在表达式里，只能通过CALL语句来调用：call test_procedure(100);
另外，存储函数不允许对调用本函数的语句正在读或写的数据表进行修改，存储过程没有这个限制，但是如果它们是从存储函数里被调用，就也需要遵守这个规则

```
delimiter $
create procedure test_procedure(id int)
begin
    select ... from ... where ...>=id;
end $
delimiter ;
```

4.存储过程的参数类型（在参数名前说明）

- in参数：输入的参数（默认）
- out参数：输出的参数（需在过程中设置变量）
- inout参数：输入一个值然后再输入一个值（需在过程中设置变量）

八、触发器（触发器是与特定数据表相关联的存储过程，当相应的数据表被insert，delete，或者update语句修改时就会自动执行）

```
delimiter $
create trigger test_trigger before(after) insert on testTable
    for each row begin
        if NEW.col_name(OLD.col_name) then ... ;
        end if;
    end $
delimiter ;
```

九、事件（定时执行预设好的数据库操作，和SQL SERVER中的作业相当）

1.启用（停止）事件调度器：

```
set event_scheduler=on（off）
```

2.查看：

```
show variables like 'event_scheduler' ;
```

3.创建：

```
create event test_event  on schedule every 4 hour
do 
delete from testTable where ...
```

4.禁用或者开启某个事件

```
alter event test_Event disable;
alter event test_Event enable;
```

十、关于建表：

- enum('F','M')：该字段只能是F或M的枚举类型
- unsigned：不允许出现负数
- auto_increment：自增
- primary key(student_id)：指定主键 
- foreign key(score_id) references - - ScoreTable(score_id)：指定外键（一般外键是其他某个表的主键）
- index(student_id)：指定索引
- engine=InnoDB：选择数据引擎为InnoDB，该引擎允许外键的使用


十一、语法：

1.NULL是无数据，或未知数据，不能使用=，<>，！=来进行比较，只能用 is null 或者 is not null

2.升序排列asc，降序排列desc

3.设置查询显示的行数：order by ... desc limit 5;

4.随机抽取3条数据：order by RAND() limit 3

十二、模式匹配：
like，not like，%，_

十三、设置和使用SQL变量：

```
select @studentName:=name from studentTable where id=1
set @studentName='abc'
```













