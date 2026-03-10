
The term "Literal" refers to a constant value that appears directly in SQL and PL statements in string format. It is distinguished by type based on the format at declaration, for example, '' is used to identify character literals, and DATE is used to identify date-time literals.

Literals can be used in nearly all scenarios, such as values, parameters, formats, identifiers, or output display.

The conceptual difference between a literal and variables/constants is as follows:

- A literal is a fixed value that appears in string form, lacking a storage container; it is merely a written notation.
- A variable is a container used to store data, and the container can be assigned multiple times.
- A constant is a type of variable that can only be assigned an initial value and cannot be assigned again.


## String Literal (Character Literal)

A string literal is a combination of English letters, Chinese characters, numeric characters, and special characters enclosed in single quotes `''`, declared as follows:

![](./image/16.png)

![](./image/27.png)

YashanDB parses the string literal into VARCHAR type data.

***Example***

```sql
SELECT 'SHENZHEN', '3.14567', '!@#$',
TYPEOF('SHENZHEN') type1,
TYPEOF('3.14567') type2,
TYPEOF('!@#$') type3
FROM DUAL;
+----------+---------+------+---------+---------+---------+
| SHENZHEN | 3.14567 | !@#$ | type1   | type2   | type3   |
+----------+---------+------+---------+---------+---------+
| SHENZHEN | 3.14567 | !@#$ | varchar | varchar | varchar |
+----------+---------+------+---------+---------+---------+
```



## Numeric Literal

A numeric literal is a value formed by the digits 0 to 9 and a decimal point. A numeric literal can be made positive or negative with a positive (+) or negative (-) sign (default is positive). Its declaration is shown below:

![](./image/35.png)

Numeric literals consist of 4 data types: INT, BIGINT, NUMBER, and DOUBLE. YashanDB parses these types according to the following rules:

- If a literal ends with 'd' or 'D', it is parsed as DOUBLE type.
- Decimal values are parsed as NUMBER type.
- Integers are first attempted to be parsed as INT type (checking if within the INT value range [-2<sup>31</sup>, 2<sup>31</sup> - 1]); if this fails, it tries to parse as BIGINT type (checking if within the BIGINT value range [-2<sup>63</sup>, 2<sup>63</sup> - 1]); if that fails, it parses as NUMBER type.
- Values exceeding the NUMBER range are parsed as DOUBLE type.

For example, 11d is parsed as DOUBLE type, 2147483647 as INT type, 2147483648 as BIGINT type, and 1.1 as NUMBER type.

***Example***

```sql
SELECT 1, TYPEOF(1) FROM DUAL;
           1 TYPEOF(1)
------------ ---------
           1 integer
  
SELECT 2147483647, TYPEOF(2147483647) FROM DUAL;
  2147483647 TYPEOF(2147483647)
------------ ------------------
  2147483647 integer
  
SELECT 2147483648, TYPEOF(2147483648) FROM DUAL;
           2147483648 TYPEOF(2147483648)
--------------------- ------------------
           2147483648 bigint
  
SELECT 1.1, TYPEOF(1.1) FROM DUAL;
        1.1 TYPEOF(1.1)
----------- -----------
        1.1 number
            
SELECT 11d, TYPEOF(11d) FROM DUAL;

        11  TYPEOF(11D)
----------- -----------
   1.1E+001 double
```

**Scientific Notation Numeric Literal**

YashanDB supports parsing literals in the format of 1.24E3 as NUMBER type data in scientific notation.

***Example***

```sql
SELECT 1.24e3 FROM dual;
      1.24E3 
------------ 
        1240
```

## Date Literal

A date literal is a value that starts with the DATE keyword and ends with a single quote `''` enclosing the string value, formatted as 'yyyy-mm-dd', for example, `DATE '2020-01-01'`. Its declaration is shown below, where date_str represents a string that conforms to the DATE type standard format.

![](./image/42.png)

***Example***

```sql
SELECT DATE '2020-01-01', TYPEOF(DATE '2020-01-01') TYPE FROM DUAL;
DATE'2020-01-01'                 TYPE
-------------------------------- -----
2020-01-01 00:00:00              date
```

## SCN Literal (Timestamp Literal)

A SCN literal for date is a value that starts with TIMESTAMP and ends with a single quote `''` enclosing the string value, formatted as 'yyyy-mm-dd hh24:mi:ss.ff', where hours, minutes, and seconds can be omitted, for instance, `TIMESTAMP '2020-01-01 13:08:28'`. Its declaration is shown below, where timestamp_str is a string that meets the TIMESTAMP type standard format.

![](./image/8.png)

***Example***

```sql
SELECT TIMESTAMP '2020-01-01 13:08:28', TYPEOF(TIMESTAMP '2020-01-01 13:08:28') TYPE FROM DUAL;
TIMESTAMP'2020-01-01                                             TYPE
---------------------------------------------------------------- -------------
2020-01-01 13:08:28.000000                                       timestamp
```


## Hexadecimal Literal

Hexadecimal literals come in two forms:

- 0xval: x must be lowercase, val can include all hexadecimal digits (0..9, A..F) but cannot be empty. If val has an odd number of digits, it will be padded with a leading 0, for example, 0x123 is equivalent to 0x0123.

- x'val': x is case insensitive, val must be an even number of hexadecimal digits and can be empty (i.e., x''). If val has an odd number of digits, an error will occur.

The default data type for hexadecimal literals is varbinary, where two hexadecimal digits represent a byte, for example, 0x41 corresponds to the ASCII character 'A', and x'' will be treated as an empty string.

In binary operations, the data type of hexadecimal literals is recognized as INT or BIGINT, for example, 0x10+0x10 will be treated as 16+16, and x'' will be considered as 0. To perform calculations using hexadecimal literals as numbers, it is recommended to first add it to 0 before other operations, for instance, 0x10+0.

In binary operations, hexadecimal literals only support +, \-, \*, and / operations.

## \_BINARY Literal

_BINARY specifies constant data as a Binary character set, and _BINARY constant data can take two forms:

- String.

- Hexadecimal literal data.

***Example***

```sql
SELECT _binary x'0001',
TYPEOF(_binary x'0001') TYPE
FROM DUAL;
x'0001' TYPE
------- -----
0001    raw
```
