```ebnf+diagram
ifnull::= IFNULL "(" expr1 "," expr2 ")"
```

IFNULL函数有2个[expr](../通用SQL语法/expr)参数，当expr1不为NULL时返回expr1，否则返回expr2。

expr1、expr2的值可以为任意数据类型，但当expr1、expr2其中之一为RAW、JSON、CLOB、NCLOB或XMLTYPE类型时，不允许与其它类型搭配使用（即expr1和expr2的数据类型必须相同），否则函数返回错误。

函数返回值类型规则如下：

- 当expr1、expr2其中之一为BLOB时，函数返回值为BLOB类型。

- 当expr1、expr2其中之一为NULL，另一个为常量时，函数返回值类型为常量对应的数据类型。

- 当expr1和expr2的数据类型相同时，函数返回此数据类型的值。若expr1和expr2均属于字符型，函数返回VARCHAR类型。

- 当expr1和expr2的数据类型不相同，函数将先进行隐式类型转换后再返回结果，基本规则如下：

  -  expr1与expr2分属于数值型、日期时间型。字符型或ROWID不同大类组时，函数返回值为VARCHAR类型。

  - expr1与expr2其中之一为布尔型：

    - 另一个属于非数值型的其他大类时，函数返回VARCHAR类型。

    - 另一个属于数值型（除BIT外）时函数返回对应的数值类型，另一个为BIT类型时函数返回BIGINT类型。

- expr1与expr2均属于日期时间型时，函数返回类型如下表所示：（将TIME类型转换为DATE或TIMESTAMP类型时年月日补充为当前日期）

| expr1/expr2                          | DATE      | TIME      | TIMESTAMP | INTERVAL<br/> YEAR TO <br/>MONTH | INTERVAL <br/>DAY TO <br/>SECOND |
| ------------------------------------ | --------- | --------- | --------- | -------------------------------- | -------------------------------- |
| **DATE**                             | DATE      | DATE      | TIMESTAMP | VARCHAR                          | VARCHAR                          |
| **TIME**                             | DATE      | TIME      | TIMESTAMP | VARCHAR                          | VARCHAR                          |
| **TIMESTAMP**                        | TIMESTAMP | TIMESTAMP | TIMESTAMP | VARCHAR                          | VARCHAR                          |
| **INTERVAL <br/>YEAR TO <br/>MONTH** | VARCHAR   | VARCHAR   | VARCHAR   | INTERVAL <br/>YEAR TO <br/>MONTH | VARCHAR                          |
| **INTERVAL <br/>DAY TO <br/>SECOND** | VARCHAR   | VARCHAR   | VARCHAR   | VARCHAR                          | INTERVAL <br/>DAY TO <br/>SECOND |

- expr1与expr2均属于数值型时，函数返回类型如下表所示：（仅当expr1与expr2均为NUMBER类型且精度相同时，函数返回同精度的NUMBER类型）

| expr1/expr2  | BIT    | TINYINT  | SMALLINT | INT    | BIGINT | FLOAT  | DOUBLE | NUMBER      |
| ------------ | ------ | -------- | -------- | ------ | ------ | ------ | ------ | ----------- |
| **BIT**      | BIT    | BIGINT   | BIGINT   | BIGINT | BIGINT | DOUBLE | DOUBLE | NUMBER      |
| **TINYINT**  | BIGINT | TINYINT  | SMALLINT | INT    | BIGINT | FLOAT  | DOUBLE | NUMBER      |
| **SMALLINT** | BIGINT | SMALLINT | SMALLINT | INT    | BIGINT | FLOAT  | DOUBLE | NUMBER      |
| **INT**      | BIGINT | INT      | INT      | INT    | BIGINT | FLOAT  | DOUBLE | NUMBER      |
| **BIGINT**   | BIGINT | BIGINT   | BIGINT   | BIGINT | BIGINT | FLOAT  | DOUBLE | NUMBER      |
| **FLOAT**    | DOUBLE | FLOAT    | FLOAT    | FLOAT  | FLOAT  | FLOAT  | DOUBLE | DOUBLE      |
| **DOUBLE**   | DOUBLE | DOUBLE   | DOUBLE   | DOUBLE | DOUBLE | DOUBLE | DOUBLE | DOUBLE      |
| **NUMBER**   | 浮动精度NUMBER | 浮动精度NUMBER   | 浮动精度NUMBER   |浮动精度NUMBER | 浮动精度NUMBER | 浮动精度NUMBER | 浮动精度NUMBER | 浮动精度NUMBER或NUMBER(p/s) |




示例

```sql
SELECT IFNULL(1, 2) res FROM DUAL;
         RES 
------------ 
           1

SELECT IFNULL(NULL, 2) res FROM DUAL;
RES   
----- 
2    

SELECT IFNULL(TRUE, 2) res FROM DUAL;
         RES 
------------ 
           1
             
SELECT IFNULL(5, CAST('0.232222' AS FLOAT)) res,
TYPEOF (IFNULL(5, CAST('0.232222' AS FLOAT))) res_type
FROM DUAL;
        RES RES_TYPE                                                         
----------- ---------- 
   5.0E+000 float 
```

