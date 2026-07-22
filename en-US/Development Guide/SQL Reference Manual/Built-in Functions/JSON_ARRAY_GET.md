```ebnf
json_array_get = JSON_ARRAY_GET "(" json_value ", " index ")".
```

The JSON_ARRAY_GET function returns the element at the specified position from a JSON array.

**json_value**

json_value is a binary [json](../General SQL Syntax/json) data that can be obtained through the [JSON](./JSON JSON_PARSE) function.

When json_value is of JSON Array type, the function returns the element at the specified index position; when json_value is of JSON Object/String/Number/Boolean/Null/Extended format type, the function returns NULL.

If json_value is NULL, the function returns NULL.

**index**

index specifies the position of the element to be returned and must be an integer, i.e., TINYINT/SMALLINT/INT/BIGINT.

If the index value is NULL, the function returns NULL.

If the index value is a positive number, it indicates that the value is taken from left to right; if the index value is a negative number, it indicates that the value is taken from right to left; if the index value is out of the array's index range, the function returns NULL.

***Example***

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
