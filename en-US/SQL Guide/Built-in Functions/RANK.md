```ebnf+diagram
rank::= RANK "("")" OVER "(" [query_partition_clause] order_by_clause ")"
```

RANK is a [window function](../../All Manuals/Development Guide/SQL Reference Manual/Built-in Functions (yashan Mode)/00Built-in Functions (yashan Mode).html#WindowFunction) used for real-time data analysis.

The syntax description and constraints of this function are the same as those of the [DENSE_RANK function](DENSE_RANK) and [ROW_NUMBER function](ROW_NUMBER), with the distinction in handling of ties during sorting:

- This function lists the same tied values and skips the next sequential value, such as 1, 2, 3, 3, 3, 6, 7, 8…
- The DENSE_RANK function lists the same tied values and does not skip the next sequential value, such as 1, 2, 3, 3, 3, 4, 5, 6…
- The ROW_NUMBER function does not list tied values but increases according to the returned result without skipping, such as 1, 2, 3, 4, 5, 6, 7, 8…

**query_partition_clause**|**order_by_clause**

The parameters for partition by and order by support data types other than LOB, JSON, and UDT.

Common syntax for window functions.

***Example***

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

-- Group by branch and sort by amount within each group
SELECT branch,amount,year,month,quantity,
		RANK() OVER (PARTITION BY branch ORDER BY amount) rank_res
FROM sales_info_range
WHERE product='11001'
ORDER BY branch,quantity;
BRANCH      AMOUNT YEAR  MONTH    QUANTITY              RANK_RES
------ ----------- ----- ----- ----------- ---------------------
0101           300 2021  10             20                     1
0101           300 2015  11             20                     1
0101           500 2001  01             30                     3
0101           600 2021  05             40                     4
0102           300 2015  03             20                     1
0102           300 2000  12             20                     1
```
