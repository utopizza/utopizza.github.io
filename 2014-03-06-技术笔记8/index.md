# 技术笔记(8)-SQL多条件查询字符串拼接问题


一、SQL多条件查询

在实现SQL的多条件查询时，一般采用的实现方法是根据传入的参数是否有效来判读是否拼接该项查询条件，如

```
// 传入参数：@ProjectName='rrrr'，@ProjectNumber='123'

// 先写好基本查询句子
declare @sqlString varchar(100)
set @sqlString = 'select * from Project_Info where 1=1'

// 拼接查询条件1
if (@ProjectName IS NOT NULL) AND (@ProjectName <> '') 
begin
    set @sqlString = @sqlString + ' and ProjectName like ''%' + @ProjectName + '%'''
end

// 拼接查询条件2
if (@ProjectNumber IS NOT NULL) AND (@ProjectNumber <> '')
begin
    set @sqlString = @sqlString + ' and ProjectNumber =''' + @ProjectNumber + ''''
end
    
// 执行最终结果
exec @sqlString
```

二、问题

拼接SQL时，如果不小心注意参数的引号，容易出现如下错误：

```
set @sqlString = @sqlString + ' and ProjectNumber =' + @ProjectNumber
```

执行报错，原因是在sql存储过程输入参数的时候，输入的rrrr，会直接赋给@ProjectName，系统不会自动给rrrr加上''两个单引号，导致最终SQL语句变成：

```
select * from Project_Info where 1=1 and ProjectNumber = 123
```

而正确SQL的应该是：

```
select * from Project_Info where 1=1 and ProjectNumber = '123'
```

于是系统报错“列名'rrrr'无效”。解决办法：使用多个连续的单引号：

```
set @sqlString = @sqlString + ' and ProjectNumber =''' + @ProjectNumber + ''''
```

三、SQL Server中的单引号使用规则

首先尽量不要在SQL中使用双引号。单引号如果要作为一个“字符串单引号”，那就在它的前面加一个单引号进行转义。即：

两个连用的单引号 == 一个“字符串”意义上的单引号（即 '' == " ' "）

| SQL 语句 | 执行结果 |
| :---: | :---: |
| ` select '''''' ` | '' |
| ` select ' '' '' ' ` | ' ' | 
| ` select ' ''ab ' ` | 'ab | 
| ` select ' ''a''b ' ` | 'a'b | 
| ` select ' ''a''b'' ' ` | 'a'b' | 

技巧：拿到一个包含很多单引号的字符串（如，` ' and ProjectName like ''%' `），分析时，首先可以确定第一个和最后一个单引号都是传统的最外围的用来定义这条字符串的单引号，把它们除开后，对于内部其他多个连用的单引号，把每两个连用的单引号替换成一个“字符串单引号”即可（如，` and ProjectName like " ' " `）。

