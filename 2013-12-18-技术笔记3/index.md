# 技术笔记(3)-C#连接数据库




一、创建连接：

1. 引入所使用的ADO.NET类的命名空间（引入System.Data类；使用SQL Server数据库的话就要再引入System.Data.SqlClient类）
2. 创建连接字符串变量，以保存生成连接需要的信息
3. 实例化Connection

二、使用连接：

1. 打开连接
2. 使用连接，主要进行读取数据，修改和删除数据等操作
3. 关闭连接

三、示例：

```
// 引入ADO.NET类的命名空间
using System.Data;
using System.Data.SqlClient;

// 创建连接字符串
string connStr = "server=XXX（服务器名）; database=XXX（数据库）; uid=XXX（用户名）; pwd=XXX（该用户登陆密码）;"
	
// 创建连接数据库的SqlConnection对象，传入连接字符串，打开连接
SqlConnection conn = new SqlConnection(connStr);
conn.Open();

// 创建要对数据库操作的SQL语句
string sql = "insert into subjectTable(ID,name,point) values(2,'物理',4)";

// 创建SqlCommand对象并传入SQL语句操作语句和SqlConnection连接对象
SqlCommand cmd = new SqlCommand(sql, conn);

// 调用ExecuteNonQuery()函数（执行查询语句并返回受影响的行数）
int res=cmd.ExecuteNonQuery();

// 关闭连接，返回数据
conn.Close();
return res;
```

四、共享连接字符串

1.打开项目的web.config文件，在connectionStrings中修改

```
<add name="connStr" connectionString="server=xxx; database=xxx; uid=xxx; pwd=xxx;"/>
```

2.在原来存放连接语句的项目中引入using System.Configuration，并将原来语句

```
string connStr = "server=xxx; database=xxx; uid=xxx; pwd=xxx;"
```

改为

```
string connStr = ConfigurationManager.ConnectionStrings["connStr"].ConnectionString;
```

