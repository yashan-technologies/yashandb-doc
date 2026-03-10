```ebnf+diagram
unhex::= UNHEX "(" expr ")"
```

The UNHEX function is used to interpret each pair of characters or bytes in [expr](../General SQL Syntax/expr) as hexadecimal numbers and convert them to the bytes represented by those hexadecimal numbers. The return type is binary.

This function follows the rules below:

- expr cannot be of JSON type.
- If expr is of binary type or bit type, the function first converts each byte into hexadecimal characters, then interprets every 2 hexadecimal characters as a hexadecimal number (an error will be raised if non-hexadecimal characters are detected) and converts it to the bytes represented by the hexadecimal number.
- If expr is of other types, the function first converts it to a string (an error will be raised if the conversion fails), then interprets every 2 characters as a hexadecimal number (an error will be raised if non-hexadecimal characters are detected) and converts it to the bytes represented by the hexadecimal number.
- When expr is NULL, the function returns NULL.

***Example*** for  Heap tables

```sql
SELECT UNHEX('41') res;
+-------------+
| res         |
+-------------+
| A           |
+-------------+
1 row in set (0.00 sec)
```
