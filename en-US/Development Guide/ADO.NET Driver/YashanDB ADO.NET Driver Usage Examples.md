***Example***: Application development based on ADO.NET.

```c#
//Examples.cs
//Demonstrates the main steps in application development based on ADO.NET, including creating a database, creating tables, inserting data, etc.
using System;
using System.Data;
using System.Data.Common;
using Yashandb.Data.YashandbClient;

namespace Examples
{
    public class Program
    {
        public static void dropTable(DbCommand command, string name)
        {
            command.CommandText = string.Format("drop table if exists {0}", name);
            command.ExecuteNonQuery();
            command.Dispose();
        }

        public static void Print(DbDataReader reader)
        {
            while (reader.Read())
            {
                for (int i = 0; i < reader.FieldCount; i++)
                {
                    var t = reader.GetDataTypeName(i);

                    if (t == "YAC_TYPE_BLOB" || t == "YAC_TYPE_BINARY")
                    {
                        byte[] bytes = (byte[])reader[i];
                        for (int j = 0; j < bytes.Length; j++)
                            Console.Write("{0}-", bytes[j]);
                        Console.WriteLine(string.Format(": {0}", t));
                        Console.WriteLine(string.Format("to string: {0} ", System.Text.Encoding.UTF8.GetString(bytes)));
                    }
                    else
                    {
                        if (reader.IsDBNull(i))
                            Console.WriteLine(string.Format("null: {0} ", t));
                        else
                            Console.WriteLine(string.Format("{0}: {1} ", reader[i], t));
                    }

                }
                Console.WriteLine();
            }
        }

        public static void Read(DbCommand command, string table)
        {
            Console.WriteLine("read");
            command.CommandText = string.Format("select * from {0}", table);
            var reader = command.ExecuteReader();
            Print(reader);
            reader.Dispose();
        }
        public static void TestNum(DbConnection connection, string table)
        {
            var command = (YasdbCommand)connection.CreateCommand();

            dropTable(command, table);
            command.CommandText = string.Format("create table {0}(a int, b float, c double, d number, e tinyint)", table);
            command.ExecuteNonQuery();
            command.Dispose();

            command.CommandText = string.Format("insert into {0} values(?, ?, ?, ?, ?)", table);
            command.Parameters.Add(20);
            command.Parameters.Add(3.1);
            command.Parameters.Add(4.14);
            command.Parameters.Add(Decimal.One / (decimal)3 * (decimal)3);
            command.Parameters.Add(false);
            command.ExecuteNonQuery();
            command.Dispose();

            Read(command, table);
            command.Dispose();
        }

        public static void TestDate(DbConnection connection, string table)
        {
            var command = (YasdbCommand)connection.CreateCommand();

            dropTable(command, table);
            command.CommandText = string.Format("create table {0}(a date, b timestamp)", table);
            command.ExecuteNonQuery();
            command.Dispose();

            command.CommandText = string.Format("insert into {0} values(?, ?)", table);
            command.Parameters.Add(DateTime.Now);
            command.Parameters.Add(DateTime.Now);

            command.ExecuteNonQuery();
            command.Dispose();

            Read(command, table);
            command.Dispose();
        }

        public static void TestTimeOffset(DbConnection connection)
        {
            var command = (YasdbCommand)connection.CreateCommand();
            string sql = "select EXTRACT(DAY FROM (sysdate -startup_time)) *60 * 60 * 24 +";
            sql += "EXTRACT(HOUR FROM(sysdate - startup_time)) * 60 * 60 +";
            sql += "EXTRACT(MINUTE FROM(sysdate - startup_time)) * 60 + ";
            sql += "EXTRACT(SECOND FROM(sysdate - startup_time)) AS uptime from v$instance";
            command.CommandText = sql;

            var reader = command.ExecuteReader();
            reader.Read();
            for (int i = 0; i < reader.FieldCount; i++)
                Console.WriteLine(string.Format("{0}: {1} ", reader[i], reader.GetDataTypeName(i)));

            command.Dispose();
        }

        public static void TestClob(DbConnection connection, string table)
        {
            var command = (YasdbCommand)connection.CreateCommand();

            dropTable(command, table);
            command.CommandText = string.Format("create table {0}(a int, b clob, c varchar(64), d blob )", table);
            command.ExecuteNonQuery();
            command.Dispose();

            command.CommandText = string.Format("insert into {0} values(?, ?, ?, ?)", table);
            command.Parameters.Add(1);
            command.Parameters.Add("Chinese");
            command.Parameters.Add("add Chinese");
            command.Parameters.Add("add");
            command.ExecuteNonQuery();
            command.Dispose();

            Read(command, table);
            command.Dispose();

            command.CommandText = string.Format("select * from {0} where c = ?", table);
            command.Parameters.Add("add Chinese");
            var reader = command.ExecuteReader();
            reader.Read();
            for (int i = 0; i < reader.FieldCount; i++)
                Console.WriteLine(string.Format("{0}: {1} ", reader[i], reader.GetDataTypeName(i)));
            reader.Dispose();
            command.Dispose();
        }

        public static void TestParam(DbConnection connection, string table)
        {
            var command = (YasdbCommand)connection.CreateCommand();

            dropTable(command, table);
            command.CommandText = string.Format("create table {0}(a int, b clob, c varchar(64), d varchar(32) )", table);
            command.ExecuteNonQuery();
            command.Dispose();

            command.CommandText = string.Format("insert into {0} (a,b,c,d) values(?, ?, ?, ?)", table);
            command.Parameters.Add(1);
            command.Parameters.Add("Chinese2");
            command.Parameters.Add("Chinese3");
            command.Parameters.Add("abc4");
            command.ExecuteNonQuery();
            command.Dispose();

            Read(command, table);
            command.Dispose();
        }

        public static void TestBytes(DbConnection connection, string table)
        {
            var command = (YasdbCommand)connection.CreateCommand();
            dropTable(command, table);

            command.CommandText = string.Format("create table {0}(a int, b blob)", table);
            command.ExecuteNonQuery();
            command.Dispose();

            command.CommandText = string.Format("insert into {0} values(?, ?)", table);
            command.Parameters.Add(1);
            command.Parameters.Add(new byte[] { 1, 2, 3, 4 });
            command.ExecuteNonQuery();
            command.Dispose();

            Read(command, table);
            command.Dispose();

            command.CommandText = string.Format("select * from {0};", table);
            var reader = command.ExecuteReader();
            reader.Read();
            byte[] a = (byte[])reader[1];
            Console.WriteLine(string.Format("{0}: {1} ", a[2], reader.GetDataTypeName(1)));
            command.Dispose();
        }

        public static void TestBindName(DbConnection connection, string table)
        {

            var command = (YasdbCommand)connection.CreateCommand();
            dropTable(command, table);

            command.CommandText = string.Format("create table {0}(a int, b varchar(32), c clob, d blob)", table);
            command.ExecuteNonQuery();
            command.Dispose();

            command.CommandText = string.Format("insert into {0} (a, c, b, d) values(@a, @c, @b, @d)", table);
            command.Parameters.Add("@a", 1);
            command.Parameters.Add("@d", new byte[] { 1, 2, 3, 4 });
            command.Parameters.Add("@c", "Flower Dancing");
            command.Parameters.Add("@b", "1234");
            command.ExecuteNonQuery();
            command.Dispose();

            Read(command, table);
            command.Dispose();
        }

        public static void TestStream(DbConnection connection, string table)
        {
            var command = (YasdbCommand)connection.CreateCommand();

            command.CommandText = string.Format("drop table if exists {0}", table);
            command.ExecuteNonQuery();
            command.Dispose();

            command.CommandText = string.Format("create table {0}(a int, b blob)", table);
            command.ExecuteNonQuery();
            command.Dispose();

            command.CommandText = string.Format("insert into {0} (b) values(@b)", table);

            var s = new System.IO.MemoryStream(new byte[] { 1, 2, 3, 4 });
            command.Parameters.Add("@b", s);
            command.ExecuteNonQuery();
            command.Dispose();

            Read(command, table);
            command.Dispose();
        }

        public static void TestBlob(DbConnection connection, string table)
        {
            var command = (YasdbCommand)connection.CreateCommand();

            command.CommandText = string.Format("drop table if exists {0}", table);
            command.ExecuteNonQuery();
            command.Dispose();

            command.CommandText = string.Format("create table {0}(a int, b blob, c clob)", table);
            command.ExecuteNonQuery();
            command.Dispose();

            command.CommandText = string.Format("insert into {0} (b) values(@b)", table);

            // var s = new System.IO.MemoryStream(new byte[] { 1, 2, 3, 4 });
            command.Parameters.Add("@b", new byte[] { 1, 2, 3, 4 });
            command.ExecuteNonQuery();
            command.Dispose();

            Read(command, table);
            command.Dispose();
        }

        public static void TestTranslation(DbConnection connection)
        {
            var command = (YasdbCommand)connection.CreateCommand();
            command.CommandText = "SELECT 1 FROM DUAL";

            using (connection.BeginTransaction())
            {
                using (command.ExecuteReader()) { }
            }

            command.Dispose();
        }

        public static void TestLastId(DbConnection connection, string table)
        {
            var command = (YasdbCommand)connection.CreateCommand();
            try
            {
                command.CommandText = string.Format("create sequence seq_{0} start with 1 increment by 1", table);
                command.ExecuteNonQuery();
            }
            catch
            {
                command.Dispose();
            }
            dropTable(command, table);
            command.CommandText = string.Format("create table {0} (id number default seq_{0}.nextval, name varchar(256))", table);
            command.ExecuteNonQuery();

            command.CommandText = string.Format("insert into {0} (id, name) values(@id, @name)", table);
            command.Parameters.Add("@id", DBNull.Value);
            command.Parameters["@id"].DbType = DbType.Decimal;
            command.Parameters.Add("@name", "abc");
            command.ExecuteNonQuery();
            Console.WriteLine("last insert id: " + command.LastInsertId);

            command.CommandText = string.Format("insert into {0} (name) values(@name) RETURNING ID INTO @id", table);
            command.Parameters.Add("@name", "abc");
            command.Parameters.Add("@id", 1);
            command.ExecuteNonQuery();
            Console.WriteLine("last insert id: " + command.LastInsertId);

            command.CommandText = string.Format("insert into {0} (name) values(@name) RETURNING ID INTO @id", table);
            command.Parameters.Add("@name", "def");
            long lastid = (long)command.ExecuteScalar();
            Console.WriteLine("last insert id: " + lastid);

            command.CommandText = string.Format("insert into {0} (name) values(@name) RETURNING ID INTO @id", table);
            command.Parameters.Add("@name", "ghi");
            command.Parameters.Add("@id", 1);
            var reader = command.ExecuteReader();
            reader.Read();
            Console.WriteLine("last insert id: " + reader[0]);

            command.Dispose();

            command.CommandText = $"select seq_{table}.nextval from dual;";
            var a = command.ExecuteScalar();
            Console.WriteLine(a);

            command.Dispose();
        }

        public static void TestProcedureInput(DbConnection connection, string table)
        {
            var command = (YasdbCommand)connection.CreateCommand();
            dropTable(command, table);
            command.CommandText = $"create table {table} (ID number, NAME varchar2(10), SEX varchar2(4), AGE number, ADDRESS varchar2(200));";
            command.ExecuteNonQuery();

            command.CommandText = $"create or replace procedure proc1 is begin insert into {table}(ID, NAME, SEX, AGE) values (1, 'moses', 'man', 25); commit; end;";
            command.ExecuteNonQuery();
            command.CommandType = CommandType.StoredProcedure;
            command.CommandText = "proc1";
            command.ExecuteNonQuery();

            command.CommandText = $@"
            create or replace procedure proc2(v_id number, v_name varchar2, v_sex varchar2, v_age number)
            is begin insert into {table}(id, name, sex, age) values(v_id, v_name, v_sex, v_age);
            commit; end;";
            command.ExecuteNonQuery();

            command.CommandText = "proc2";
            command.CommandType = CommandType.StoredProcedure;
            command.Parameters.Add("@v_id", 1);
            command.Parameters.Add("@v_name", "aa");
            command.Parameters.Add("@v_sex", "man");
            command.Parameters.Add("@v_age", 20);
            command.ExecuteNonQuery();
            command.Dispose();
        }

        public static void TestProcedureOutput(DbConnection connection, string table)
        {
            var command = (YasdbCommand)connection.CreateCommand();
            command.CommandText = $@"
            create or replace procedure proc3 (recount out number)
            is  begin select  count(*)  into recount from {table}; commit; end;";
            command.ExecuteNonQuery();

            command.CommandText = "proc3";
            command.CommandType = CommandType.StoredProcedure;
            command.Parameters.Add("@recount", ParameterDirection.Output, DbType.Decimal);
            command.ExecuteReader();

            Console.WriteLine($"recount:{command.Parameters["@recount"].Value.ToString()}");
            command.Dispose();
        }

        public static void TestSpecicalChar(DbConnection connection)
        {
            var command = (YasdbCommand)connection.CreateCommand();
            command.CommandText = "SELECT '😀' from dual;";
            var reader = command.ExecuteReader();
            // Print(reader);
            reader.Read();
            if ((string)reader[0] != "😀")
                throw new Exception("no equal");
            else
                Console.WriteLine("equal");
            command.Dispose();
        }

        public static void TestTimeSpan(DbConnection connection, string table)
        {
            var command = (YasdbCommand)connection.CreateCommand();

            dropTable(command, table);
            command.CommandText = $"create table {table} (ID number, dayspan  INTERVAL DAY(9) TO SECOND(6), yearspan INTERVAL YEAR(9) TO MONTH);";
            command.ExecuteNonQuery();

            command.CommandText = $"insert into {table} values(1, '-00 23:59:59', '-1-10');";
            command.ExecuteNonQuery();

            command.CommandText = $"insert into {table} values(2, '09 23:59:59', '3-09');";
            command.ExecuteNonQuery();

            command.CommandText = $"SELECT * FROM {table};";
            var reader = command.ExecuteReader();
            Print(reader);
            command.Dispose();
        }

        public static void TestInsertNull(DbConnection connection, string table)
        {
            var command = (YasdbCommand)connection.CreateCommand();

            dropTable(command, table);
            command.CommandText = $"create table {table} (ID int, name varchar(32));";
            command.ExecuteNonQuery();

            command.CommandText = $"insert into {table} values(@id, @name);";
            command.Parameters.Add("@id", System.DBNull.Value);
            command.Parameters["@id"].DbType = DbType.Int32;
            command.Parameters.Add("@name", "aaa");
            command.ExecuteNonQuery();

            command.CommandText = $"insert into {table} values(@id, @name);";
            command.Parameters.Add("@id", 1);
            command.Parameters.Add("@name", System.DBNull.Value);
            command.Parameters["@name"].DbType = DbType.String;
            command.ExecuteNonQuery();

            command.CommandText = $"SELECT * FROM {table};";
            var reader = command.ExecuteReader();
            Print(reader);
            command.Dispose();
        }

        public static void TestBaseMultiQuery(DbConnection connection)
        {
            var command = (YasdbCommand)connection.CreateCommand();
            command.CommandText = @"
                select 1 from dual;
                select 3 from dual;
                select 99 from dual;
            ";
            var reader = command.ExecuteReader();
            while (reader.Read())
            {
                Console.WriteLine(reader.GetInt64(0));
            }
            while (reader.NextResult())
            {
                while (reader.Read())
                {
                    Console.WriteLine(reader.GetInt64(0));
                }
            }
            Console.WriteLine("no next result");
            reader.Close();
            command.Dispose();
        }

        public static void TestMultiBind(DbConnection connection, string table)
        {

            var command = (YasdbCommand)connection.CreateCommand();
            dropTable(command, table);

            command.CommandText = $"create table {table}(a int, b varchar(32))";
            command.ExecuteNonQuery();
            command.Dispose();

            command.CommandText = $"insert into {table} (a, b) values(1, 'One——');";
            command.ExecuteNonQuery();
            command.CommandText = $"insert into {table} (a, b) values(2, 'Two——');";
            command.ExecuteNonQuery();
            command.CommandText = $"insert into {table} (a, b) values(3, 'Three——');";
            command.ExecuteNonQuery();
            command.CommandText = $"insert into {table} (a, b) values(4, 'Four——');";
            command.ExecuteNonQuery();
            command.CommandText = $"insert into {table} (a, b) values(6, 'Six——');";
            command.ExecuteNonQuery();
            command.Dispose();

            command.CommandText = $@"
                select a, b from {table};
                select a, b from {table} where a=@a;
                select a, b from {table} where a>@a;
                select a, b from {table} where b = @b;
                select a, b from {table} where b != @b and a= @a;
            ";
            command.Parameters.Add("@a", 3);
            command.Parameters.Add("@b", "Two");
            var reader = command.ExecuteReader(CommandBehavior.SequentialAccess);
            while (reader.Read())
            {
                Console.WriteLine($"----> {reader[0]}, {reader[1]}");
            }
            while (reader.NextResult())
            {
                while (reader.Read())
                {
                    Console.WriteLine($"----> {reader[0]}, {reader[1]}");
                }
            }
            command.Dispose();
        }

       public static void TestMultiNames(DbConnection connection, string table)
        {
            var command = (YasdbCommand)connection.CreateCommand();
            dropTable(command, table);

            command.CommandText = $"create table {table}(a int, b varchar(32), c varchar(32))";
            command.ExecuteNonQuery();
            command.Dispose();

            command.CommandText = $"insert into {table} (a, b) values(1, 'Seven——');";
            command.ExecuteNonQuery();
            command.CommandText = $"insert into {table} (a, b, c) values(1, 'Two——', 'Two——');";
            command.ExecuteNonQuery();
            command.CommandText = $"insert into {table} (a, b, c) values(2, 'Eight——', 'Two——');";
            command.ExecuteNonQuery();

            command.CommandText = $"select a, b, c from {table} where b=@b and c=@b";
            command.Parameters.Add("@b", "Two——");
            var reader = command.ExecuteReader(CommandBehavior.SequentialAccess);
            while (reader.Read())
            {
                Console.WriteLine($"----> {reader[0]}, {reader[1]}, {reader[2]}");
            }
            command.Dispose();

            command.CommandText = @$"select * from (
                select* from test_multinames where a = @a
                union all
                select* from test_multinames where a = @a and b = @b
            )";

            command.Parameters.Add("@a", 1);
            command.Parameters.Add("@b", "Seven");
            reader = command.ExecuteReader(CommandBehavior.SequentialAccess);
            while (reader.Read())
            {
                Console.WriteLine($"----> {reader[0]}, {reader[1]}, {reader[2]}");
            }
            command.Dispose();
        }
        public static void Main(string[] args)
        {
            var connection = new YasdbConnection("Data Source=192.168.31.139:1688;User ID=sys;Password=yasdb_123");
            connection.Open();

            TestNum(connection, "test_int");
            TestDate(connection, "test_date");
            TestTimeOffset(connection);
            TestClob(connection, "test_clob");
            TestParam(connection, "test_param");
            TestBytes(connection, "test_bytes");
            TestBindName(connection, "test_bindname");
            TestBlob(connection, "csharp_test_parameter");
            TestStream(connection, "test_stream");
            
            TestTranslation(connection);
            TestLastId(connection, "test_lastinsertid");
            TestProcedureInput(connection, "test_procedure");
            TestProcedureOutput(connection, "test_procedure");
            TestSpecicalChar(connection);
            TestTimeSpan(connection, "test_timespan");
            TestInsertNull(connection, "test_null");
            TestBaseMultiQuery(connection);
            TestMultiBind(connection, "test_multibind");
            TestMultiNames(connection, "test_multinames");
            
            connection.Dispose();

            Console.WriteLine("Done.");
            Console.ReadKey();
        }
    }
}
```

Run the application in a Linux environment:

```shell
dotnet run Examples.csproj
```
