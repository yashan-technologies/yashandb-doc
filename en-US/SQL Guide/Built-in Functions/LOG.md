```ebnf+diagram
log::= LOG "(" expr1 "," expr2 ")"
```

The LOG function calculates the logarithm of expr2 with expr1 as the base, returning a DOUBLE type value.

Both expr1 and expr2 are general expressions recognized by YashanDB, which must be numerical or convertible character strings to NUMBER type (conversion failure returns Invalid number error). The function does not support other types.

When the value of either expr1 or expr2 is NULL, the function returns NULL.

According to the mathematical concept of logarithms, expr1 should be a positive number other than 0 and 1, while expr2 can be any positive number. The handling rules for other situations are as follows:

|expr2 value\expr1 value |Non-0/1 Positive |-Inf |Negative |0 |1 |Inf |Nan |-Nan |
| :-------------: | :-------: | :--: | :--: | :--: | :--: | :--: | :--: | ---- |
|        **Positive**      |       Logarithm  | Nan  |    Nan   |  0   | Inf  |  0   | Nan  | Nan  |
|    **-Inf**     |    Nan    | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  |
|         **Negative**     |        Nan       | Nan  |    Nan   | Nan  | Nan  | Nan  | Nan  | Nan  |
|      **0**      |   -Inf    | Nan  | Nan  | Nan  | -Inf | Nan  | Nan  | Nan  |
|     **Inf**     |    Inf    | Nan  | Nan  | Nan  | Inf  | Nan  | Nan  | Nan  |
|     **Nan**     |    Nan    | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  |
|    **-Nan**     |    Nan    | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  | Nan  |

***Example***

```sql
SELECT LOG(2,4) res FROM DUAL;
RES
-----------
2.0E+000

SELECT LOG(b'10',b'100') res FROM DUAL;
RES
-----------
2.0E+000

DROP TABLE IF EXISTS number_fd;
CREATE TABLE number_fd(numberf FLOAT, numberd DOUBLE);
INSERT INTO number_fd VALUES(0,5.55);
INSERT INTO number_fd VALUES(1,5.55);
INSERT INTO number_fd VALUES(2,-5.55);
INSERT INTO number_fd VALUES(2,0);
INSERT INTO number_fd VALUES('2','Inf');
INSERT INTO number_fd VALUES('2','-Inf');
INSERT INTO number_fd VALUES('2','Nan');
INSERT INTO number_fd VALUES('2','-Nan');

SELECT LOG(numberf,numberd) res1, LOG(numberd,numberf) res2 FROM number_fd;
       RES1        RES2 
----------- ----------- 
          0        -Inf
        Inf           0
        Nan         Nan
       -Inf           0
        Inf           0
        Nan         Nan
        Nan         Nan
        Nan         Nan
```
