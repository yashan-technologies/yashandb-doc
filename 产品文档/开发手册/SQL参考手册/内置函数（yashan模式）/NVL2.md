```ebnf+diagram
nvl2::= NVL2 "(" expr1 "," expr2 "," expr3 ")"
```

NVL2函数用于根据expr1是否为空决定返回expr2或expr3的值。

expr1、expr2、expr3均为YashanDB认可的[通用表达式](../通用SQL语法/expr)。

expr1、expr2、expr3均支持LOB及XMLTYPE类型隐式转换。

函数返回值的规则如下：
* 如果expr1非空，则返回expr2的值。
* 如果expr1为空，则返回expr3的值。

函数返回值的数据类型的规则如下：
* expr2和expr3其中一个为空时，函数返回值的数据类型与非空值一致。
* expr2和expr3均为空时，函数返回值的数据类型为VARCHAR类型。
* expr2和expr3均不为空且二者数据类型不同时，函数会先执行[数据类型转换](../数据类型（yashan模式）/数据类型转换)并返回转换后的数据类型，若二者数据类型无法进行转换则返回类型转换错误。二者数据类型支持转换时，转换规则如下：
    * expr2和expr3均为数值型时，函数会根据提升规则（TINYINT -> SMALLINT -> INT -> BIGINT -> NUMBER -> FLOAT -> DOUBLE）将数值优先级低的数据类型转换为优先级高的数据类型。
    * expr2和expr3都为DATE、TIMESTAMP、时区类型时，返回类型优先级为TIMESTAMP TZ -> TIMESTAMP LTZ -> TIMESTAMP -> DATE。
    * expr2为其他类型时，函数会将expr3隐式转换为expr2的数据类型。

示例

```sql
--因为第一个参数不为空，所以返回第二个参数，同时第二个参数和第三个参数之间进行隐式类型转换
SELECT NVL2(1,NOW(),'2011-04-01 12:28:03') res FROM DUAL;
RES                              
-------------------------------- 
2022-10-09 00:17:09  

--因为第一个参数是空的，所以返回第三个参数，同时第二个参数和第三个参数之间进行隐式类型转换
SELECT NVL2('',NOW(),'2011-04-01 12:28:03') res FROM DUAL;
RES                              
-------------------------------- 
2011-04-01 12:28:03 

--隐式类型转换失败
SELECT NVL2(null,NOW(),'an apple') res FROM DUAL;
[1:13]YAS-00008 type convert error : literal does not match format string

--INT类型转换成VARCHAR类型
SELECT NVL2(null,'an apple',124) res FROM DUAL;
RES   
----- 
124  
```