NULL表示为空值，YashanDB中将空字符串同样视作NULL处理，NULL并不作为确定的值使用，而表示该值处于未知状态或不具有意义。

仅支持在未定义为NOT NULL或PRIMARY KEY约束限制的列中使用NULL。

## SQL内置函数中的NULL

YashanDB对SQL内置函数中给定参数为NULL的处理结果请查阅[内置函数](../内置函数（yashan模式）/00内置函数（yashan模式）)。

## 比较条件中的NULL

如需判断是否为NULL，请使用比较运算符`IS NULL`进行判断，当操作数为NULL时，结果返回TRUE，否则返回FALSE。

NULL表示空值，即缺少数据，因此无法与其他值（包括NULL）进行比较，当使用`=` `!=` 等运算符进行比较时，结果会返回FALSE。

## 运算中的NULL

YashanDB中算数运算及位运算中所有NULL参与运算的结果均为NULL，部分比较运算、逻辑运算及连接运算的返回结果根据给定的操作数返回不同结果，详见下表：

| 运算     | 运算符            | NULL参与运算                                                 |
| -------- | ----------------- | ------------------------------------------------------------ |
| 算数运算 | +                 | 结果为NULL                                                   |
|          | -                 | 结果为NULL                                                   |
|          | *                 | 结果为NULL                                                   |
|          | /                 | 结果为NULL                                                   |
|          | %                 | 结果为NULL                                                   |
| 比较运算 | =                 | 结果为FALSE                                                  |
|          | != 或 <>          | 结果为FALSE                                                  |
|          | \>                | 结果为FALSE                                                  |
|          | \>=               | 结果为FALSE                                                  |
|          | <                 | 结果为FALSE                                                  |
|          | <=                | 结果为FALSE                                                  |
|          | [NOT] IN          | IN：<br />* 左边数据为NULL：结果为FALSE<br />* 右边集合包含NULL：如果集合中有非NULL值与左边数据相等，则结果为TRUE，否则为FALSE<br />NOT IN：<br />* 左边数据为NULL：结果为FALSE<br />* 右边集合包含NULL：如果集合中含有NULL值，则返回FALSE；如果集合中不含NULL值，且所有数据与左边数据不相等，则结果为TRUE，否则为FALSE |
|          | [NOT] LIKE        | 结果为FALSE                                                  |
|          | [NOT] BETWEEN AND | 结果为FALSE                                                  |
|          | IS [NOT] NULL     | IS NULL：<br />* 操作数为NULL：结果为TRUE<br />* 操作数不为NULL：结果为FALSE<br />IS NOT NULL：<br />* 操作数为NULL：结果为FALSE<br />* 操作数不为NULL：结果为TRUE |
| 逻辑运算 | AND               | 结果为NULL                                                   |
|          | OR                | * TRUE OR NULL：结果为TRUE<br/>* FALSE OR NULL：结果为NULL<br/>* NULL OR NULL：结果为NULL |
|          | NOT               | 结果为NULL                                                   |
| 连接运算 | &#124;&#124;              | 任何数据&#124;&#124;NULL：结果为任何数据                             |
| 位运算   | &                 | 结果为NULL                                                   |
|          | &#124;                | 结果为NULL                                                   |
|          | ^                 | 结果为NULL                                                   |
