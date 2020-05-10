# 技术笔记(4)-ADO.NET



一、ADO.NET主要用于访问数据，尤其是数据库中的数据，该类被封装在System.Data.dll中。它的两个核心组件：DataSet和.NET数据提供程序

1、DataSet：记录在内存中的数据，相当于内存中的一个完整的数据集。但它并不与外部的物理数据库直接相连，而是通过DataAdapter间接的完成与外部数据库的交互。该类的主要对象有：

- DataTable：使用行和列形式来组织的一个矩形数据集
- DataColumn：列（规则的集合）
- DataRow：行（实际的对应于规则的数据存储）
- Constraint：决定能进入DataTable的数据
- DataRelation：不同的DataTable之间的关联

2、.NET数据提供程序：主要有4种对象，分别是：

- Connection：提供与数据源的连接
- Command：执行一系列的数据库命令
- DataReader：从数据源中提供高性能的数据流
- DataAdapter：提供连接DataSet对象和数据源的桥梁。DataAdapter通过Command对象在数据源中执行SQL命令，并将数据加载到DataSet中，使DataSet中数据的更改与数据源保持一致

二、ADO.NET Connection：Connection类表示一个数据源的单个连接，但并不是单个调用。ADO.NET支持断开式连接，即可在一个Connection对象的使用过程中多次打开或关闭操作，但这些操作并不意味着该对象被创建或者销毁。主要属性有ConnectionString，ConnectionTimeOut，Database，ChangeDatabase，State等。主要的方法有：Open()，Close()。主要的状态属性有：Broken，Closed，Connecting，Executing，Fetching，Open。

三、ADO.NET Command：从本质上讲，ADO.NET的Command类就是SQL命令或者是对存储过程的引用。

1、主要属性有：

- CommandText：执行命令的内容（字符串），或者存储过程的名称
- CommandType：告诉Command对象怎样解释CommandText属性的内容，当该属性的值为StoredProcedure时，说明CommandText属性的内容为存储过程名称；当为TableDirect时，说明内容为表名；当为Text时，说明为SQL文本命令（即直接的sql语句）
- Connection：对Connection对象的引用
- Parameters：包含了针对CommandText属性所指定的SQL命令或存储过程的参数集合（通过函数AddRange(paras)向Command对象添加参数集）
- CommandTimeout：决定了命令出错前等待服务器响应的时间
Transaction：
UpdateRowSource:

2、主要的方法有：
	
- ExecuteNonQuery()：执行命令并返回受影响的行数（用于返回结果为空的sql命令或存储过程）
- ExecuteScalar()：执行查询并返回结果集中的第一行的第一列（用于返回单个值的sql命令或存储过程）
- ExecuteReader()：向Connection发送CommandText并生成DataReader（用于返回多个结果的sql命令或存储过程，该方法创建一个DataReader对象。该方法执行时可以不带参数，或者带一个参数CommandBehavior）
- ExecuteXmlReader()：向Connection发送CommandText并生成XmlReader

四、DataReader：当Command对象返回结果集时，需要用DataReader来检索数据。DataReader对象返回一个来自Command对象的只读的，只能向前的数据流。DataReader每次只在内存中保留一行，所以开销非常小。

1、主要属性：
	
- Depth：多层结果集中，当前行的嵌套深度
- FieldCount：当前行的数目
- IsClosed：DataReader是否需要关闭
- Item：列值
- RecordsAffected：被修改，插入或者删除的行的数目

2、主要方法：

- Close()：关闭
- GetType()：将指定的列的值作为指定类型获取
- GetDataTypeName()：获取数据源类型的名称
- GetFieldType()：返回指定列的系统类型
- GetName()：获得指定列的名称
- GetValue()：获得指定列的值
- NextResult()：前进到得下一个结果
- Read()：前进到下一列

五、DataAdapter：DataAdapter是DataSet和物理数据源之间的桥梁，即DataSet通过DataAdapter与数据库间接交互。DataAdapter类的目的是执行数据库查询并创建包含查询结果的DataTable，该类也可以把对DataTable的更改写回数据库。

1、主要方法：
	
- Fill()：将查询结果填充DataSet
- Update()：将DataSet中的数据的变动写回数据库

六、应用实例

```
Sqlconnection conn=new SqlConnection();
conn.ConnectionString="Server=XXX;"+"Database=XXX;"+"Intergrated Security=true;";

SqlCommand cmd=new SqlCommand();
cmd.CommendText="select * from studentTable";
cmd.Connection=conn;

conn.Open();

DataTable dt=new DataTable();
DataSet ds=new DataSet();

SqlDataAdapter adapter=new SqlDataAdapter();
adapter.SelectCommand=cmd;
adapter.Fill(ds,"dt");

conn.Close();

dt=ds.Tables["dt"];
this.GridView.DataSource=dt.DefaultView;
this.GridView.DataBind();
```

