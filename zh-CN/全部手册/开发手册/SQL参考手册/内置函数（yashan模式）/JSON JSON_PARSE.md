```ebnf+diagram
json::= JSON "(" expr [EXTENDED] ")"
```

JSON函数用于将[expr](../通用SQL语法/expr)的值转换为二进制[JSON](../通用SQL语法/json)数据。当转换后的JSON数据为JSON Object类型时，函数返回按key值进行排序后的结果。

本函数存在别名JSON_FORMAT和JSON_PARSE，其功能与JSON完全相同。

**expr**

通用表达式，其值须为CHAR、VARCHAR、BFILE或CLOB类型数据。

- expr不能为超过65534字节的VARCHAR、LOB、BFILE类型数据。

- 当expr的值为NULL时，函数返回NULL。

**EXTENDED**

EXTENDED关键字用于指定使用扩展模式解析JSON字符串，详情请查阅[JSON扩展格式解析](../通用SQL语法/json)。

示例

```sql
SELECT JSON_FORMAT(JSON('[123, "ABC", false]')) yason_res FROM DUAL;
YASON_RES                                                        
---------------------------------------------------------------- 
[123,"ABC",false] 

-- JSON Object类型会进行key值排序
SELECT JSON_FORMAT(JSON('{"keyC":123, "keyabc":false, "keyA":234, "keyB":345, "key":true}') pretty) yason_res FROM DUAL;
YASON_RES                                                        
---------------------------------------------------------------- 
{
  "key" : true,
  "keyA" : 234,
  "keyB" : 345,
  "keyC" : 123,
  "keyabc" : false
}

-- 通过扩展模式解析JSON字符串
SELECT JSON('{"$binary": "SGVsbG8gd29ybGQh"}' EXTENDED) yason_res FROM DUAL;
YASON_RES
----------------------------------------------------------------
"48656C6C6F20776F726C6421"
```
