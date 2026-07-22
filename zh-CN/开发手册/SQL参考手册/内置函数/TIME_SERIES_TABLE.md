```ebnf
time_series_table = TIME_SERIES_TABLE "(" select_statement ")".
```

TIME_SERIES_TABLE 是一个内置表函数，用于时序数据查询并以关系表的形式返回查询结果。

该函数的使用规则如下：
- 访问TIME_SERIES_TABLE返回的列时，列名匹配不区分大小写。
- 包含该函数的SQL不进入计划缓存，每次执行都会重新解析和校验。

**select_statement**

select_statement 为字符串常量，必须为合法的SELECT查询语句。

示例

```sql
--简单查询：查询时序数据表test1中时间大于1000的全部数据
SELECT * FROM TIME_SERIES_TABLE('SELECT * FROM test1 WHERE time > 1000');

--联合查询：将时序数据表test1的温度数据与时序数据表test2的湿度数据按设备名进行连接
SELECT a.device, a.temperature, b.humidity
FROM TIME_SERIES_TABLE('SELECT device, temperature FROM test1') a
JOIN TIME_SERIES_TABLE('SELECT device, humidity FROM test2') b
ON a.device = b.device;
```