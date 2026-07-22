```ebnf
dense_rank = DENSE_RANK "("")" OVER "(" [query_partition_clause] order_by_clause ")".
```

DENSE_RANK为[窗口函数](00内置函数.md#WindowFunction)，用于对数据的实时分析。

本函数不支持向量化计算。

本函数的语法描述及约束与[RANK函数](./RANK)，[ROW\_NUMBER函数](./ROW_NUMBER)一致，区别在于排序时对并列值的处理：

- 本函数列出相同并列值，并对下一顺序值不跳号，例如1，2，3，3，3，4，5，6……
- RANK函数列出相同并列值，并对下一顺序值跳号，例如1，2，3，3，3，6，7，8……
- ROW_NUMBER函数不列出并列值，而是根据返回的结果递增，且不跳号，例如1，2，3，4，5，6，7，8……

partition by与order by的参数支持除LOB、JSON、UDT外的其它数据类型。

**query\_partition\_clause**|**order\_by\_clause**

窗口函数通用语法。

示例（HEAP表）

```sql
-- sales_info_range表中包含如下字段和数据
SELECT year,month,branch,product,quantity,amount,salsperson FROM sales_info_range;
YEAR  MONTH BRANCH PRODUCT      QUANTITY      AMOUNT SALSPERSON
----- ----- ------ --------- ----------- ----------- -------------
2001  01    0101   11001              30         500 0201010011
2000  12    0102   11001              20         300
2015  11    0101   11001              20         300
2015  03    0102   11001              20         300
2021  10    0101   11001              20         300
2021  05    0101   11001              40         600

-- 按branch进行分组，并在组内按amount进行排序
SELECT branch,amount,year,month,quantity,
		DENSE_RANK() OVER (PARTITION BY branch ORDER BY amount) denserank_res
FROM sales_info
WHERE product='11001'
ORDER BY branch,quantity;
BRANCH      AMOUNT YEAR  MONTH    QUANTITY         DENSERANK_RES
------ ----------- ----- ----- ----------- ---------------------
0101           300 2015  11             20                     1
0101           600 2021  05             40                     2
0102           300 2015  03             20                     1
0102           300 2000  12             20                     1
0201           500 2001  01             30                     1
0402           300 2021  10             20                     1
```
