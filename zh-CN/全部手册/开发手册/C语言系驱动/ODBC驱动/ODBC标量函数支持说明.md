ODBC官方指定了以下的[标量函数](https://learn.microsoft.com/zh-cn/sql/odbc/reference/appendixes/appendix-scalar-functions?view=sql-server-ver16)：

- 字符串函数
- 数值函数
- 时间、日期和时间间隔函数
- 系统函数
- 显示数据类型转换数
- SQL-92 CAST函数

YashanDB ODBC驱动已支持的标量函数如下表所示：

|  接口| 说明|
| ----------------------------------- | ----------------------------------------------------------------------------- | 
| CONCAT（string_exp1, string_exp2）  |  将字符串string_exp2连接到string_exp1并返回。 |
| IFNULL（exp, value） |  如果exp为null，则返回value值。如果exp不为null，则返回exp。<br/>数据类型或值类型必须与exp的数据类型兼容。 |
| LEFT（string_exp, count） | 返回从左往右第1 ~ count位字符。|
| SUBSTRING（string_exp, start, length**） | 返回从string_exp中截取的子字符串，从start位置开始截取长度为length的字符串。|
| RIGHT（string_exp, count） | 返回从右往左第1 ~ count位字符。 |
