DBMS_RANDOM包提供了一组内置的存储过程/函数，主要用于生成随机数字与字符。

## INITIALIZE

```plsql
DBMS_RANDOM.INITIALIZE(
   seed IN INTEGER);
```

INITIALIZE函数用于使用一个种子值初始化DBMS_RANDOM包。

示例

```plsql
BEGIN
DBMS_RANDOM.INITIALIZE(100);
END;
/
```

## NORMAL

```plsql
DBMS_RANDOM.NORMAL();
```

NORMAL函数用于返回服从正态分布的随机数，无参数，'()'可省略。

示例
```plsql
SELECT DBMS_RANDOM.NORMAL FROM dual;
DBMS_RANDOM.NORMAL 
------------------ 
        -1.9243543
```


## RANDOM

```plsql
DBMS_RANDOM.RANDOM();
```
RANDOM函数用于返回随机数，无参数，'()'可省略。

示例

```plsql
BEGIN
DBMS_RANDOM.INITIALIZE(100);
FOR i IN 1 .. 5 LOOP
 DBMS_OUTPUT.PUT_LINE(DBMS_RANDOM.RANDOM); 
END LOOP;
END;
/
--result
592679413
2139893194
61410547
1016237248
628080577
```

## SEED

```plsql
DBMS_RANDOM.SEED(
  seed IN INTEGER);
```

SEED函数用于重置种子值。

示例
```plsql
BEGIN
DBMS_RANDOM.SEED('15');
END;
/
```

## STRING

```plsql
DBMS_RANDOM.STRING(
    opt  IN  CHAR,
    len  IN  NUMBER);
```

STRING函数用于随机生成字符串。

| 参数 | 描述                                                         |
| :--- | :----------------------------------------------------------- |
| opt  | 字符串格式：<br>\* 'x','X'：返回包括数字与大写字母的字符串<br>\* 'u','U'：返回只有大写字母的字符串<br/>\* 'l','L'：返回只有小写字母的字符串<br>\* 'a','A'：返回包括大小写字母的字符串<br/>\* 'p','P'：返回任一ASCII码字符 |
| len  | 字符串长度                                                   |

示例

```plsql
SELECT DBMS_RANDOM.STRING('l',10) value FROM dual;
VALUE                                                            
---------------------------------------------------------------- 
elwtenyjkj 

SELECT DBMS_RANDOM.STRING('p',10) value FROM dual;
VALUE                                                            
---------------------------------------------------------------- 
g)6*gN/?Yl 
```

## VALUE

```plsql
DBMS_RANDOM.VALUE (
   low IN NUMBER,
   high IN NUMBER);
```

VALUE函数用于生成一个指定范围的38位随机小数（小数点后38位），参数可省略，表示不指定范围，则生成范围为`[0,1)`的38位随机数。

示例
```plsql
SET num 40;
SELECT DBMS_RANDOM.VALUE FROM dual;
                        DBMS_RANDOM.VALUE 
----------------------------------------- 
  .87566195239211943754742840247867358906

SELECT DBMS_RANDOM.VALUE(1,10) FROM dual;
                     DBMS_RANDOM.VALUE(1, 
----------------------------------------- 
  5.6740327609409654422060040389667274521
```

## TERMINATE

```plsql
DBMS_RANDOM.TERMINATE;
```

TERMINATE函数用于终止对包的使用，此函数已过期，无实际功能。
