```ebnf+diagram
ratio_to_report::= RATIO_TO_REPORT "(" expr ")" OVER "(" [query_partition_clause]")"
```

RATIO_TO_REPORT为[窗口函数](00内置函数（yashan模式）.html#WindowFunction)，用于计算某个值相对于分组内所有值总和的比率。

本函数不支持向量化计算。

**expr**

需要计算比例的表达式，通常是某个列或者计算得出的值。

- expr可以为NUMBER、SMALLINT、INT、BIGINT、TINYINT数值类型以及浮点类型(FLOAT、DOUBLE)。
- expr可以为是其值能转换成数值类型的字符类型，如果字符类型的值不能转换成数值类型，则会报错。
- expr不能为日期类型（DATE、TIME、TIMESTAMP、INTERVAL YEAR TO MONTH、INTERVAL DAY TO SECOND）、RAW类型、布尔类型、BIT类型、BFILE类型、大对象类型(CLOB、BLOB、NCLOB)、ROWID/UROWID类型、JSON类型。
- expr可以为内置函数类型，但不能为窗口函数等分析类型函数。

**query_partition_clause**

该语句用于对数据进行分区计算，可省略。省略时，RATIO_TO_REPORT会对整个结果集进行计算。

窗口函数通用语法。

示例（HEAP表）

```sql
-- sales_info表数据准备
INSERT INTO sales_info VALUES ('2023','12','0102','10001',20,2000,'');
INSERT INTO sales_info VALUES ('2024','03','0102','10001',10,1000,'');
INSERT INTO sales_info VALUES ('2024','05','0101','10001',40,4000,'');
COMMIT;
SELECT product, branch, amount FROM sales_info;

PRODUCT           BRANCH         AMOUNT
----------------- --------- -----------
11001             0402              300
11001             0201              500
11001             0102              300
11001             0102              300
10001             0102             2000
10001             0102             1000
11001             0101              600
11001             0101              300
10001             0101             4000

-- 不进行分区计算
SELECT product, branch, amount, ROUND(RATIO_TO_REPORT(amount) OVER(), 8) AS sales_ratio FROM sales_info;

PRODUCT           BRANCH         AMOUNT SALES_RATIO
----------------- --------- ----------- -----------
11001             0402              300   .03225806
11001             0201              500   .05376344
11001             0102              300   .03225806
11001             0102              300   .03225806
10001             0102             2000   .21505376
10001             0102             1000   .10752688
11001             0101              600   .06451613
11001             0101              300   .03225806
10001             0101             4000   .43010753

-- 按branch对数据进行分区计算
SELECT product, branch, amount, ROUND(RATIO_TO_REPORT(amount) OVER(PARTITION BY branch), 8) AS sales_ratio FROM sales_info;

PRODUCT           BRANCH         AMOUNT SALES_RATIO
----------------- --------- ----------- -----------
11001             0101              600   .12244898
11001             0101              300   .06122449
10001             0101             4000   .81632653
11001             0102              300   .08333333
11001             0102              300   .08333333
10001             0102             2000   .55555556
10001             0102             1000   .27777778
11001             0201              500           1
11001             0402              300           1
```
