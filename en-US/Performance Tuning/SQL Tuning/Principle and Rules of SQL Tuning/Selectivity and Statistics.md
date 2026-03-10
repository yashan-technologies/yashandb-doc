This section will describe the principle of selectivity, the relationship between statistics and selectivity, as well as relevant tuning assessments. In the optimization of complex joins, the impact of selectivity on execution plans can be understood through specific examples.

## Selectivity

Generally, filters are associated with operators. Based on whether it is a join, filters can be divided into join conditions and filtering conditions (also known as join condition and filter for distinction).

Join conditions refer to the conditions on the ON clause or the WHERE clause during implicit inner joins (as in the following two query statements); others can be regarded as filtering conditions.

```sql
SELECT * FROM area INNER JOIN branches ON area.area_no = branches.area_no;
SELECT * FROM area, branches WHERE area.area_no = branches.area_no;
```

Selectivity is the ratio of the number of data entries filtered by the filter to the original data count. Suppose there are a total of 100 data entries in the table, and the filter outputs 10 entries; the selectivity would be 1/10. 

Selectivity directly affects the estimated number of rows. In the Cost Model, the number of rows is a fairly important evaluation factor. Hence, selectivity significantly influences the final cost estimation indirectly.

## Statistics

The basis for selectivity calculation is the collected statistics. The collection and accuracy of statistics significantly impact the accuracy of selectivity calculations.

For information related to YashanDB statistics, please refer to [statistics](../../Performance Tuning Features and Tools/Statistics).

For SQL tuning, it is essential to ensure the timeliness of statistics. By querying views and executing statements, one can assess whether the statistics have been accurately collected.

The following example will illustrate how to determine whether the maximum/minimum value statistics for a column are accurate, and other statistics can be assessed in a similar manner.

***Example***

```sql
SELECT HIGH_VALUE FROM DBA_TAB_COL_STATISTICS WHERE table_name = 'AREA' AND column_name = 'AREA_NO';
HIGH_VALUE
----------------------------
3035


SELECT MAX(area_no) FROM area;
MAX(AREA_NO)
------------
05

         
SELECT LOW_VALUE FROM DBA_TAB_COL_STATISTICS WHERE table_name = 'AREA' AND column_name = 'AREA_NO';

LOW_VALUE
--------------------------
3031


SELECT MIN(area_no) FROM area;
MIN(AREA_NO)
------------
01

```
