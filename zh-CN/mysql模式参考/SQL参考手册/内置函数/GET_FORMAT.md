```ebnf+diagram
get_format::= GET_FORMAT "(" (DATE|TIME|TIMESTAMP|DATETIME) "," ("EUR"|"USA"|"JIS"|"ISO"|"INTERNAL") ")" 
```

GET_FORMAT函数返回对应参数指定的格式字符串，返回值可与[DATE_FORMAT](./DATE_FORMAT)函数、[TIME_FORMAT](./TIME_FORMAT)函数等组合使用。

若指定的GET_FORMAT函数的第二个参数不在合法值范围内，GET_FORMAT函数返回NULL。

函数参数取值及返回值如下表：

|  函数调用| 返回值|
| --- | --- |
| GET_FORMAT(DATE,'USA') | '%m.%d.%Y' |
| GET_FORMAT(DATE,'JIS') | '%Y-%m-%d' |
| GET_FORMAT(DATE,'ISO') | '%Y-%m-%d' |
| GET_FORMAT(DATE,'EUR') | '%d.%m.%Y' |
| GET_FORMAT(DATE,'INTERNAL') | '%Y%m%d' |
| GET_FORMAT(DATETIME,'USA')  | '%Y-%m-%d %H.%i.%s' |
| GET_FORMAT(DATETIME,'JIS')  | '%Y-%m-%d %H:%i:%s' |
| GET_FORMAT(DATETIME,'ISO')  | '%Y-%m-%d %H:%i:%s' |
| GET_FORMAT(DATETIME,'EUR')  |'%Y-%m-%d %H.%i.%s' |
| GET_FORMAT(DATETIME,'INTERNAL') |'%Y%m%d%H%i%s' |
| GET_FORMAT(TIME,'USA') | '%h:%i:%s %p' |
| GET_FORMAT(TIME,'JIS') | '%H:%i:%s' |
| GET_FORMAT(TIME,'ISO') | '%H:%i:%s' |
| GET_FORMAT(TIME,'EUR') | '%H.%i.%s' |
| GET_FORMAT(TIME,'INTERNAL') | '%H%i%s' |

对于GET_FORMAT函数的第一个参数，关键字TIMESTAMP与DATETIME等价。

示例（HEAP表）

```sql
SELECT GET_FORMAT(TIME,'INTERNAL') res FROM DUAL;

res               
----------------- 
%H%i%s

SELECT DATE_FORMAT('2025-6-1', GET_FORMAT(TIMESTAMP,'ISO')) res FROM DUAL;

res
----------------- 
2025-06-01 00:00:00
```
