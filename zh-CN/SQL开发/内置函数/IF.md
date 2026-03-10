```ebnf+diagram
if::= IF "(" expr1 "," expr2 "," expr3 ")"
```

IF函数有3个[expr](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)参数，若expr1为TRUE，返回expr2，若expr1为FALSE，返回expr3。

expr1的值可以为任意数据类型，为NULL时视为FALSE，若不为NULL：

*   对于数值类型或可隐式转换为数值类型的其他类型，其值为非0时，视为TRUE，其值为0时，视为FALSE；
*   对于不可隐式转换为数值类型的类型，视为FALSE；
*   对于BOOLEAN类型和BOOLEAN表达式，按其真假处理。

expr2，expr3的值可以为任意数据类型。当expr2和expr3数据类型不相同时，函数将先进行隐式类型转换再返回结果，转换规则与[IFNULL](./IFNULL)中规则一致。

需注意，本函数如下规格与MySQL中的IF函数存在差异：

*   当expr2和expr3均为BIT类型时，YashanDB返回BIT类型，MySQL返回INT/BIGINT UNSIGNED类型；
*   当epxr2和epxr3为BOOLEAN、TINYINT或SMALLINT类型时，YashanDB按照BOOLEAN<TINYINT<SMALLINT的优先级进行类型转换，MySQL全部返回INT类型；

示例

```sql
SELECT IF(1, 2, 3) res FROM DUAL;
         RES 
------------ 
           2

SELECT IF(0, 2, 3) res FROM DUAL;
         RES 
------------ 
           3

SELECT IF(SYSDATE, TRUE, FALSE) res FROM DUAL;
RES                  
-------------------- 
false    

SELECT IF(TRUE, TRUE, FALSE) res FROM DUAL;
RES                  
-------------------- 
true 

SELECT IF(FALSE, TRUE, FALSE) res FROM DUAL;
RES                  
-------------------- 
false   

SELECT IF(2,5.44, CAST('0.232222' AS DOUBLE)) res,
TYPEOF (IF(2,5.44, CAST('0.232222' AS DOUBLE))) res_type
FROM DUAL;
        RES RES_TYPE                                                         
----------- ----------- 
  5.44E+000 double       
```
