```ebnf
coalesce = COALESCE "(" expr "," expr  {"," expr} ")".
```

COALESCE函数在多个[expr](../通用SQL语法/expr)的值中，查找到第一个不为空的expr值，并将其返回，未查找到时返回NULL。expr的数量范围为[1,65535]。

函数执行时依次对入参两两比较判断，比较规则与[IFNULL](IFNULL)相同。

示例（HEAP表）

```sql
-- numbers_nobit表中包含如下字段和数据
SELECT numbera,numberb,numberc,numberd,numbere,numberf,numberg FROM numbers_nobit;
     numbera numberb  numberc               numberd     numbere     numberf     numberg
------------ ------- -------- --------------------- ----------- ----------- -----------
          -5      55     5555   5555555555555555555  5.555E+000  5.556E+000         555
 
-- 按照三个expr中数据类型优先级最高的进行转换，numbera的值被转换为FLOAT类型
SELECT COALESCE(numbera,numberb,numbere) res FROM numbers_nobit;
        res 
----------- 
  -5.0E+000
```