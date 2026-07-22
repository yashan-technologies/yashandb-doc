```ebnf
get_format = GET_FORMAT "(" (DATE|TIME|TIMESTAMP|DATETIME) "," ('EUR'|'USA'|'JIS'|'ISO'|'INTERNAL') ")" .
```

The GET_FORMAT function returns a format string specified by the corresponding parameters. The return value can be used in combination with the [DATE_FORMAT](DATE_FORMAT) function, [TIME_FORMAT](TIME_FORMAT) function, etc.

If the second parameter specified in the GET_FORMAT function is not within the valid range of values, the GET_FORMAT function returns NULL.

The parameter values and return values for the function are shown in the table below:

|Function Call |Return Value |
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

For the first parameter of the GET_FORMAT function, the keyword TIMESTAMP is equivalent to DATETIME.

***Example*** for  Heap tables

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
