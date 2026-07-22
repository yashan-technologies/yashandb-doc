```ebnf
dense_rank = DENSE_RANK "("")" OVER "(" [query_partition_clause] order_by_clause ")".
```

DENSE_RANK is a [window function](00Built-in Functions.md#WindowFunction) used for real-time analysis of data.

This function does not support vectorization calculation.

The syntax description and constraints of this function are consistent with those of the [RANK function](RANK) and [ROW_NUMBER function](ROW_NUMBER), the difference lies in the treatment of tied values during sorting:

- This function lists the same tied values and does not skip numbers for the next sequential value, for example, 1, 2, 3, 3, 3, 4, 5, 6...
- RANK function lists the same tied values and skips numbers for the next sequential value, for example, 1, 2, 3, 3, 3, 6, 7, 8...
- ROW_NUMBER function does not list tied values but increments based on the returned results without skipping numbers, for example, 1, 2, 3, 4, 5, 6, 7, 8...

The parameters for partition by and order by support all data types except LOB, JSON, and UDT.

**query\_partition\_clause**|**order\_by\_clause**

General syntax for window functions.

***Example*** for Heap tables

```sql
-- The sales_info_range table contains the following fields and data
SELECT year,month,branch,product,quantity,amount,salsperson FROM sales_info_range;
YEAR  MONTH BRANCH PRODUCT      QUANTITY      AMOUNT SALSPERSON
----- ----- ------ --------- ----------- ----------- -------------
2001  01    0101   11001              30         500 0201010011
2000  12    0102   11001              20         300
2015  11    0101   11001              20         300
2015  03    0102   11001              20         300
2021  10    0101   11001              20         300
2021  05    0101   11001              40         600

-- Group by branch and sort by amount within the groups
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
