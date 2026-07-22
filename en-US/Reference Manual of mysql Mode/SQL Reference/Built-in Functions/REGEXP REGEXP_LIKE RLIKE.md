```ebnf
regexp_like = REGEXP_LIKE "(" expr "," regexp ["," match_para] ")".
```

REGEXP_LIKE is a regular expression function that matches the target string `regexp` against the source string [expr](../General SQL Syntax/expr) according to the regular pattern and returns the match result. The return value is of BIGINT type, returning 1 when the match is successful, otherwise returning 0.

[REGEXP/RLIKE Condition](../General SQL Syntax/condition) are synonyms of REGEXP_LIKE() function, differing only in the return data type. REGEXP and RLIKE condition oparations return a data type of TINYINT.

**expr**

A general expression whose value must be of character type or another type that can be converted to character type.

- `expr` cannot be TEXT data that exceeds 65534 bytes.

- The function uses character calculations defined by the input character set (only supports UTF-8).

- When the value of `expr` is NULL, the function returns NULL.

**regexp**

Specifies a [RegExp](../General SQL Syntax/RegExp) with a length not exceeding 512 bytes.

- `regexp` does not support POSIX character.

- When `regexp` is NULL, the function returns NULL.

**match\_para**

Regex matching parameters, which are optional or can be specified as NULL, with a default value of 'c'. The optional values are as follows:

- 'i': Case insensitive.

- 'c': case sensitive.

- 'n': Allows the dot (.) to match any character, including newline characters. By default, the dot does not match newline characters.

- 'm': Treats the string as multiline, interpreting ^ and $ as the beginning and end of any line in the string, rather than just the start or end of the entire string. By default, the string is treated as a single line.

- 'x': Ignores whitespace and # comments. By default, whitespace characters match themselves.

If the `match_para` parameter is specified with a value other than the optional ones, the function will return error YAS-07202.

When `match_para` is omitted, it checks whether the collation of `expr` and `regexp` is specified, and performs regular expression matching based on the case sensitivity of the collation. If no collation is specified for `expr` and `regexp`, the system default collation utf8mb4_general_ci (case-insensitive) is used.

***Example*** for Standalone Deployment Heap tables

```sql
-- During numeric-to-character conversion, ".0" is removed

select regexp_like(1, 1.0), regexp_like(0, 0.0);

  regexp_like(1, 1.0)   regexp_like(0, 0.0)
--------------------- ---------------------
                    1                     1

select (1 rlike 1.0), (0 rlike 0.0);

1 rlike 1.0 (0 rlike 0.0)
----------- -------------
          1             1

select cast(1e5 as char(10)) res1, regexp_like(1e5, 100000) res2, 
cast(1.0e5 as char(10)) res3, regexp_like(1.0e5, 100000) res4;

res1                          res2 res3                           res4
------------ --------------------- ------------- ---------------------
1.0E+005                         0 1.0E+005                          0

select cast(1e5 as char(10)) res1, (1e5 rlike 100000) res2, 
cast(1.0e5 as char(10)) res3, (1.0e5 rlike 100000) res4;

res1            res2 res3                 res4
-------------- ----- ------------------- -----
1.0E+005           0 1.0E+005                0


SELECT REGEXP_LIKE('aa\naa','^aa$','i') reg1, 
REGEXP_LIKE('aa','^aa$','i') reg2 FROM DUAL;

                 reg1                  reg2
--------------------- ---------------------
                    0                     1

SELECT ('aa\naa' rlike '^aa$') reg1, ('aa' rlike '^aa$') reg2 FROM DUAL;

 reg1  reg2
----- -----
    0     1


SELECT REGEXP_LIKE('AA','A.A') reg1, 
REGEXP_LIKE('AA','A.A','n') reg2 FROM DUAL;

                 reg1                  reg2
--------------------- ---------------------
                    0                     0

SELECT ('AA' rlike 'A.A') reg1, ('AA' rlike 'A.A') reg2 FROM DUAL;

 reg1  reg2
----- -----
    0     0

SELECT REGEXP_LIKE('-654196584','^-[0-9]*[1-9][0-9]*$') reg1, 
REGEXP_LIKE('654196584','^[0-9]*[1-9][0-9]*$','m') reg2 FROM DUAL;

                 reg1                  reg2
--------------------- ---------------------
                    1                     1

SELECT ('-654196584' rlike '^-[0-9]*[1-9][0-9]*$') reg1, 
('654196584' rlike '^[0-9]*[1-9][0-9]*$') reg2 FROM DUAL;

 reg1  reg2
----- -----
    1     1

```
