```ebnf
concat = CONCAT "(" expr "," expr {"," expr} ")".
```

CONCAT函数将多个[expr](../通用SQL语法/expr)的值连接成一个字符串，等同于`expr||expr||……`。

本函数遵循如下规则：

- 本函数的参数须为[2,65535]个。

- 在向量化执行引擎中，expr不能为LOB类型的行外存储数据。

- 对于非字符型/CLOB类型的expr值，函数会先将其转换为VARCHAR类型（转换失败将返回错误）。

- 当expr值为NULL时，函数将其转换为长度为0的VARCHAR字符串。

- 返回值的数据类型如下：

  - 当expr值中不存在CLOB/NCLOB类型时：

    - 仅包含CHAR/NCHAR类型数据时，函数返回CHAR/NCHAR类型的字符串。

    - 仅包含CHAR和VARCHAR类型数据时，函数返回VARCHAR类型的字符串。

    - 仅包含CHAR和NCHAR类型数据时，函数返回NCHAR类型的字符串。

    - 存在NVARCHAR类型数据时，函数返回NVARCHAR类型的字符串。

    - 不包含NVARCHAR类型数据，但存在VARCHAR和NCHAR类型时，函数返回NVARCHAR类型的字符串。
  
    - expr包含BFILE类型数据时，会隐式转换成VARCHAR类型，之后函数按照上述规则判断返回类型。

  - 当expr值中存在CLOB或NCLOB时，通过如下三个规则判断返回的数据类型：

    - 规则1：当参数列表中只有一个CLOB时，如果前面不包含NCHAR、NVARCHAR数据，则返回CLOB，否则返回NCLOB。

    - 规则2：当参数列表中只有一个NCLOB时，如果前面包含NCHAR、NVARCHAR数据或者没有其他参数，则返回NCLOB，否则返回CLOB。

    - 规则3：当有多个CLOB或NCLOB时，则以参数列表中从左往右的首个CLOB或NCLOB作为唯一的CLOB或NCLOB代入规则1或规则2。

示例

```sql
SELECT CONCAT(employee_name,' has joined us for ',CEIL(SYSDATE-entry_date),' days.') res FROM employees;
RES                                                               
----------------------------------------------------------------
Mask has joined us for 1001 days.                              
John has joined us for 2001 days.                              
Anna has joined us for 301 days.                               
Jack has joined us for 701 days.                               
Jim has joined us for 201 days.
```
