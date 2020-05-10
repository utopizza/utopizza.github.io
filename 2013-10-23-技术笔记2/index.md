# 技术笔记(2)-SQL语法小结



1、 Transact-SQL语言分类

- 数据定义语言（DDL）：创建，删除，修改数据表，建立索引和约束，创建其他数据库对象等
- 数据操纵语言（DML）：查询，添加，删除，修改数据等
- 数据控制语言（DCL）：控制安全性的命令

2、数据类型：

- 数值型：int,smallint,bigint,tinyint,bit,decimal,numeric,float,real
- 字符型：char,varchar,text
- 日期/时间型：datetime,smalldatetime
- 货币型：money,smallmoney
- 二进制型：binary,varbinary,image
- unicode型：nchar,nvarchar,ntext
- sql-variant型：混合型
- table型：临时表
- 自定义型

3、标识符命名规则：

- 规则标识符（符合标识符规则，不用分隔符）
- 分隔标识符（不符合标识符规则，必须包含在" "和[ ]内）

4、对象命名规则：服务器名+数据库名+所有者名+对象名，如：sever.database.owner_name.object_name

5、常量：见上数据类型
	
6、变量：

```
//声明
DECLARE  @myint int，@mychar char(8)
	  	           
//赋值：
SET  @myint=10  
SET @mychar='wang' 
SELECT  @myint=10，@mychar='wang'

//查询（即输出）
SELECT  @myint  as  myint
```

7、全局变量：系统预定好的变量，只读，如：@@ERROR，@@DBTS

8、算术运算：+，-，*，/，%

9、逻辑运算：

- and：仅当两个都为true时返回true
- or：仅当两个都为false时返回false
- not：对布尔表达式取反
- all：如果全都为true，返回true
- any：如果有一个为true，返回true
- like：如果操作数与一种模式匹配，返回true
- in：如果操作数等于表达式列表中的一个，返回true

10、字符串连接符：+

11、比较运算符：=，<，>，！=，<=，>=，<>（不等于）

12、null与空判断：null为空值，即未知值（不是0或空字符），用操作符is来运算，而不是用比较运算符

13、大对象处理：对ntext，text，image都是当作二进制大对象（BLOB）进行访问的，有专门的语句writetext，updatetext，readtext等来处理

14、流程控制：

```
declare @cal int
select @cal=1
while(@cal<5)
begin
    select @cal=@cal+1
end
```

15、select语句

```
select * from newstable  
select ID,title,content from newstable  
select content1 + ' , ' + content2 as content from newstable  
select top 10 ID from newstable
```

16、where语句：

```
select * from newstable where ID = 10
```
	   
17、添加排序顺序：

```
select * from newstable where caID = 10 order by createTime  
select * from newstable where caID = 10 order by createTime desc
```

18、添加汇总信息：（聚合函数）

```
select AVG(ID) from newstable  返回newstable表的ID列的平均值
select MAX(ID) from newstable  返回newstable表的ID列的最大值
select MIN(ID) from newstable  返回newstable表的ID列的最小值
select COUNT(ID) from newstable where ID<10  
select SUM(ID) from newstable where ID<10  
```

19、group by语句：必须对不出现在聚合函数中的列使用该语句，否则报错。SQL要求，在含聚合函数的查询中，任何列名要么加入聚合函数中，要么包含在group by短语中

```
select SUM(ID),caID from newstable 
// 必须改为
select SUM(ID),caID from newstable group by caID
```

20、添加HAVING子句

```
select caID from newstable group by caID having min(ID)>7
```

21、distinct：去除重复的值

```
select distinct caID from newstable
```

22、内连接：

```
select * from table1 inner join table2 on table1.ID=table2.ID  返回table1和table2中所有满足table1.ID=table2.ID的行
```

23、外连接：

```
select * from table1 left join table2 on table1.ID=table2.ID ;
select * from table1 right join table2 on table1.ID=table2.ID
```

24、组合字段，建立新列

```
select ID * caID as ID_caID from newstable  
```

25、insert语句：

```
insert into newstable(title,content) values('wang','yusheng') 
insert into test select title,content,createTime from newstable where createTime<GETDATE()
```

26、select into语句，利用已有表的字段创建新表（可作为临时表）：

```
select newstable.title,content,createTime into test2 from newstable
```

27、update语句：

```
update test set createTime=GETDATE(),content='abcdefg' where ID=1
update test2 set createTime=GETDATE() where title not like 'wang'
update test2 set content='update2' where title in ('wang','sheng')
```

28、delete语句：由于delete语句操作不可恢复，所以删除数据前最好用select into语句建立一个临时表来备份

```
delete from test where ID=1
delete from test where ID in (select ID from test group by ID having sum(ID) <3)
```

29、创建表：

```
create table categorytable
(
  ID int identity(1,1) primary key , 
  [name] varchar(20) not null default 'abcd'
  ...
)
```

30、键：键是行标识符，能唯一的标识行和列。但键并不是独立的另一个列，它可以是表中任何一列或多个列组合来表达，但被定义为键的列不能有重复的项，必须保持其唯一性。

31、主键：本表的行标识符

```
create table newtable(newText char(10) null primary key)
```

32、外键：外键是在另一个表中出现的主键的实例

```
create table newstable(newsID int references category(newsID))
```
          
33、数据规范化：设计一组表的过程叫规范化，目标是避免数据的冗余存储

- 第一范式：列出所有要保存的数据项，并为他们建立列名称，并把相关的列组合到表中，对每个表建立一个主键
- 第二范式：检查每一列，将完全依赖主键的列保留，将部分依赖主键的列移到另一个新表中重新组合
- 第三范式：存在可传递关系的列从单一的表中除去，移到单独的表中

34、添加索引：（数据库一般自动为表中的主键建立索引）

- 唯一索引：不允许两行有相同的索引，不允许重复值，没有可以引用到多行的索引条目
- 群集索引：在其内部排序的索引，并且按照索引排序结构规定了行的存储结构（存储顺序），每张表中只能有一个群集索引，索引要求唯一
- 非群集索引：对表中的行生成索引但不改变他们的存储顺序，索引不要求唯一

35、建立索引：

```
create table newstable
(
    newsText char(6) null) primary key unique(唯一索引)
    [或clustered(群集索引)，nonclustered(非群集索引)]
)
    
create table newstable(...)
create unique clustered index newsID_ind on newstable(newsID)
```

36、添加约束：约束可以是主键，外键的引用，或者是数据验证的规则

37、更改表：

```
alter table newstable add ID2 int null  
alter table newstable drop column ID2  
```

38、删除表：

```
drop table test
```

40、创建数据库：

```
create database mydatabase
```

41、存储过程：存储在服务器上的查询过程称为存储过程

```
create procedure pro_test
as
	select ....
	from test
go
```

42、调用存储过程：
(1) 在数据库中

```
exec  pro_test
```

(2) 在客户端得数据控件中：设定对象，命令对象发出exec语句


