This section mainly introduces the built-in functions parsed according to MySQL syntax in YashanDB (mysql mode) (the function list can be queried via [V$MYSQL_FUNCTION](../../../Reference Manual/System Views/Dynamic Performance Views/V$MYSQL_FUNCTION)). The built-in functions with the same name in the yashan mode mentioned in [Built-in Functions in yashan mode](../../../Development Guide/SQL Reference Manual/Built-in Functions/00Built-in Functions) still use the yashan syntax for parsing.

Database Management Functions
------------------------

Database management functions are used to obtain current session information and global information.

YashanDB provides the following management functions:

[CHECK_USER_PRIVILEGE](CHECK_USER_PRIVILEGE)

[COERCIBILITY](COERCIBILITY)

[COLLATION](COLLATION)

[CONNECTION_ID](CONNECTION_ID)

[CURRENT_USER](CURRENT_USER)

[DATABASE](DATABASE)

[LOAD_FILE](LOAD_FILE)

[ROW_COUNT](ROW_COUNT)

[SCHEMA](SCHEMA)

[SESSION_USER](SESSION_USER)

[SLEEP](SLEEP)

[SYSTEM_USER](SYSTEM_USER)

[USER](USER)

[VALIDATE_PASSWORD_STRENGTH](VALIDATE_PASSWORD_STRENGTH)

[VALUES](VALUES)

[VERSION](VERSION)


Aggregate Functions
------------------------

An aggregate function is a function that operates on multiple rows of data and returns a single row of data. This function can appear in SELECT, ORDER BY, and HAVING statements, and is typically used in conjunction with the GROUP BY statement.

* When there is no GROUP BY statement specified, the aggregate function computes over all rows and returns only one computed result.
* When a GROUP BY statement is specified, the aggregate function performs group-wise computations based on the values following the GROUP BY clause and returns one computed result for each value.
* When both a GROUP BY statement and a HAVING statement are specified, the multiple computed results returned by the previous operation are filtered according to the conditions following the HAVING clause before being returned.

When using aggregate functions, the following points should be noted:

* Aggregate functions are deterministic; they always return the same value when called with a given set of input values.
* An aggregate function must take exactly one input parameter. For example, `COUNT(c1)`.
* Except for `COUNT(*)` and `COUNT(constant)`, other aggregate functions ignore NULL values; when all rows are NULL, these functions return NULL.
* Aggregate functions are not allowed to be combined with FOR UPDATE statements.
* Some aggregate functions can be used as window functions by using the OVER keyword; details can be found in the respective function sections.

YashanDB provides the following aggregate functions:


[ANY_VALUE](ANY_VALUE)

[AVG](AVG)

[COUNT](COUNT)

[GROUP_CONCAT](GROUP_CONCAT)

[MAX](MAX)

[MIN](MIN)

[STDDEV](STDDEV)

[STDDEV_POP](STDDEV_POP)

[STDDEV_SAMP](STDDEV_SAMP)

[SUM](SUM)

[VARIANCE](VARIANCE)

[VAR_POP](VAR_POP)

[VAR_SAMP](VAR_SAMP)

Datetime Functions
--------------------

Datetime functions are functions that operate on each row of data and return a row of data, with parameters or return results containing date and time data.

YashanDB provides the following datetime functions:

[ADDDATE](ADDDATE)

[ADDTIME](ADDTIME)

[CURDATE](CURDATE)

[CURRENT_DATE](CURDATE)

[CURTIME](CURTIME)

[CURRENT_TIME](CURTIME)

[CURRENT_TIMESTAMP](CURRENT_TIMESTAMP)

[DATE](DATE)

[DATEDIFF](DATEDIFF)

[DATE_ADD](DATE_ADD)

[DATE_FORMAT](DATE_FORMAT)

[DATE_SUB](DATE_SUB)

[DAYOFMONTH](DAY)

[DAYOFWEEK](DAYOFWEEK)

[DAYOFYEAR](DAYOFYEAR)

[DAY](DAY)

[DAYNAME](DAYNAME)

[EXTRACT](EXTRACT)

[FROM_DAYS](FROM_DAYS)

[FROM_UNIXTIME](FROM_UNIXTIME)

[GET_FORMAT](GET_FORMAT)

[HOUR](HOUR)

[LAST_DAY](LAST_DAY)

[MICROSECOND](MICROSECOND)

[MINUTE](MINUTE)

[MONTH](MONTH)

[MONTHNAME](MONTHNAME)

[NOW](NOW)

[QUARTER](QUARTER)

[SECOND](SECOND)

[SEC_TO_TIME](SEC_TO_TIME)

[STR_TO_DATE](STR_TO_DATE)

[SUBDATE](SUBDATE)

[SUBTIME](SUBTIME)

[SYSDATE](SYSDATE)

[TIME](TIME)

[TIMEDIFF](TIMEDIFF)

[TIMESTAMP](TIMESTAMP)

[TIMESTAMPADD](TIMESTAMPADD)

[TIMESTAMPDIFF](TIMESTAMPDIFF)

[TIME_TO_SEC](TIME_TO_SEC)

[TIME_FORMAT](TIME_FORMAT)

[TO_DAYS](TO_DAYS)

[TO_SECONDS](TO_SECONDS)

[UTC_DATE](UTC_DATE)

[UTC_TIME](UTC_TIME)

[UTC_TIMESTAMP](UTC_TIMESTAMP)

[WEEK](WEEK)

[WEEKDAY](WEEKDAY)

[WEEKOFYEAR](WEEKOFYEAR)

[YEAR](YEAR)

[YEARWEEK](YEARWEEK)


Character Functions
------------------------

Character functions operate on each row of data and return a row of data, primarily dealing with character data types.

YashanDB mysql mode provides the following character functions:

[ASCII](ASCII)

[BIT_LENGTH](BIT_LENGTH)

[CHAR](CHAR)

[CHARSET](CHARSET)

[CHAR_LENGTH/CHARACTER_LENGTH](CHAR_LENGTH CHARACTER_LENGTH)

[CONCAT](CONCAT)

[CONCAT_WS](CONCAT_WS)

[CONVERT](CONVERT)

[CONVERT_TZ](CONVERT_TZ)

[FIND_IN_SET](FIND_IN_SET)

[FORMAT](FORMAT)

[INSTR](INSTR)

[LCASE](LCASE)

[LEFT](LEFT)

[LENGTH](LENGTH)

[LOWER](LOWER)

[LPAD](LPAD)

[LTRIM](LTRIM)

[LOCATE](LOCATE)

[OCTET_LENGTH](OCTET_LENGTH)

[POSITION](POSITION)

[REPLACE](REPLACE)

[REVERSE](REVERSE)

[RIGHT](RIGHT)

[RPAD](RPAD)

[RTRIM](RTRIM)

[SPACE](SPACE)

[STRCMP](STRCMP)

[SUBSTR](SUBSTR)

[SUBSTRING](SUBSTRING)

[SUBSTRING_INDEX](SUBSTRING_INDEX)

[TRIM](TRIM)

[TRUNCATE](TRUNCATE)

[UCASE](UCASE)

[UPPER](UPPER)

Mathematical Functions
---------------------------

Mathematical functions operate on each row of data and return a row of data, mainly performing mathematical operations on numeric data and returning numeric data.

YashanDB provides the following mathematical functions:

[ABS](ABS)

[ACOS](ACOS)

[ASIN](ASIN)

[ATAN](ATAN)

[ATAN2](ATAN2)

[CEIL](CEIL)

[CEILING](CEILING)

[COS](COS)

[COT](COT)

[EXP](EXP)

[FLOOR](FLOOR)

[LN](LN)

[LOG](LOG)

[MOD](MOD)

[PI](PI)

[POW](POW)

[POWER](POW)

[RAND](RAND)

[ROUND](ROUND)

[SIGN](SIGN)

[SIN](SIN)

[SQRT](SQRT)

[TAN](TAN)

Conversion Functions
-------------------------

Conversion functions operate on each row of data and return a row of data, converting data from one type to another.

YashanDB provides the following conversion functions:

[BIN](BIN)

[CAST](CAST)

[HEX](HEX)

[OCT](OCT)

[UNHEX](UNHEX)

Other Functions
--------------------

[CASE](CASE)

[CRC32](CRC32)

[DECODE](DECODE)

[ENCODE](ENCODE)

[FROM_BASE64](FROM_BASE64)

[IF](IF)

[IFNULL](IFNULL)

[JSON_EXTRACT](JSON_EXTRACT)

[LAST_INSERT_ID](LAST_INSERT_ID)

[LOCALTIME](LOCALTIME)

[LOCALTIMESTAMP](LOCALTIMESTAMP)

[MD5](MD5)

[NULLIF](NULLIF)

[UUID](UUID)

[UUID_SHORT](UUID_SHORT)
