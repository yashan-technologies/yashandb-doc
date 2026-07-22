```ebnf
value = VALUE "(" correlation_variable ")".
```

The VALUE function is used to extract the actual row values from table-form collection data, applicable to collection data such as nested table UDTs and XMLSEQUENCE function return values.

**correlation_variable**

Must be an alias for the table() table function or a UDF whose return value is a nested table UDT.

***Example*** for Heap tables

```sql
-- Get the return value of XMLSEQUENCE() function
select extractvalue(value(info),'/employee/name') as name,extractvalue(value(info),'/employee/department') as department
    FROM TABLE(XMLSEQUENCE(xmlEXTRACT(XMLType('<employee>
                         <name>John</name>
                          <department>Public Dep</department>
                       </employee>'),'/employee'))) info;

NAME                                                             DEPARTMENT                                             
---------------------------------------------------------------- ----------------------------------------------------------------
John                                                             Public Dep

-- Get the value of a nested table UDT
CREATE OR REPLACE TYPE user_table_type IS TABLE OF CHAR(10);
/

select value(udt) from table(user_table_type ('123456')) udt;

VALUE(UDT)
----------------------------------------------------------------
123456
```
