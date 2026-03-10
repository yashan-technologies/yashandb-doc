```ebnf+diagram
random::= RANDOM "("")"
```

RANDOM函数返回一个0到1之间的随机数，返回类型为NUMBER型。可以使用`ORDER BY RANDOM()`来对一组记录进行随机化排序。

不建议对RANDOM函数进行排序，当RANDOM函数同时作为排序键和投影列时，输出结果仍旧无序。

不建议对RANDOM函数进行比较，RANDOM函数会执行多次，比较结果无意义。

示例（HEAP表）

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

-- 当RANDOM函数同时作为排序键和投影列时，输出结果仍旧无序。
SELECT RANDOM() res FROM employees ORDER BY 1;
        RES
-----------
 .612940056
 .369912063
 .998551899
 .992730169
  .62891736

-- 当RANDOM函数进行比较时，比较结果无意义。
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
