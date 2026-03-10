```ebnf+diagram
pi::= PI  "("")" 
```

PI函数无给定参数，返回圆周率的值，返回数据类型为DOUBLE。



示例

```sql
--求圆周率的值
SELECT PI() FROM DUAL;

                 PI()
---------------------
 3.1415926535898E+000
```

