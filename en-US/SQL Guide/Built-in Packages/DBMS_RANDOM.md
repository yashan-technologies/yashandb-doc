The DBMS_RANDOM package provides a set of built-in procedures/functions mainly used for generating random numbers and characters.

## INITIALIZE

```plsql
DBMS_RANDOM.INITIALIZE(
   seed IN INTEGER);
```

The INITIALIZE function is used to initialize the DBMS_RANDOM package with a seed value.

***Example***

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

The NORMAL function returns a random number that follows a normal distribution, with no parameters, and '()' can be omitted.

***Example***
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

The RANDOM function returns a random number, with no parameters, and '()' can be omitted.

***Example***

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

The SEED function is used to reset the seed value.

***Example***
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

The STRING function is used to generate a random string.

|Parameter |Description |
| :--- | :----------------------------------------------------------- |
| opt       | String formats:<br>\* 'x','X': Returns a string including numbers and uppercase letters<br>\* 'u','U': Returns a string with only uppercase letters<br/>\* 'l','L': Returns a string with only lowercase letters<br>\* 'a','A': Returns a string including both uppercase and lowercase letters<br>\* 'p','P': Returns any ASCII character |
| len       | Length of the string                                          |

***Example***

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

The VALUE function is used to generate a 38-digit random decimal number (38 decimal places) within a specified range. Parameters can be omitted, which means the range is `[0,1)` to generate a 38-digit random number.

***Example***
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

The TERMINATE function is used to terminate the use of the package. This function is deprecated and has no actual functionality.