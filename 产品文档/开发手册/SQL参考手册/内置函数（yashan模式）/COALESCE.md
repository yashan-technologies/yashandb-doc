```ebnf+diagram
coalesce::= COALESCE "(" expr "," (expr)  {"," (expr)} ")"
```

COALESCE函数在多个[expr](../通用SQL语法/expr)的值中，查找到第一个不为空的expr值，并将其返回，未查找到时返回NULL。expr的数量最少为2个，最多为100个。

expr值的数据类型可以为YashanDB支持的大多数类型，但同一个参数列表里所有expr值的数据类型必须处于下面同一个分类中，否则函数返回Inconsistent datatype错误：

*   数值型：按优先度从高到低为DOUBLE、FLOAT、NUMBER、BIGINT、INT、SMALLINT、TINYINT，但BIT类型只能与同类型在同一个参数列表中。
*   字符型：
    *   按优先度从高到低为VARCHAR、CHAR。
    *   按优先度从高到低为NVARCHAR、NCHAR。
*   日期时间型：按优先度从高到低为TIMESTAMP、DATE、TIME，但INTERVAL YEAR TO MONTH、INTERVAL DAY TO SECOND只能与同类型在同一个参数列表中。
*   布尔型：BOOLEAN
* CLOB
* BLOB
* NCLOB
* XMLTYPE


如所有expr值的数据类型处在同一分类中（字符型数据包含两个子分类），但具体类型不相同，函数将按上面所列优先级顺序进行类型转换，如`(expr1 NUMBER, expr2 SMALLINT,expr3 FLOAT)`时，expr1、expr2的数据类型将被转换成FLOAT，且函数返回一个FLOAT型数据。

 当同一个参数列表里的所有expr值均为NULL时，函数返回NULL。



示例

```sql
-- numbers_nobit表中包含如下字段和数据
SELECT numbera,numberb,numberc,numberd,numbere,numberf,numberg FROM numbers_nobit;
     NUMBERA NUMBERB  NUMBERC               NUMBERD     NUMBERE     NUMBERF     NUMBERG
------------ ------- -------- --------------------- ----------- ----------- -----------
          -5      55     5555   5555555555555555555  5.555E+000  5.556E+000         555
 
-- 按照三个expr中数据类型优先级最高的进行转换，numbera的值被转换为FLOAT类型
SELECT COALESCE(numbera,numberb,numbere) res FROM numbers_nobit;
        RES 
----------- 
  -5.0E+000

 
SELECT COALESCE('','',true) res FROM DUAL;
RES                  
-------------------- 
true    
```
