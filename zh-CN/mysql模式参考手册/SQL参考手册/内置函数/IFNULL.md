```ebnf
ifnull = IFNULL "(" expr1 "," expr2 ")".
```

IFNULL函数有2个[expr](../通用SQL语法/expr)参数，当expr1不为NULL时返回expr1，否则返回expr2。

函数比较返回值类型规则如下：



| expr1\expr2 | TINYINT  | SMALLINT | INT     | BIGINT  | UTINYINT  | USMALLINT | UINT    | UBIGINT |
| ----------- | -------- | -------- | ------- | ------- | --------- | --------- | ------- | ------- |
| TINYINT     | TINYINT  | SMALLINT | INT     | BIGINT  | SMALLINT  | INT       | BIGINT  | NUMBER  |
| SMALLINT    | SMALLINT | SMALLINT | INT     | BIGINT  | SMALLINT  | INT       | BIGINT  | NUMBER  |
| INT         | INT      | INT      | INT     | BIGINT  | INT       | INT       | BIGINT  | NUMBER  |
| BIGINT      | BIGINT   | BIGINT   | BIGINT  | BIGINT  | BIGINT    | BIGINT    | BIGINT  | NUMBER  |
| UTINYINT    | SMALLINT | SMALLINT | INT     | BIGINT  | UTINYINT  | USMALLINT | UINT    | UBIGINT |
| USMALLINT   | INT      | INT      | INT     | BIGINT  | USMALLINT | USMALLINT | UINT    | UBIGINT |
| UINT        | BIGINT   | BIGINT   | BIGINT  | BIGINT  | UINT      | UINT      | UINT    | UBIGINT |
| UBIGINT     | NUMBER   | NUMBER   | NUMBER  | NUMBER  | UBIGINT   | UBIGINT   | UBIGINT | UBIGINT |
| FLOAT       | FLOAT    | FLOAT    | FLOAT   | FLOAT   | FLOAT     | FLOAT     | FLOAT   | FLOAT   |
| DOUBLE      | DOUBLE   | DOUBLE   | DOUBLE  | DOUBLE  | DOUBLE    | DOUBLE    | DOUBLE  | DOUBLE  |
| NUMBER      | NUMBER   | NUMBER   | NUMBER  | NUMBER  | NUMBER    | NUMBER    | NUMBER  | NUMBER  |
| DATE        | VARCHAR  | VARCHAR  | VARCHAR | VARCHAR | VARCHAR   | VARCHAR   | VARCHAR | VARCHAR |
| TIME        | VARCHAR  | VARCHAR  | VARCHAR | VARCHAR | VARCHAR   | VARCHAR   | VARCHAR | VARCHAR |
| TIMESTAMP   | VARCHAR  | VARCHAR  | VARCHAR | VARCHAR | VARCHAR   | VARCHAR   | VARCHAR | VARCHAR |
| CHAR        | CHAR     | CHAR     | CHAR    | CHAR    | CHAR      | CHAR      | CHAR    | CHAR    |
| VARCHAR     | VARCHAR  | VARCHAR  | VARCHAR | VARCHAR | VARCHAR   | VARCHAR   | VARCHAR | VARCHAR |
| VARBINARY   | RAW      | RAW      | RAW     | RAW     | RAW       | RAW       | RAW     | RAW     |
| TEXT        | TEXT     | TEXT     | TEXT    | TEXT    | TEXT      | TEXT      | TEXT    | TEXT    |
| BLOB        | BLOB     | BLOB     | BLOB    | BLOB    | BLOB      | BLOB      | BLOB    | BLOB    |


| expr1\expr2 | FLOAT   | DOUBLE  | NUMBER  | DATE      | TIME      | TIMESTAMP |
| ----------- | ------- | ------- | ------- | --------- | --------- | --------- |
| TINYINT     | FLOAT   | DOUBLE  | NUMBER  | VARCHAR   | VARCHAR   | VARCHAR   |
| SMALLINT    | FLOAT   | DOUBLE  | NUMBER  | VARCHAR   | VARCHAR   | VARCHAR   |
| INT         | FLOAT   | DOUBLE  | NUMBER  | VARCHAR   | VARCHAR   | VARCHAR   |
| BIGINT      | FLOAT   | DOUBLE  | NUMBER  | VARCHAR   | VARCHAR   | VARCHAR   |
| UTINYINT    | FLOAT   | DOUBLE  | NUMBER  | VARCHAR   | VARCHAR   | VARCHAR   |
| USMALLINT   | FLOAT   | DOUBLE  | NUMBER  | VARCHAR   | VARCHAR   | VARCHAR   |
| UINT        | FLOAT   | DOUBLE  | NUMBER  | VARCHAR   | VARCHAR   | VARCHAR   |
| UBIGINT     | FLOAT   | DOUBLE  | NUMBER  | VARCHAR   | VARCHAR   | VARCHAR   |
| FLOAT       | FLOAT   | DOUBLE  | DOUBLE  | VARCHAR   | VARCHAR   | VARCHAR   |
| DOUBLE      | DOUBLE  | DOUBLE  | DOUBLE  | VARCHAR   | VARCHAR   | VARCHAR   |
| NUMBER      | DOUBLE  | DOUBLE  | NUMBER  | VARCHAR   | VARCHAR   | VARCHAR   |
| DATE        | VARCHAR | VARCHAR | VARCHAR | DATE      | TIMESTAMP | TIMESTAMP |
| TIME        | VARCHAR | VARCHAR | VARCHAR | TIMESTAMP | TIME      | TIMESTAMP |
| TIMESTAMP   | VARCHAR | VARCHAR | VARCHAR | TIMESTAMP | TIMESTAMP | TIMESTAMP |
| CHAR        | CHAR    | CHAR    | CHAR    | CHAR      | CHAR      | CHAR      |
| VARCHAR     | VARCHAR | VARCHAR | VARCHAR | VARCHAR   | VARCHAR   | VARCHAR   |
| VARBINARY   | RAW     | RAW     | RAW     | RAW       | RAW       | RAW       |
| TEXT        | TEXT    | TEXT    | TEXT    | TEXT      | TEXT      | TEXT      |
| BLOB        | BLOB    | BLOB    | BLOB    | BLOB      | BLOB      | BLOB      |


| expr1\expr2 | CHAR    | VARCHAR | RAW  | TEXT | BLOB |
| ----------- | ------- | ------- | ---- | ---- | ---- |
| TINYINT     | CHAR    | VARCHAR | RAW  | TEXT | BLOB |
| SMALLINT    | CHAR    | VARCHAR | RAW  | TEXT | BLOB |
| INT         | CHAR    | VARCHAR | RAW  | TEXT | BLOB |
| BIGINT      | CHAR    | VARCHAR | RAW  | TEXT | BLOB |
| UTINYINT    | CHAR    | VARCHAR | RAW  | TEXT | BLOB |
| USMALLINT   | CHAR    | VARCHAR | RAW  | TEXT | BLOB |
| UINT        | CHAR    | VARCHAR | RAW  | TEXT | BLOB |
| UBIGINT     | CHAR    | VARCHAR | RAW  | TEXT | BLOB |
| FLOAT       | CHAR    | VARCHAR | RAW  | TEXT | BLOB |
| DOUBLE      | CHAR    | VARCHAR | RAW  | TEXT | BLOB |
| NUMBER      | CHAR    | VARCHAR | RAW  | TEXT | BLOB |
| DATE        | CHAR    | VARCHAR | RAW  | TEXT | BLOB |
| TIME        | CHAR    | VARCHAR | RAW  | TEXT | BLOB |
| TIMESTAMP   | CHAR    | VARCHAR | RAW  | TEXT | BLOB |
| CHAR        | CHAR    | VARCHAR | RAW  | TEXT | BLOB |
| VARCHAR     | VARCHAR | VARCHAR | RAW  | TEXT | BLOB |
| VARBINARY   | RAW     | RAW     | RAW  | BLOB | BLOB |
| TEXT        | TEXT    | TEXT    | BLOB | BLOB | BLOB |
| BLOB        | BLOB    | BLOB    | BLOB | BLOB | BLOB |



示例（HEAP表）

```sql
SELECT IFNULL(1, 2) res FROM DUAL;
         res 
------------ 
           1

SELECT IFNULL(NULL, 2) res FROM DUAL;
res   
----- 
2    

SELECT IFNULL(TRUE, 2) res FROM DUAL;
         res 
------------ 
           1
             
SELECT IFNULL(5, CAST('0.232222' AS NUMBER)) res,
TYPEOF (IFNULL(5, CAST('0.232222' AS NUMBER))) res_type
FROM DUAL;
        res res_type                                                         
----------- -----------------
          5 number
```
