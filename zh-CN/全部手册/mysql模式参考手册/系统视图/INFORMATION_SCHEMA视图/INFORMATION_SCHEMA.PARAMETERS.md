该视图展示了数据库中存储过程和存储函数的参数信息以及存储函数的返回值。

|  字段| 类型| 说明|
| --- | --- | --- |
| SPECIFIC_CATALOG         | varchar(3)   |包含该参数的存储过程或自定义函数所属目录名称，参数值固定为`def`   |
| SPECIFIC_SCHEMA          | varchar(64)  |包含该参数的存储过程或自定义函数所属数据库实例   |
| SPECIFIC_NAME            | varchar(532) |包含该参数的存储过程或自定义函数的名称   |
| ORDINAL_POSITION         | varchar(11)  |参数在存储过程或自定义函数中的位置<br>对于存储过程的参数，值为 1、2、3，依此类推<br>自定义函数的参数值为0表示为输出参数   |
| PARAMETER_MODE           | varchar(6)   |参数的输入输出模式，参数值为IN、OUT或者INOUT<br>对于自定义函数的返回值，该参数值固定为`NULL`   |
| PARAMETER_NAME           | varchar(68)  |参数名称<br>对于自定义函数的返回值，该参数值固定为`NULL`   |
| DATA_TYPE                | varchar(64)  |参数的数据类型   |
| CHARACTER_MAXIMUM_LENGTH | int          |字符串类型参数的字符长度   |
| CHARACTER_OCTET_LENGTH   | varchar(20)  |字符串类型参数的字节长度<br>仅语法兼容，无实际功能含义   |
| NUMERIC_PRECISION        | int          |数值类型参数的精度   |
| NUMERIC_SCALE            | int          |数值类型参数的小数位数   |
| DATETIME_PRECISION       | varchar(20)  |时间类型参数的毫秒精度<br>仅语法兼容，无实际功能含义   |
| CHARACTER_SET_NAME       | varchar(20)  |字符串类型参数的字符集<br>仅语法兼容，无实际功能含义   |
| COLLATION_NAME           | varchar(20)  |字符串类型参数的排序规则<br>仅语法兼容，无实际功能含义   |
| DTD_IDENTIFIER           | varchar(20)  |参数数据类型的详细信息<br>仅语法兼容，无实际功能含义   |
| ROUTINE_TYPE             | varchar(9)   |该参数所属对象类型，存储过程为`PROCEDURE`，自定义函数为`FUNCTION`   |
