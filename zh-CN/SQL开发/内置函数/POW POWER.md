```ebnf+diagram
pow::= POW "(" expr "," exp ")"
power::= POWER "(" expr "," exp ")"
```

POW/POWER函数计算[expr](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)参数值的exp次幂，其返回类型为：

- 当expr的值为TINYINT、SMALLINT、INT、BIGINT、NUMBER、CHAR、VARCHAR、NCHAR、NVARCHAR类型时，返回NUMBER。
- 当expr的值为FLOAT或DOUBLE类型时，返回DOUBLE。
- 当exp的值为FLOAT或DOUBLE类型时，返回DOUBLE。
-  当expr或exp的值为NULL时，函数返回NULL。
- 对于不能转换为NUMBER类型的expr或exp报错。

**exp**

指数。

exp为与expr相同的通用表达式，当exp的值为NULL时，函数返回NULL。 

下表列示了不同情况下本函数的计算规则：（字符型将被隐式转换为NUMBER类型参与下表规则）

|  expr底数数据类型| expr底数值| exp指数数据类型| exp指数值| 函数计算结果|
| ------------------ | ----------- | --------------------------- | --------- | ------------- |
| 整数/浮点数/NUMBER | 正数/负数/0 | 整数/浮点数/NUMBER          | 0         | 1             |
| 整数/浮点数/NUMBER | 正数        | 整数/浮点数/NUMBER          | 正数/负数 | expr的exp次幂 |
| 整数/浮点数/NUMBER | 0           | 整数/浮点数/NUMBER          | 正数      | 0             |
| 整数/浮点数/NUMBER | 0           | 浮点数                      | 负数      | Inf           |
| 整数/浮点数/NUMBER | 0           | 整数/NUMBER                 | 负数      | YAS-00012错误 |
| 整数               | 负数        | 整数/NUMBER（小数点后无值） | 正数/负数 | expr的exp次幂 |
| 整数               | 负数        | 浮点数                      | 正数/负数 | Nan           |
| 整数               | 负数        | NUMBER（小数点后有值）      | 正数/负数 | YAS-04426错误 |
| 浮点数             | 负数        | 整数                        | 正数/负数 | expr的exp次幂 |
| 浮点数             | 负数        | 浮点数                      | 正数/负数 | Nan           |
| 浮点数             | 负数        | NUMBER                      | 正数/负数 | Nan           |
| NUMBER             | 负数        | 整数/NUMBER（小数点后无值） | 正数/负数 | expr的exp次幂 |
| NUMBER             | 负数        | 浮点数                      | 正数/负数 | Nan           |
| NUMBER             | 负数        | NUMBER（小数点后有值）      | 正数/负数 | YAS-04426错误 |

示例

```sql
SET NUMWIDTH 20;

SELECT POW(2,2) res FROM DUAL;
                  RES 
--------------------- 
                    4

SELECT POW(2,-2) res FROM DUAL;
                  RES 
--------------------- 
                  .25

SELECT POW(2,2.1) res FROM DUAL;
                  RES
---------------------
 4.287093850145172657

SELECT POWER(2.2,-12.1) res FROM DUAL;
                  RES
---------------------
 .0000718928270982645
           
SELECT POW(-1,1.1) res FROM DUAL;
[1:13]YAS-04426 the argument value is out of range

SELECT POW(0, -1) res FROM DUAL;
[1:12]YAS-00012 numeric overflow
```
