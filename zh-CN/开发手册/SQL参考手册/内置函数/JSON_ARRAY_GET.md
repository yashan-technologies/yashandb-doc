```ebnf
json_array_get = JSON_ARRAY_GET "(" json_value ", " index ")".
```

JSON_ARRAY_GET函数从一个JSON数组数据中返回指定位置的元素。

**json_value**

json\_value为一个二进制[json](../通用SQL语法/json)数据，可通过[JSON](./JSON JSON_PARSE)函数获取。

当json\_value为JSON Array类型时，函数返回指定index位置的元素；当json\_value为JSON Object/String/Number/Boolean/Null/扩展格式类型时，函数返回NULL。

当json_value为NULL时，函数返回NULL。

**index**

index指定返回元素的位置，须为整型数值，即TINYINT/SMALLINT/INT/BIGINT。

当index的值为NULL时，函数返回NULL。

当index的值为正数时，表示按从左往右的顺序取值；当index的值为负数时，表示按从右往左的顺序取值；当index的取值不在数组的索引范围内时，函数返回NULL。

示例

```sql
SELECT JSON_FORMAT(JSON_ARRAY_GET(JSON('[123, "ABC", false]'), 0)) res FROM DUAL;
RES                                             
---------------------------------------------------------------- 
123            
 
SELECT JSON_FORMAT(JSON_ARRAY_GET(JSON('{"id": 1}'), 0)) res FROM DUAL;
RES                                             
---------------------------------------------------------------- 
                                                                
SELECT JSON_FORMAT(JSON_ARRAY_GET(JSON('[123, "ABC", false]'), -1)) res FROM DUAL;
RES                                             
---------------------------------------------------------------- 
false 
```
