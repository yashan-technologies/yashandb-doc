```ebnf
row_number = ROW_NUMBER "("")" OVER "(" [query_partition_clause] order_by_clause ")".
```

ROW_NUMBER is a [window function](00Built-in Functions.md#WindowFunction) with a syntax and constraints consistent with the [DENSE_RANK function](DENSE_RANK) and the [RANK function](RANK), differing in how it handles ties during sorting:

* This function does not list tied values but increments based on the result return without skipping numbers, such as 1, 2, 3, 4, 5, 6, 7, 8…
* The DENSE_RANK function lists tied values and does not skip numbers for the next ordering value, such as 1, 2, 3, 3, 3, 4, 5, 6…
* The RANK function lists tied values and skips numbers for the next ordering value, such as 1, 2, 3, 3, 3, 6, 7, 8…

The parameters for partition by and order by support data types other than LOB, JSON, and UDT.

***Example***

```sql
-- The sales_info_range table contains the following fields and data
SELECT year,month,branch,product,quantity,amount,salsperson FROM sales_info_range;
YEAR      MONTH     BRANCH    PRODUCT              QUANTITY      AMOUNT SALSPERSON
--------- --------- --------- ----------------- ----------- ----------- -------------------------
2001      01        0201      11001                      30         500 0201010011
2000      12        0102      11001                      20         300
2015      11        0101      11001                      20         300
2015      03        0102      11001                      20         300
2021      10        0101      11001                      20         300
2021      05        0101      11001                      40         600

-- Group by branch and sort within the group by amount
SELECT branch,amount,year,month,quantity,
		ROW_NUMBER() OVER (PARTITION BY branch ORDER BY amount) rownumber_res
FROM sales_info_range
WHERE product='11001'
ORDER BY branch,quantity;
BRANCH         AMOUNT YEAR      MONTH        QUANTITY         ROWNUMBER_RES
--------- ----------- --------- --------- ----------- ---------------------
0101              300 2015      11                 20                     1
0101              300 2021      10                 20                     2
0101              600 2021      05                 40                     3
0102              300 2000      12                 20                     1
0102              300 2015      03                 20                     2
0201              500 2001      01                 30                     1
```
