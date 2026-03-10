```ebnf+diagram
convert_tz::= CONVERT_TZ "(" dt "," from_tz "," to_tz ")" 
```

CONVERT_TZ函数将dt参数指定的TIMESTAMP类型值由from_tz参数指定的时区转换为to_tz参数指定的时区，并返回得到的TIMESTAMP类型值，dt参数为可转换为TIMESTAMP的数据类型。

若from_tz、to_tz参数为非法值，CONVERT_TZ函数返回NULL。

若转换后的结果超出了TIMESTAMP类型值的合法值，CONVERT_TZ函数返回dt参数值。

from_tz、to_tz参数的合法值如下，均不区分大小写：

- 'SYSTEM'，指定数据库时区与操作系统时区相同。

- 用于描述与UTC时间偏移量的字符串，格式为`[H]H:MM`，前缀`+`或`-`，如'+10:00'、'-7:30'。当hour值小于10时，可省略前导零。合法取值为'-13:59'-'+14:00'。

示例（HEAP表）

yasql和MySQL Client客户端对时间格式的回显打印机制不同，不影响业务使用。

```sql

-- exmaple via yasql
SELECT CONVERT_TZ('2025-8-1 23:12:56', '+3:00', '+8:30') res FROM DUAL;

res               
----------------- 
2025-08-02 04:42:56.000000

-- exmaple via MySQL Client
SELECT CONVERT_TZ('2025-8-1 23:12:56', '+3:00', '+8:30') res FROM DUAL;

res               
----------------- 
2025-08-02 04:42:56

```
