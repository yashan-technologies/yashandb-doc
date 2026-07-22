```ebnf
lsfa_listagg = LSFA_LISTAGG "(" string ")".
```

LSFA\_LISTAGG函数将多行的数据执行拼接操作，并通过分隔符`,`进行分隔，返回一行VARCHAR类型的字符串。

> **Note**:
>
> 如需使用本函数，需确保已安装listagg插件（可通过查看$YASDB_HOME/third路径下是否存在listagg文件夹确认）。
>
> 若正常安装插件后仍无法使用本函数，请在$YASDB_HOME/plug-in/package/package.ini中添加一行`PACKAGE3 = {library = yspi_listagg, name = /, schema = public}`并重启数据库使之生效。

本函数遵循如下规则：

- 本函数仅适用于单机部署。

- 本函数不支持向量化计算。

- 本函数输出结果长度最长为65534，超过65534则报错。

- 本函数是聚合函数，但不支持DISTINCT和ALL。

**string**

string可以为：

- 通用表达式[expr](../通用SQL语法/expr)
- 查询列为单列且返回行为单行的子查询

string的值为字符型或可转换为字符型的其他类型。

在单行计算中，当string的值为NULL时，函数返回NULL。

在多行计算中，函数将忽略string值为空的行，当所有行均为空时，计算结果为NULL。存在多个拼接行时，会将多行的结果使用分隔符`,`分隔开来。

示例（单机HEAP表）

```sql
-- 创建exprs_listagg表，并插入数据
CREATE TABLE exprs_listagg (id INT,name VARCHAR(50),money FLOAT);
INSERT INTO exprs_listagg 
VALUES  (1,'Dong',10000),(2,'Ming',46450),
        (3,'Hong',46450 ),(4,'Dong',14465),
        (5,'Ming',46450),(6,'Dong',46450);

-- 未指定GROUP BY时，将所有行拼接，得到一行结果
SELECT LSFA_LISTAGG(MONEY) AS money FROM exprs_listagg;
MONEY
---------------------------------------------------------------- 
4.645E+004,4.645E+004,1.4465E+004,4.645E+004,4.645E+004,1.0E+004

-- group by后各组的多行数据分别拼接成一行，得到按组的多行结果
SELECT NAME,LSFA_LISTAGG(MONEY) AS money FROM exprs_listagg GROUP BY name;
NAME                                                  MONEY                                                            
----------------------------------------------------- ---------------------------------------------------------------- 
Dong                                                  4.645E+004,1.4465E+004,1.0E+004                                 
Ming                                                  4.645E+004,4.645E+004                                           
Hong                                                  4.645E+004
```
