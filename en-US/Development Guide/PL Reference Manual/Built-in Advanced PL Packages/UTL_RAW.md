The UTL_RAW package provides SQL functions for operating on RAW data types.

## BIT_AND

```plsql
UTL_RAW.BIT_AND (
   r1 IN RAW,
   r2 IN RAW) 
RETURN RAW;
```

The BIT_AND function performs a bitwise logical "AND" on the values in RAW r1 and RAW r2 and returns the result as RAW.

|Parameter |Description |
| :--- | :-------------------------- |
| r1 | RAW type value, can be '' or NULL |
| r2 | RAW type value, can be '' or NULL |

Function usage rules:

- If any input parameter value is '' or NULL, the return result is NULL.
- If r1 and r2 have different lengths, the bitwise "AND" operation stops at the last byte of the shorter RAW, and the excess part of the longer RAW is appended at the end. The length of the final returned RAW will match the longer RAW.

***Example***

```plsql
SQL> select utl_raw.bit_and('ff01','f02') from dual;

UTL_RAW.BIT_AND('FF0                                             
---------------------------------------------------------------- 
0F00                                                            

1 row fetched.

SQL> select utl_raw.bit_and('ff01','02') from dual;

UTL_RAW.BIT_AND('FF0                                             
---------------------------------------------------------------- 
0201                                                            

1 row fetched.
```

## BIT_COMPLEMENT

```plsql
UTL_RAW.BIT_COMPLEMENT (
   r IN RAW) 
  RETURN RAW;
```

The BIT_COMPLEMENT function performs a bitwise logical "complement" on the value in RAW r and returns the result as RAW. The result length is equal to the input RAW r length.

|Parameter |Description |
| :--- | :-------------------------- |
| r  | RAW type value, can be '' or NULL |

Function usage rules:

- If the input is NULL or '', it returns NULL.

```plsql
SQL> select utl_raw.BIT_COMPLEMENT('') from dual;

UTL_RAW.BIT_COMPLEME                                             
---------------------------------------------------------------- 

1 row fetched.

SQL> select utl_raw.BIT_COMPLEMENT('ff00') from dual;

UTL_RAW.BIT_COMPLEME                                             
---------------------------------------------------------------- 
00FF                                                            

1 row fetched.
```

## BIT_OR

```plsql
UTL_RAW.BIT_OR (
   r1 IN RAW,
   r2 IN RAW) 
  RETURN RAW;
```

The BIT_OR function performs a bitwise logical "OR" on the values in RAW r1 and RAW r2 and returns the result as RAW.

|Parameter |Description |
| :--- | :-------------------------- |
| r1 | RAW type value, can be '' or NULL |
| r2 | RAW type value, can be '' or NULL |

Function usage rules:

- If r1 and r2 have different lengths, the "OR" operation stops at the last byte of the shorter RAW, and the longer RAW is appended at the end. The result length will match the longer RAW.
- Returns the result of the "OR" operation on r1 and r2; if either r1 or r2 is NULL or '', it returns NULL.

***Example***

```plsql
SQL> select utl_raw.bit_or('ff01', 'f02') from dual;

UTL_RAW.BIT_OR('FF01                                             
---------------------------------------------------------------- 
FF03                                                            

1 row fetched.
```

## BIT_XOR

```plsql
UTL_RAW.BIT_XOR (
   r1 IN RAW,
   r2 IN RAW) 
  RETURN RAW;
```

The BIT_XOR function performs a bitwise logical "XOR" on the values in RAW r1 and RAW r2 and returns the result as RAW.

|Parameter |Description |
| :--- | :-------------------------- |
| r1 | RAW type value, can be '' or NULL |
| r2 | RAW type value, can be '' or NULL |

Function usage rules:

- If r1 and r2 have different lengths, the "XOR" operation stops at the last byte of the shorter RAW, and the longer RAW is appended at the end. The result length will match the longer RAW.
- Returns the result of the "XOR" operation on r1 and r2; if either r1 or r2 is NULL or '', it returns NULL.

***Example***

```plsql
SQL> select utl_raw.bit_xor('ff01', 'f02') from dual;

UTL_RAW.BIT_XOR('FF0                                             
---------------------------------------------------------------- 
F003                                                            

1 row fetched.
```

## CAST_FROM_BINARY_DOUBLE

```plsql
UTL_RAW.CAST_FROM_BINARY_DOUBLE(
   n          IN BINARY_DOUBLE,
   endianess  IN PLS_INTEGER DEFAULT 1) 
RETURN RAW;
```

The CAST_FROM_BINARY_DOUBLE function converts a DOUBLE type to RAW, following the IEEE754 standard.

|Parameter |Description |
| ----------- | ------------------------------------------------------------ |
| n           | BINARY_DOUBLE type value                                      |
| endianess   | Indicates the endianness as a BINARY_INTEGER value, options are as follows:<br/>* 1 or big_endian: indicates big-endian format, this is the default value<br/>* 2 or little_endian: indicates little-endian format<br/>* 3 or machine_endian: indicates the endianness of the YashanDB server |

***Example***

```plsql
SQL> select utl_raw.cast_from_binary_double(123.45, 1) as a from dual;

A                 
----------------- 
405EDCCCCCCCCCCD 

1 row fetched.
```

## CAST_FROM_BINARY_FLOAT

```plsql
UTL_RAW.CAST_FROM_BINARY_FLOAT(
   n          IN BINARY_FLOAT,
   endianess  IN PLS_INTEGER DEFAULT 1) 
RETURN RAW;
```

The CAST_FROM_BINARY_FLOAT function converts a FLOAT type to RAW.

|Parameter |Description |
| ----------- | ------------------------------------------------------------ |
| n           | BINARY_FLOAT type value                                       |
| endianess   | Indicates the endianness as a BINARY_INTEGER value, options are as follows:<br/>* 1 or big_endian: indicates big-endian format, this is the default value<br/>* 2 or little_endian: indicates little-endian format<br/>* 3 or machine_endian: indicates the endianness of the YashanDB server |

***Example***

```plsql
SQL> select utl_raw.cast_from_binary_float(123.45,1) as a from   dual;

A         
--------- 
42F6E666 

1 row fetched.
```

## CAST_FROM_BINARY_INTEGER

```plsql
UTL_RAW.CAST_FROM_BINARY_INTEGER (
   n          IN BINARY_INTEGER
   endianess  IN PLS_INTEGER DEFAULT BIG_ENDIAN) 
RETURN RAW;
```

The CAST_FROM_BINARY_INTEGER function converts an INTEGER to RAW type.

|Parameter |Description |
| ----------- | ------------------------------------------------------------ |
| n           | BINARY_INTEGER type value                                     |
| endianess   | Indicates the endianness as a BINARY_INTEGER value, options are as follows:<br/>* 1 or big_endian: indicates big-endian format, this is the default value<br/>* 2 or little_endian: indicates little-endian format<br/>* 3 or machine_endian: indicates the endianness of the YashanDB server |

***Example***

```plsql
SQL> select utl_raw.cast_from_binary_integer(13423,1)  as a from dual;

A         
--------- 
0000346F 

1 row fetched.
```

## CAST_FROM_NUMBER

```plsql
UTL_RAW.CAST_FROM_NUMBER (
n IN NUMBER)
RETURN RAW;
```

The CAST_FROM_NUMBER function converts a NUMBER type to RAW format.

|Parameter |Description |
| ---- | -------------- |
| n    | NUMBER type value  |

***Example***

```plsql
SQL> select utl_raw.cast_from_number(123456.789) as a from dual;

A             
------------- 
C30D23394F5B 

1 row fetched.
```

## CAST_TO_NUMBER

```plsql
UTL_RAW.CAST_TO_NUMBER (
   r  IN RAW) 
 RETURN NUMBER;
```

The CAST_TO_NUMBER function converts RAW type to NUMBER.

|Parameter |Description |
| ---- | --------------------------- |
| r    | RAW type value, can be '' or NULL |

***Example***

```plsql
SQL> select utl_raw.cast_to_number(utl_raw.cast_from_number(123456.789)) as a from dual;

          A 
----------- 
 123456.789

1 row fetched.
```

## CAST_TO_NVARCHAR2

```plsql
UTL_RAW.CAST_TO_NVARCHAR2 (
   r IN RAW) 
RETURN NVARCHAR2;
```

The CAST_TO_NVARCHAR2 function converts RAW type to NVARCHAR2 format using the database's default character set.

|Parameter |Description |
| ---- | --------------------------- |
| r  | RAW type value, can be '' or NULL |

***Example***

```plsql
SQL> select utl_raw.cast_to_nvarchar2('20142014')  as a from dual;

A
----------------------------------------------------------------
——

1 row fetched.
```

## CAST_TO_RAW

```plsql
UTL_RAW.CAST_TO_RAW (
   c  IN VARCHAR2) 
RETURN RAW;
```

The CAST_TO_RAW function converts VARCHAR2 type data to binary data format. This operation only changes the data format without changing the data value.

|Parameter |Description |
| ---- | ---------------- |
| `c`  | VARCHAR2 type value |

***Example***

```plsql
SQL> select utl_raw.cast_to_RAW('aA1234567890')  as a from dual;

A                         
------------------------- 
614131323334353637383930 

1 row fetched.
```

## CAST_TO_VARCHAR2

```plsql
UTL_RAW.CAST_TO_VARCHAR2 (
   r IN RAW) 
RETURN VARCHAR2;
```

The CAST_TO_VARCHAR2 function converts RAW type to VARCHAR2 format using the database's default character set.

|Parameter |Description |
| ---- | --------------------------- |
| r  | RAW type value, can be '' or NULL |

***Example***

```plsql
SQL> select utl_raw.cast_to_varchar2(utl_raw.cast_to_RAW('——'))  as a from dual;

A     
----- 
——   

1 row fetched.
```

## CAST_TO_BINARY_DOUBLE

```plsql
UTL_RAW.CAST_TO_BINARY_DOUBLE (
   r          IN RAW
   endianess  IN PLS_INTEGER DEFAULT 1) 
RETURN BINARY_DOUBLE;
```

The CAST_TO_BINARY_DOUBLE function converts RAW type to DOUBLE type, complying with the IEEE754 standard.

|Parameter |Description |
| ----------- | ------------------------------------------------------------ |
| r         | BINARY_DOUBLE type value                                        |
| endianess | Indicates the endianness as a BINARY_INTEGER value, options are as follows:<br/>* 1 or big_endian: indicates big-endian format, this is the default value<br/>* 2 or little_endian: indicates little-endian format<br/>* 3 or machine_endian: indicates the endianness of the YashanDB server |

***Example***

```plsql
SQL> select utl_raw.cast_to_binary_double(utl_raw.cast_from_binary_double(123.45, 2), 2) as a from dual;

          A 
----------- 
 1.235E+002

1 row fetched.
```

## CAST_TO_BINARY_FLOAT

```plsql
UTL_RAW.CAST_TO_BINARY_FLOAT (
   r          IN RAW
   endianess  IN PLS_INTEGER DEFAULT 1) 
RETURN BINARY_FLOAT;
```

The CAST_TO_BINARY_FLOAT function converts RAW type to FLOAT type, complying with the IEEE754 standard.

|Parameter |Description |
| ----------- | ------------------------------------------------------------ |
| r         | BINARY_FLOAT type value                                        |
| endianess | Indicates the endianness as a BINARY_INTEGER value, options are as follows:<br/>* 1 or big_endian: indicates big-endian format, this is the default value<br/>* 2 or little_endian: indicates little-endian format<br/>* 3 or machine_endian: indicates the endianness of the YashanDB server |

***Example***

```plsql
SQL> select utl_raw.cast_to_binary_float(utl_raw.cast_from_binary_float(123.45, 2), 2) as a from dual;

          A 
----------- 
 1.235E+002

1 row fetched.
```

## CAST_TO_BINARY_INTEGER

```plsql
UTL_RAW.CAST_TO_BINARY_INTEGER (
   r          IN RAW
   endianess  IN PLS_INTEGER DEFAULT BIG_ENDIAN) 
RETURN BINARY_INTEGER;
```

The CAST_TO_BINARY_INTEGER function converts RAW type to INTEGER type.

|Parameter |Description |
| ----------- | ------------------------------------------------------------ |
| r         | BINARY_INTEGER value                                           |
| endianess | Indicates the endianness as a BINARY_INTEGER value, options are as follows:<br/>* 1 or big_endian: indicates big-endian format, this is the default value<br/>* 2 or little_endian: indicates little-endian format<br/>* 3 or machine_endian: indicates the endianness of the YashanDB server |

***Example***

```plsql
SQL> select utl_raw.CAST_TO_BINARY_INTEGER('0000346F', 1) as a from dual;

           A 
------------ 
       13423

1 row fetched.
```

## COMPARE

```plsql
UTL_RAW.COMPARE (
   r1  IN RAW,
   r2  IN RAW,
   pad IN RAW DEFAULT NULL) 
  RETURN NUMBER;
```

The COMPARE function compares two RAW values. If their lengths are different, it extends the shorter RAW to the right using the optional padding parameter.

|Parameter |Description |
| :---- | :----------------------------------------------------------- |
| r1  | RAW type value, can be '' or NULL                             |
| r2  | RAW type value, can be '' or NULL                             |
| `pad` | RAW default NULL for padding the shorter RAW during comparison, default is NULL which indicates '00' |

Function usage rules:

- Compares r1 and r2, returning 0 if they are equal; otherwise, returns the position of the first mismatch.
- If lengths are unequal, the shorter RAW is extended, default padding with '00'.
- Pad can be set to more than one byte but only the first byte is taken.
- r1, r2, and pad can all be '' or NULL; setting pad to '' will still use '00'.

***Example***

```plsql
SQL> select utl_raw.compare('ff0102', 'ff', '0102') from dual;

UTL_RAW.COMPARE('FF0 
-------------------- 
                   3

1 row fetched.

SQL> select utl_raw.compare('ff0002', 'ff') from dual;

UTL_RAW.COMPARE('FF0 
-------------------- 
                   3

1 row fetched.
```

## CONCAT

```plsql
UTL_RAW.CONCAT (  
   r1  IN RAW DEFAULT NULL,
   r2  IN RAW DEFAULT NULL,
   r3  IN RAW DEFAULT NULL,
   r4  IN RAW DEFAULT NULL,
   r5  IN RAW DEFAULT NULL,
   r6  IN RAW DEFAULT NULL,
   r7  IN RAW DEFAULT NULL,
   r8  IN RAW DEFAULT NULL,
   r9  IN RAW DEFAULT NULL,
   r10 IN RAW DEFAULT NULL,
   r11 IN RAW DEFAULT NULL,
   r12 IN RAW DEFAULT NULL) 
  RETURN RAW;
```

The CONCAT function concatenates up to 12 RAW values into one RAW. If the total size exceeds 8000, it returns an error.

|Parameter |Description |
| :---- | :---------------------------------- |
| r1  | RAW type value, can be '' or NULL (optional) |
| r2  | RAW type value, can be '' or NULL (optional) |
| r3  | RAW type value, can be '' or NULL (optional) |
| r4  | RAW type value, can be '' or NULL (optional) |
| r5  | RAW type value, can be '' or NULL (optional) |
| r6  | RAW type value, can be '' or NULL (optional) |
| r7  | RAW type value, can be '' or NULL (optional) |
| r8  | RAW type value, can be '' or NULL (optional) |
| r9  | RAW type value, can be '' or NULL (optional) |
| r10 | RAW type value, can be '' or NULL (optional) |
| r11 | RAW type value, can be '' or NULL (optional) |
| r12 | RAW type value, can be '' or NULL (optional) |

Function usage rules:

- Combines 0–12 RAW values into one RAW; if the total size exceeds 8000, it raises an error.
- Inputting '' or NULL skips that parameter during concatenation.

```plsql
SQL> select utl_raw.concat() from dual;

UTL_RAW.CONCAT()                                                 
---------------------------------------------------------------- 

1 row fetched.

SQL> select utl_raw.concat('','') from dual;

UTL_RAW.CONCAT('',''                                             
---------------------------------------------------------------- 

1 row fetched.

SQL> select utl_raw.concat('01','02') from dual;

UTL_RAW.CONCAT('01',                                             
---------------------------------------------------------------- 
0102                                                            

1 row fetched.
```

## COPIES

```plsql
UTL_RAW.COPIES (
   r IN RAW,
   n IN NUMBER) 
  RETURN RAW;
```

The COPIES function returns n copies of r concatenated together.

|Parameter |Description |
| :--- | :----------------------------- |
| r  | RAW type value, can be '' or NULL   |
| n  | NUMBER type value, number of copies |

Function usage rules:

- Returns n copies of RAW; n must be a positive number.
- If r is NULL or '', or n < 0.5, it raises an error if it exceeds the maximum length.

```plsql
SQL> select utl_raw.copies('ff01', 0) from dual;

[1:31]YAS-05213 invalid argument

SQL> select utl_raw.copies('ff01', 1) from dual;

UTL_RAW.COPIES('FF01                                             
---------------------------------------------------------------- 
FF01                                                            

1 row fetched.
```

## LENGTH

```plsql
UTL_RAW.LENGTH (
   r  IN RAW) 
RETURN NUMBER;
```

The LENGTH function returns the byte length of RAW r.

|Parameter |Description |
| :--- | :-------------------------- |
| r  | RAW type value, can be '' or NULL |

Function usage rules:

- Retrieves the byte length of RAW, returning a number.
- Inputting '' or NULL returns NULL.

```plsql
SQL> select utl_raw.length('') from dual;

UTL_RAW.LENGTH('') 
------------------ 
                 0

1 row fetched.

SQL> select utl_raw.length() from dual;

[1:8]YAS-04323 arguments count must be 1
```

## REVERSE

```plsql
UTL_RAW.REVERSE (
   r IN RAW) 
  RETURN RAW;
```

The REVERSE function reverses the byte sequence of RAW r from one end to the other.

|Parameter |Description |
| :--- | :---------------------------- |
| r  | RAW type value, cannot be '' or NULL |

Function usage rules:

- Reverses the RAW by bytes.
- An input of NULL or '' raises an error.

```plsql
SQL> select utl_raw.reverse('1234') from dual;

UTL_RAW.REVERSE('123                                             
---------------------------------------------------------------- 
3412                                                            

1 row fetched.
```

## SUBSTR

```plsql
UTL_RAW.SUBSTR (
   r   IN RAW,
   pos IN BINARY_INTEGER,
   len IN BINARY_INTEGER DEFAULT NULL) 
  RETURN RAW;
```

The SUBSTR function returns len bytes of RAW, starting from pos in RAW r.

|Parameter |Description |
| :---- | :------------------- |
| r   | RAW type value, cannot be '' or NULL |
| pos | BINARY_INTEGER value, starting position for extracting RAW |
| len | BINARY_INTEGER value (optional), length of bytes to extract |

Function usage rules:

- Retrieves a portion of bytes from RAW, starting at pos for a total of len bytes (up to the end).
- pos is positive, counted from the head, starting at 1; 0 is equivalent to 1, and decimals are floored (ignoring signs).
- pos is negative, counted from the tail.
- len defaults to the end, but cannot be 0; decimals are floored.
- Inputting '' or NULL for r raises an error.

```plsql
SQL> select utl_raw.substr('01020304050607', -7, 7) from dual;

UTL_RAW.SUBSTR('0102                                             
---------------------------------------------------------------- 
01020304050607                                                  

1 row fetched.

SQL> select utl_raw.substr('01020304050607', -7, 0) from dual;

[1:45]YAS-05213 invalid argument

SQL> select utl_raw.substr('01020304050607', 0) from dual;

UTL_RAW.SUBSTR('0102                                             
---------------------------------------------------------------- 
01020304050607                                                  

1 row fetched.
```

## Exception Explanation

|Parameter |Description |
| ----------- | ---------- |
| VALUE_ERROR | Input value error |

***Example***

```plsql
SQL> select utl_raw.cast_to_number('35533922395E405C2E5556101010101010101010101') as a from dual;

YAS-04412 value error, out of range [0, 100)
```
