# 技术笔记(6)-SqlHelper



刚学ASP.NET时用的比较多的一个简单版的SqlHelper：

```
using System;
using System.Configuration;
using System.Data;
using System.Data.SqlClient;
using System.Collections;

namespace TICSystemCommon 
{
    public abstract class SqlHelper
    {
        //Database connection strings
        public static readonly string ConnectionStringShop = ConfigurationManager.ConnectionStrings["ConnectionString"].ConnectionString;

        /// <param name="connectionString">a valid connection string for a SqlConnection</param>
        /// <param name="commandType">the CommandType (stored procedure, text, etc.)</param>
        /// <param name="commandText">the stored procedure name or T-SQL command</param>
        /// <param name="commandParameters">an array of SqlParamters used to execute the command</param>
        /// <returns>an int representing the number of rows affected by the command</returns>
        public static int ExecuteNonQuery(string connectionString, CommandType cmdType, string cmdText, params SqlParameter[] commandParameters)
        {
            SqlCommand cmd = new SqlCommand();
            using (SqlConnection conn = new SqlConnection(connectionString))
            {
                PrepareCommand(cmd, conn, null, cmdType, cmdText, commandParameters);
                int val = cmd.ExecuteNonQuery();
                //cmd.Parameters.Clear();
                return val;
            }
        }

        /// <param name="connectionString">a valid connection string for a SqlConnection</param>
        /// <param name="commandType">the CommandType (stored procedure, text, etc.)</param>
        /// <param name="commandText">the stored procedure name or T-SQL command</param>
        /// <param name="commandParameters">an array of SqlParamters used to execute the command</param>
        /// <returns>A SqlDataReader containing the results</returns>
        public static SqlDataReader ExecuteReader(string connectionString, CommandType cmdType, string cmdText, params SqlParameter[] commandParameters)
        {
            SqlCommand cmd = new SqlCommand();
            SqlConnection conn = new SqlConnection(connectionString);

            // we use a try/catch here because if the method throws an exception we want to 
            // close the connection throw code, because no datareader will exist, hence the 
            // commandBehaviour.CloseConnection will not work
            try
            {
                PrepareCommand(cmd, conn, null, cmdType, cmdText, commandParameters);
                SqlDataReader rdr = cmd.ExecuteReader(CommandBehavior.CloseConnection);
                //cmd.Parameters.Clear();
                return rdr;
            }
            catch(Exception e)
            {
                conn.Close();
                throw e;
            }
        }

        /// <param name="connectionString">a valid connection string for a SqlConnection</param>
        /// <param name="commandType">the CommandType (stored procedure, text, etc.)</param>
        /// <param name="commandText">the stored procedure name or T-SQL command</param>
        /// <param name="commandParameters">an array of SqlParamters used to execute the command</param>
        /// <returns> the first column of the first record against an existing database connection</returns>
        public static object ExecuteScalar(string connectionString, CommandType cmdType, string cmdText, params SqlParameter[] commandParameters)
        {
            SqlCommand cmd = new SqlCommand();
            using (SqlConnection connection = new SqlConnection(connectionString))
            {
                PrepareCommand(cmd, connection, null, cmdType, cmdText, commandParameters);
                object val = cmd.ExecuteScalar();
                cmd.Parameters.Clear();
                return val;
            }
        }

        /// <summary>
        /// Prepare a command for execution
        /// </summary>
        /// <param name="cmd">SqlCommand object</param>
        /// <param name="conn">SqlConnection object</param>
        /// <param name="trans">SqlTransaction object</param>
        /// <param name="cmdType">Cmd type e.g. stored procedure or text</param>
        /// <param name="cmdText">Command text, e.g. Select * from Products</param>
        /// <param name="cmdParms">SqlParameters to use in the command</param>
        private static void PrepareCommand(SqlCommand cmd, SqlConnection conn, SqlTransaction trans, CommandType cmdType, string cmdText, SqlParameter[] cmdParms)
        {

            if (conn.State != ConnectionState.Open)
                conn.Open();

            cmd.Connection = conn;
            cmd.CommandText = cmdText;

            if (trans != null)
                cmd.Transaction = trans;

            cmd.CommandType = cmdType;

            if (cmdParms != null)
            {
                foreach (SqlParameter parm in cmdParms)
                    cmd.Parameters.Add(parm);
            }
        }
    
    
    }
}
```

在DAL层调用SqlHelper：

```
using System.Text;
using TICSystemCommon;
using TICSystemModel;
using System.Data;
using System.Data.SqlClient;

namespace TICSystemDAL
{
    public class AccidentInfoDal
    {
        
        public AccidentInfoModel GetAccidentInfoByAccidentId(int AccidentId)
        {
            AccidentInfoModel AccidentInfo = new AccidentInfoModel();
            string sql = "Proc_GetAccidentInfoByAccidentId";
            DataTable dt = new DataTable();
            SqlParameter[] para = new SqlParameter[]
            {
                new SqlParameter("@AccidentId",AccidentId)
            };
            try
            {
                using (SqlDataReader dr = SqlHelper.ExecuteReader(SqlHelper.ConnectionStringShop, CommandType.StoredProcedure, sql, para))
                {
                    if (dr.Read())
                    {
                        AccidentInfo.AccidentId = Convert.ToInt32(dr["AccidentId"].ToString());
                        AccidentInfo.AccidentPlace = dr["AccidentPlace"].ToString();
                        AccidentInfo.AccidentTime = dr["AccidentTime"].ToString();
                        AccidentInfo.PersonName = dr["PersonName"].ToString();
                        AccidentInfo.PersonPhone = dr["PersonPhone"].ToString();
                        AccidentInfo.CarNumber = dr["CarNumber"].ToString();
                        AccidentInfo.IsDealed = Convert.ToBoolean(dr["IsDealed"].ToString());
                    }
                }
                return AccidentInfo;
            }
            catch (Exception)
            {
                return null;
            }
        }       
        
    }
}
```

这里有个帖子说如果项目中需要考虑多线程并发和事务，则不能把数据库操作类设置成static，而是应该进行实例化来调用：

[不该活着的SqlHelper和DBHelper][1]


  [1]: http://www.cnblogs.com/cyq1162/p/5745325.html?utm_source=tuicool&utm_medium=referral#!comments

