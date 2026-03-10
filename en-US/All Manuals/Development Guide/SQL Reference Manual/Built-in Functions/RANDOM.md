```ebnf+diagram
random::= RANDOM "("")"
```

The RANDOM function returns a random number between 0 and 1, with a return type of NUMBER. You can use `ORDER BY RANDOM()` to randomly sort a set of records.

It is not recommended to sort by the RANDOM function. When the RANDOM function is used as both a sorting key and a projection column, the output results remain unordered.

It is also not advisable to compare the RANDOM function, as it will be executed multiple times, making the comparison results meaningless.

***Example*** for Heap tables

```sql
SELECT RANDOM() res FROM DUAL;
        RES
-----------
 .936900865
 
SELECT branch,department,employee_no,employee_name,sex,entry_date
FROM employees
ORDER BY RANDOM();
BRANCH DEPARTMENT EMPLOYEE_NO   EMPLOYEE_NAME SEX   ENTRY_DATE                       
------ ---------- ------------- ------------- ----- -------------------------------- 
0101   008        0201008004    Jim           1     2021-06-19 22:55:32                   
0101   000        0101000001    Mask          1     2019-04-11 22:55:32                   
0201   010        0201010011    Anna          0     2021-03-11 22:55:32                   
0201   008        0201008003    Jack          1     2020-02-05 22:55:32                   
0101   000        0101000002    John          1     2016-07-15 22:55:32

-- When the RANDOM function is used as both a sorting key and a projection column, the output results remain unordered.
SELECT RANDOM() res FROM employees ORDER BY 1;
        RES
-----------
 .612940056
 .369912063
 .998551899
 .992730169
  .62891736

-- When comparing the RANDOM function, the comparison results are meaningless.
SELECT RANDOM() res FROM employees WHERE RANDOM() > 0.5;
        RES
-----------
 .556500692
 .336096448

SELECT RANDOM() = RANDOM() res FROM employees;
RES
--------------------
false
false
false
false
false
```
