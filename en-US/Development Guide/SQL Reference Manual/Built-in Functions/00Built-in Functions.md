A Function is an operation that takes zero or more input parameters, performs specific computations, and returns the result. Its general form can be represented as `function_name(argument1, argument2, ...)`.

Functions support [implicit conversion](../Data Types/Data Type Conversion) of data types: when the input parameter's type does not match the expected parameter type in the Function, YashanDB will first attempt to convert it into an acceptable type before performing the computation. Therefore, in YashanDB, the parameters of a Function can all be general expressions ([expr](../General SQL Syntax/expr)) (unless otherwise specified in the Function definition), and the result of the expression does not have to match the required parameter type, as long as it meets the requirements of the parameter type after the system's implicit conversion.

This chapter introduces the built-in functions in YashanDB, while the PL Reference Manual will cover User-Defined Functions (UDF) for users. You can refer to the V$FUNCTION view to understand all built-in function information provided by YashanDB.


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


[AVG](AVG)

[COUNT](COUNT)

[GROUPING](GROUPING)

[GROUPING\_ID](GROUPING_ID)

[GROUP\_CONCAT](GROUP_CONCAT)

[GROUP\_ID](GROUP_ID)

[LISTAGG](LISTAGG)

[MAX](MAX)

[MEDIAN](MEDIAN)

[MIN](MIN)

[PERCENTILE_CONT](PERCENTILE_CONT)

[STDDEV](STDDEV)

[STDDEV\_POP](STDDEV_POP)

[STDDEV\_SAMP](STDDEV_SAMP)

[STRING\_AGG](STRING_AGG)

[SUM](SUM)

[VARIANCE](VARIANCE)

[VAR\_POP](VAR_POP)

[VAR\_SAMP](VAR_SAMP)

[WM_CONCAT](WM_CONCAT)

<span id="KeepAggregateFunction" name="KeepAggregateFunction"></span>

Keep Aggregate Functions
------------------------------------

The Keep clause combines with the FIRST or LAST field to select specific rows based on sorting rules in grouped queries, and applies aggregate functions to those rows.

The Keep aggregate function functionality is only applicable to HEAP tables in Standalone Deployment and cluster setups.

The general syntax for the Keep clause can be expressed as:

```ebnf
keep_clause = AGGREGATE_FUNC KEEP(DENSE_RANK (FIRST|LAST) order_by_clause).
```

The input parameters and return value specifications of aggregate functions supported by the Keep syntax are the same as those of regular aggregate functions, but DISTINCT cannot be used within the aggregate function in the Keep syntax.

DENSE_RANK is a required keyword used for processing dense rankings after sorting.

FIRST or LAST specifies whether to select the first or last ranked row.

The order_by_clause is used to sort the data rows within the group and cannot be omitted. The syntax is described in the order_by_clause section in the [SELECT](../SQL Statements/SELECT) statement.

The Keep syntax does not allow the use of the OVER PARTITION BY clause.

The Keep syntax supports the following aggregate functions:

[AVG](AVG)

[COUNT](COUNT)

[MIN](MIN)

[MAX](MAX)

[SUM](SUM)

[VARIANCE](VARIANCE)

[STDDEV](STDDEV)

Datetime Functions
-----------------------

Datetime functions are functions that operate on each row of data and return a row of data, with parameters or return results containing date and time data.

YashanDB provides the following datetime functions:

[ADD_MONTHS](ADD_MONTHS)

[AGE](AGE)

[CURRENT_DATE](CURRENT_DATE)

[CURRENT_TIMESTAMP](CURRENT_TIMESTAMP)

[DATE](DATE)

[DATE\_ADD](DATE_ADD)

[DATE_FORMAT](DATE_FORMAT)

[DATE\_SUB](DATE_SUB)

[DAYOFWEEK](DAYOFWEEK)

[DBTIMEZONE](DBTIMEZONE)

[EXTRACT](EXTRACT)

[FROM_TZ](FROM_TZ)

[LAST\_DAY](LAST_DAY)

[MONTHS_BETWEEN](MONTHS_BETWEEN)

[NOW](NOW)

[SESSIONTIMEZONE](SESSIONTIMEZONE)

[SYSDATE](SYSDATE)

[SYSTIMESTAMP](SYSTIMESTAMP)

[TIME](TIME)

[TIMEDIFF](TIMEDIFF)

[TIMESTAMP](TIMESTAMP)

[TIMESTAMPDIFF](TIMESTAMPDIFF)

[TZ\_OFFSET](TZ_OFFSET)

[UTC\_TIMESTAMP](UTC_TIMESTAMP)

Character Functions
------------------------

Character functions operate on each row of data and return a row of data, primarily dealing with character data types.

YashanDB provides the following character functions:

[ASCII](ASCII)

[ASCIISTR](ASCIISTR)

[BIT_LENGTH](BIT_LENGTH)

[CHAR_LENGTH/CHARACTER_LENGTH](CHAR_LENGTH CHARACTER_LENGTH)

[CHR](CHR)

[CONCAT](CONCAT)

[CONCAT_WS](CONCAT_WS)

[FIND_IN_SET](FIND_IN_SET)

[INITCAP](INITCAP)

[INSTR](INSTR)

[INSTRB](INSTRB)

[LEFT](LEFT)

[LENGTH/LENGTHB](LENGTH LENGTHB)

[LENGTH2](LENGTH2)

[LOWER](LOWER)

[LPAD](LPAD)

[LSFA_LISTAGG](LSFA_LISTAGG)

[LTRIM](LTRIM)

[NLSSORT](NLSSORT)

[NLS_CHARSET_DECL_LEN](NLS_CHARSET_DECL_LEN)

[NLS\_LOWER](NLS_LOWER)

[OCTET_LENGTH](OCTET_LENGTH)

[ORA_HASH](ORA_HASH)

[POSITION](POSITION)

[REGEXP\_COUNT](REGEXP_COUNT)

[REGEXP\_INSTR](REGEXP_INSTR)

[REGEXP\_LIKE](REGEXP_LIKE)

[REGEXP\_REPLACE](REGEXP_REPLACE)

[REGEXP\_SUBSTR](REGEXP_SUBSTR)

[REPLACE](REPLACE)

[REVERSE](REVERSE)

[RIGHT](RIGHT)

[RLIKE\_FILTER](RLIKE_FILTER)

[RPAD](RPAD)

[RTRIM](RTRIM)

[SPLIT](SPLIT)

[STRPOS](STRPOS)

[SUBSTR](SUBSTR)

[SUBSTRB](SUBSTRB)

[SUBSTRING](SUBSTRING)

[SUBSTRING_INDEX](SUBSTRING_INDEX)

[TO_BASE64](TO_BASE64)

[TO_MULTI_BYTE](TO_MULTI_BYTE)

[TO_SINGLE_BYTE](TO_SINGLE_BYTE)

[TRANSLATE](TRANSLATE)

[TREAT](TREAT)

[TRIM](TRIM)

[UNISTR](UNISTR)

[UPPER](UPPER)

[CONVERT](CONVERT)

Mathematical Functions
---------------------------

Mathematical functions operate on each row of data and return a row of data, mainly performing mathematical operations on numeric data and returning numeric data.

YashanDB provides the following mathematical functions:

[ABS](ABS)

[ACOS](ACOS)

[ASIN](ASIN)

[ATAN](ATAN)

[ATAN2](ATAN2)

[BITAND/BITOR/BITXOR](BITAND BITOR BITXOR)

[CEIL](CEIL)

[COS](COS)

[COT](COT)

[DIV](DIV)

[EXP](EXP)

[FLOOR](FLOOR)

[LN](LN)

[LOG](LOG)

[MOD](MOD)

[PI](PI)

[POW/POWER](POW POWER)

[RANDOM](RANDOM)

[ROUND](ROUND)

[SIGN](SIGN)

[SIN](SIN)

[SINH](SINH)

[SQRT](SQRT)

[TAN](TAN)

[TANH](TANH)

[TRUNC](TRUNC)

Conversion Functions
-------------------------

Conversion functions operate on each row of data and return a row of data, converting data from one type to another.

YashanDB provides the following conversion functions:

[BIN](BIN)

[BIN_TO_NUM](BIN_TO_NUM)

[CAST](CAST)

[CHARTOROWID](CHARTOROWID)

[HEXTORAW](HEXTORAW)

[NUMTODSINTERVAL](NUMTODSINTERVAL)

[NUMTOYMINTERVAL](NUMTOYMINTERVAL)

[RAWTOHEX](RAWTOHEX)

[ROWIDTOCHAR](ROWIDTOCHAR)

[SCN\_TO\_TIMESTAMP](SCN_TO_TIMESTAMP)

[TIMESTAMP_TO_SCN](TIMESTAMP_TO_SCN)

[TO\_BLOB](TO_BLOB)

[TO\_CHAR](TO_CHAR)

[TO\_CLOB](TO_CLOB)

[TO\_DATE](TO_DATE)

[TO\_DSINTERVAL](TO_DSINTERVAL)

[TO\_NUMBER](TO_NUMBER)

[TO\_TIMESTAMP](TO_TIMESTAMP)

[TO\_TIMESTAMP\_TZ](TO_TIMESTAMP_TZ)

[TO\_YMINTERVAL](TO_YMINTERVAL)

<span id="WindowFunction" name="WindowFunction"></span>

Window Functions
---------------------

A window is a standard SQL term that represents a collection of data rows. Window functions are various functionalities provided by the system to operate on this collection of data rows. The difference between window functions and aggregate functions is that each group of aggregate functions returns one value, while each group of window functions returns multiple values by row.

The general syntax for window functions can be expressed as (where [] indicates optional):

```ebnf
window_function = WINDOW_FUNC([func_parameter]) OVER ([query_partition_clause] [order_by_clause [windowing_clause]]).
```

```ebnf
query_partition_clause = PARTITION BY (expr {"," expr}|("(" expr {"," expr} ")")).
```

query_partition_clause is used to group the table data, which can be omitted to indicate that all data belongs to one group.

expr cannot be NULL, but can be:

*   Column fields
*   Constant expressions: can be any constant value, equivalent to no grouping

```ebnf
order_by_clause = ORDER BY ((expr) [ASC|DESC] [(NULLS FIRST) | (NULLS LAST)]) {"," ((expr) [ASC|DESC] [(NULLS FIRST) | (NULLS LAST)])}.
```

order_by_clause is used to sort the data rows within the group and can be omitted; the syntax is described in the order_by_clause section of the [SELECT](../SQL Statements/SELECT) statement. Here, expr can be any constant value; when it is constant, it is equivalent to no sorting.

```ebnf
windowing_clause = (ROWS|RANGE) ((BETWEEN (UNBOUNDED PRECEDING|CURRENT ROW|value_expr(PRECEDING|FOLLOWING)) AND (UNBOUNDED FOLLOWING|CURRENT ROW|value_expr (PRECEDING|FOLLOWING)))|(UNBOUNDED PRECEDING|CURRENT ROW|value_expr PRECEDING)).
```

windowing_clause is used to further filter the data rows within each group, with each group customizing its window row range using BETWEEN ... AND, i.e., sliding window. Only some window functions can utilize this functionality. Based on this feature, this functionality needs to work in conjunction with order_by_clause to ensure that the filtering basis is an ordered collection of rows.

- ROWS: uses relative offsets from the current row to calculate the row range.
- RANGE: uses the difference in values relative to the current row to calculate the row range; this functionality requires that the order_by_clause sorting values be numeric or datetime types.
- UNBOUNDED PRECEDING: starts from the first row of the group.
- UNBOUNDED FOLLOWING: ends at the last row of the group.
- CURRENT ROW: used as the start or end row.
- value_expr (PRECEDING|FOLLOWING): uses value_expr to calculate the start and end rows (ROWS) or the values of the start and end rows (RANGE); only constants are supported in the LSC table. For example, ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING indicates using the previous row as the start and the next row as the end; the window function performs analytical statistics based on this range.

When using the ROWS keyword in windowing_clause, the following restrictions apply (column represents the end row; row represents the start row; √ indicates supported syntax, × indicates unsupported syntax):

|                           | **UNBOUNDED PRECEDING** | **UNBOUNDED FOLLOWING** | **CURRENT ROW** | **value_expr PRECEDING** | **value_expr FOLLOWING** |
|:--------------------------|:------------------------|:------------------------|:----------------|:-------------------------|:-------------------------|
| **UNBOUNDED PRECEDING**   | ×                       | √                       | √               | √                        | √                        |
| **UNBOUNDED FOLLOWING**   | ×                       | ×                       | ×               | ×                        | ×                        |
| **CURRENT ROW**           | ×                       | √                       | √               | ×                        | √                        |
| **value_expr PRECEDING**  | ×                       | √                       | √               | √                        | √                        |
| **value_expr FOLLOWING**  | ×                       | √                       | ×               | ×                        | √                        |

When using the RANGE keyword in windowing_clause, the following restrictions apply:

-  If the start row is CURRENT ROW, then the end row may only be CURRENT ROW or value_expr/UNBOUNDED FOLLOWING.
- If the start row is value_expr FOLLOWING, then the end row may only be expr/UNBOUNDED FOLLOWING.
- When there are multiple ordering keys in the ORDER BY of the window, the expressions for the start and end rows can only use CURRENT ROW, UNBOUNDED PRECEDING, or UNBOUNDED FOLLOWING.

When using window functions, the following restrictions must be followed:

- Window functions cannot be nested, meaning window functions cannot be used as parameters for other window functions.
- Window functions are generally applied in the select_list of SELECT statements or in the ORDER BY statements.

YashanDB provides the following dedicated window functions:

[DENSE\_RANK](DENSE_RANK)

[FIRST\_VALUE](FIRST_VALUE)

[LAG](LAG)

[LAST\_VALUE](LAST_VALUE)

[LEAD](LEAD)

[RANK](RANK)

[ROW\_NUMBER](ROW_NUMBER)

[RATIO\_TO\_REPORT](RATIO_TO_REPORT)

Additionally, some other types of functions can also be used as window functions, identified by the OVER keyword, such as:

[AVG](AVG)

[COUNT](COUNT)

[LISTAGG](LISTAGG)

[MAX](MAX)

[MEDIAN](MEDIAN)

[MIN](MIN)

[PERCENTILE_CONT](PERCENTILE_CONT)

[SUM](SUM)

[WM_CONCAT](WM_CONCAT)

You can refer to the V$WINDOW_FUNCTION view to understand all window function information provided by YashanDB.

Varray Functions
--------------------

Varray functions refer to functions that operate on arrays ([array variables](../../PL Reference Manual/PL Language Fundamentals/Variables/Collection Variables), [array objects](../../PL Reference Manual/PL Objects/User-Defined Types), [array types](../Data Types/User-Defined Types) or generate array objects, meaning their input parameters or return values must contain at least one array type value.

[ARRAY_APPEND](ARRAY_APPEND)

[ARRAY_LENGTH](ARRAY_LENGTH)

[ARRAY_NDIMS](ARRAY_NDIMS)

[ARRAY_POSITION](ARRAY_POSITION)

[ARRAY_REMOVE](ARRAY_REMOVE)

[ARRAY_REPLACE](ARRAY_REPLACE)

[ARRAY_TO_STRING](ARRAY_TO_STRING)

[ARRAY_UPPER](ARRAY_UPPER)

[STRING_TO_ARRAY](STRING_TO_ARRAY)

<span id="tableFunc" name="tableFunc"></span>

Built-in Table Functions
--------------------

Built-in table functions are a type of function called using the table() syntax, capable of accepting different identifiers as input to perform aggregated queries on different types of data.

Table functions support a maximum expanded row of 65535 bytes.

[PX_CHANNEL](PX_CHANNEL)

[PX_OBJ](PX_OBJ)

[TIME_SERIES_TABLE](TIME_SERIES_TABLE)

Geographic Information Processing Functions (GIS Function)
-------------------------

Geographic information processing functions refer to a series of functions used to process spatial geographic information. For more details, please refer to [GIS Function](GIS Function/00GIS Function).

XML Data Processing Functions (XML Function)
-------------------------

XML data processing functions refer to a series of functions used to handle XML-related data. For more details, please refer to [XML Function](XML Function/00XML Function).

Security Functions
--------------------

Security functions refer to a category of functions that provide functionality for creating secure random numbers, generating keys, signing and verifying signatures, encryption and decryption, and row access control.

YashanDB provides the following security functions:

[CHAR_TO_LABEL](CHAR_TO_LABEL)

[CRYPT_HASH](CRYPT_HASH)

[CRYPT_HMAC](CRYPT_HMAC)

[CRYPT_KEY](CRYPT_KEY)

[CRYPT_RANDOM](CRYPT_RANDOM)

[CRYPT_SIGN](CRYPT_SIGN)

[CRYPT_VERIFY](CRYPT_VERIFY)

[ENCRYPT_AES128](ENCRYPT_AES128)

[DECRYPT_AES128](DECRYPT_AES128)

[CRYPT_ASYM_ENCRYPT](CRYPT_ASYM_ENCRYPT)

[CRYPT_ASYM_DECRYPT](CRYPT_ASYM_DECRYPT)

[CRYPT_ENCRYPT](CRYPT_ENCRYPT)

[CRYPT_DECRYPT](CRYPT_DECRYPT)

[CRYPT_SELFTEST](CRYPT_SELFTEST)

[LABEL_TO_CHAR](LABEL_TO_CHAR)

[SECURITY_CLEAR_CSP](SECURITY_CLEAR_CSP)

[SECURITY_MOD_STATUS](SECURITY_MOD_STATUS)

[SECURITY_MOD_VERSION](SECURITY_MOD_VERSION)

AI Functions
-----------------

AI Functions are functions that process information based on AI models. These functions provide intelligent computing capabilities by invoking registered AI models.

For AI model registration and management, refer to [DBMS_LLM](../../PL Reference Manual/Built-in Advanced PL Packages/DBMS_LLM.md).

YashanDB provides the following AI functions:

[AI_EMBED](AI_EMBED)

[AI_COMPLETE](AI_COMPLETE)

[AI_RERANK](AI_RERANK)

[AI_CLASSIFY](AI_CLASSIFY)

[AI_SENTIMENT](AI_SENTIMENT)

[AI_SUMMARIZE](AI_SUMMARIZE)

[AI_TRANSLATE](AI_TRANSLATE)

Other Functions
--------------------

[BFILENAME](BFILENAME)

[COALESCE](COALESCE)

[CONTAINS](CONTAINS)

[CHECK\_AUDIT\_THRESHOLD](CHECK_AUDIT_THRESHOLD)

[CHECK\_SYS\_PRIVILEGE](CHECK_SYS_PRIVILEGE)

[DECODE](DECODE)

[DUMP](DUMP)

[EMPTY_BLOB](EMPTY_BLOB)

[EMPTY_CLOB](EMPTY_CLOB)

[GET_TYPE_NAME](GET_TYPE_NAME)

[GREATEST](GREATEST)

[IF](IF)

[IFNULL](IFNULL)

[ISNULL](ISNULL)

[JSON](JSON JSON_PARSE) (Alias: JSON_PARSE)

[JSON_ARRAY_GET](JSON_ARRAY_GET)

[JSON_ARRAY_LENGTH](JSON_ARRAY_LENGTH)

[JSON_EXISTS](JSON_EXISTS)

[JSON_QUERY](JSON_QUERY)

[JSON_SERIALIZE](JSON_SERIALIZE JSON_FORMAT) (Alias: JSON_FORMAT)

[JSON_TABLE](JSON_TABLE)

[JSON_VALUE](JSON_VALUE)

[LAST_INSERT_ID](LAST_INSERT_ID)

[LEAST](LEAST)

[LNNVL](LNNVL)

[LOCALTIME](LOCALTIME)

[LOCALTIMESTAMP](LOCALTIMESTAMP)

[MD5](MD5)

[MULTISET](MULTISET)

[NEXT_DAY](NEXT_DAY)

[NULLIF](NULLIF)

[NVL](NVL)

[NVL2](NVL2)

[TYPEOF](TYPEOF)

[UNSUPPORT_ERROR](UNSUPPORT_ERROR)

[USERENV](USERENV)

[SYS_CONNECT_BY_PATH](SYS_CONNECT_BY_PATH)

[SYS_CONTEXT](SYS_CONTEXT)

[SYS_EXTRACT_UTC](SYS_EXTRACT_UTC)

[SYS_GUID](SYS_GUID)

[SQLCODE](SQLCODE)

[SQLERRM](SQLERRM)

[SOUNDEX](SOUNDEX)

[YASDECODE](YASDECODE)

[VALUE](VALUE)
