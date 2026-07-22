```ebnf
value = VALUE "(" correlation_variable ")".
```

VALUE函数用于提取表形式集合数据中的实际行值，适用于嵌套表UDT、XMLSEQUENCE函数返回值等集合数据。

**correlation_variable**

必须为table()表函数、返回值为嵌套表UDT的UDF的别名。

示例（HEAP表）

```sql
-- 获取XMLSEQUENCE()函数的返回值
select extractvalue(value(info),'/employee/name') as name,extractvalue(value(info),'/employee/department') as department
    FROM TABLE(XMLSEQUENCE(xmlEXTRACT(XMLType('<employee>
                         <name>John</name>
                          <department>Public Dep</department>
                       </employee>'),'/employee'))) info;

NAME                                                             DEPARTMENT                                             
---------------------------------------------------------------- ----------------------------------------------------------------
John                                                             Public Dep

-- 获取嵌套表UDT的值
CREATE OR REPLACE TYPE user_table_type IS TABLE OF CHAR(10);
/

select value(udt) from table(user_table_type ('123456')) udt;

VALUE(UDT)
----------------------------------------------------------------
123456
```
