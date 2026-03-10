Data type conversion often occurs when the input type and the required type do not match, or when an expression contains multiple different data types. In these cases, data types can be integrated through implicit or explicit conversion.

Implicit Data Conversion
------------------------------

Implicit conversion refers to the automatic conversion of data types by YashanDB when the original data type and the target data type are inconsistent and the user has not specified a type conversion function. YashanDB applies a set of conversion rules to attempt to convert data types, helping users reduce the difficulty of writing SQL statements. However, if the conversion exceeds the default conversion range, the corresponding SQL statement will result in an error.

YashanDB defaults to implicit conversion in the following scenarios:

* In INSERT/UPDATE statements, the data to be inserted will be converted to the target column type.
* During arithmetic operations, data types involved in the operation will be converted.
* In comparison operations, if the data types involved in the operation are inconsistent, conversion will occur.
* When the input parameter to a function does not match its defined data type, the parameter will be implicitly converted to an acceptable data type.
* In PL or interface programs, when the filter condition uses binding parameters and the input parameter does not match the binding column data type, the parameter will first be implicitly converted to an acceptable data type. (When the binding column is numeric, the input parameter will be implicitly converted to NUMBER type.)

Executing implicit conversions can lead to the following issues:

* FLOAT/DOUBLE types are imprecise data types; operations involving them may lead to precision discrepancies.
* Converting from TIMESTAMP to DATE will discard microseconds.

The table below lists all implicit conversions supported by YashanDB: (The header represents the original type; the row headers represent the target type; ✓ indicates support for conversion, X indicates no support, -- indicates no conversion needed.)

(1)

|Original Type\Target Type |TINYINT |SMALLINT |INT |BIGINT |NUMBER |FLOAT |DOUBLE |Character Type |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **TINYINT** | \-- | ✓   | ✓   | ✓   | ✓   | ✓   | ✓   | ✓   |
| **SMALLINT** | ✓   | \-- | ✓   | ✓   | ✓   | ✓   | ✓   | ✓   |
| **INT** | ✓   | ✓   | \-- | ✓   | ✓   | ✓   | ✓   | ✓   |
| **BIGINT** | ✓   | ✓   | ✓   | \-- | ✓   | ✓   | ✓   | ✓   |
| **NUMBER** | ✓   | ✓   | ✓   | ✓   | \-- | ✓   | ✓   | ✓   |
| **FLOAT** | ✓   | ✓   | ✓   | ✓   | ✓   | \-- | ✓   | ✓   |
| **DOUBLE** | ✓   | ✓   | ✓   | ✓   | ✓   | ✓   | \-- | ✓   |
| **Character Type** | ✓   | ✓   | ✓   | ✓   | ✓   | ✓   | ✓   | \-- |
| **DATE** | X   | X   | X   | X   | X   | X   | X   | ✓   |
| **TIMESTAMP** | X   | X   | X   | X   | X   | X   | X   | ✓   |
| **LTZ** | X   | X   | X   | X   | X   | X   | X   | ✓   |
| **TZ** | X   | X   | X   | X   | X   | X   | X   | ✓   |
| **YM_INTERVAL** | X   | X   | X   | X   | X   | X   | X   | ✓   |
| **DS_INTERVAL** | X   | X   | X   | X   | X   | X   | X   | ✓   |
| **TIME** | X   | X   | X   | X   | X   | X   | X   | ✓   |
| **BOOLEAN** | ✓   | ✓   | ✓   | ✓   | ✓   | X   | X   | ✓<sup>①</sup>   |
| **BIT** | ✓ | ✓ | ✓ | ✓ | ✓ | X | X | ✓ |
| **CLOB** | X | X | X | X | X | X | X | ✓ |
| **BLOB** | X | X | X | X | X | X | X | ✓ |
| **NCLOB** | X | X | X | X | X | X | X | ✓ |
| **RAW** | X | X | X | X | X | X | X | ✓ |
| **JSON** | X | X | X | X | X | X | X | ✓ |
| **ROWID** | X | X | X | X | X | X | X | ✓ |
| **UROWID** | X | X | X | X | X | X | X | ✓ |
| **BFILE** | X | X | X | X | X | X | X | ✓ |

(2)

|Original Type\Target Type |DATE |TIMESTAMP |LTZ |TZ |YM_INTERVAL |DS_INTERVAL |TIME |BOOLEAN |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **TINYINT** | X   | X   | X   | X   | X   | X   | X   | ✓   |
| **SMALLINT** | X   | X   | X   | X   | X   | X   | X   | ✓   |
| **INT** | X   | X   | X   | X   | X   | X   | X   | ✓   |
| **BIGINT** | X   | X   | X   | X   | X   | X   | X   | ✓   |
| **NUMBER** | X   | X   | X   | X   | X   | X   | ✓   | ✓   |
| **FLOAT** | X   | X   | X   | X   | X   | X   | ✓   | X   |
| **DOUBLE** | X   | X   | X   | X   | X   | X   | ✓   | X   |
| **Character Type** | ✓   | ✓   | ✓   | ✓   | ✓   | ✓   | ✓   | ✓<sup>①</sup>   |
| **DATE** | \-- | ✓   | ✓   | ✓   | X   | X   | ✓   | X   |
| **TIMESTAMP** | ✓   | \-- | ✓   | ✓   | X   | X   | ✓   | X   |
| **LTZ** | ✓   | ✓   | \-- | ✓   | X   | X   | ✓   | X   |
| **TZ** | ✓   | ✓   | ✓   | \-- | X   | X   | ✓   | X   |
| **YM_INTERVAL** | X   | X   | X   | X   | \-- | X   | X   | X   |
| **DS_INTERVAL** | X   | X   | X   | X   | X   | \-- | ✓   | X   |
| **TIME** | ✓   | ✓   | ✓   | ✓   | X   | ✓   | \-- | X   |
| **BOOLEAN** | X   | X   | X   | X   | X   | X   | ✓   | \-- |
| **BIT** | X | X | X | X | X | X | X | ✓ |
| **CLOB** | X | X | X | X | X | X | X | X |
| **BLOB** | X | X | X | X | X | X | X | X |
| **NCLOB** | X | X | X | X | X | X | X | X |
| **RAW** | X | X | X | X | X | X | X | X |
| **JSON** | X | X | X | X | X | X | X | X |
| **ROWID** | X | X | X | X | X | X | X | X |
| **UROWID** | X | X | X | X | X | X | X | X |
| **BFILE** | X | X | X | X | X | X | X | X |

(3)

|Original Type\Target Type |BIT |CLOB |BLOB |NCLOB |RAW |JSON |ROWID |UROWID |BFILE |
| --------------- | ---- | ---- | ---- | ----- | ---- | ---- | ----- | ------ |------ |
| **TINYINT**     | ✓    | ✓    | X    | ✓     | X    | X    | X     | X      |  X      |
| **SMALLINT**    | ✓    | ✓    | X    | ✓     | X    | X    | X     | X      | X      |
| **INT**         | ✓    | ✓    | X    | ✓     | X    | X    | X     | X      | X      |
| **BIGINT**      | ✓    | ✓    | X    | ✓     | X    | X    | X     | X      | X      |
| **NUMBER**      | ✓    | ✓    | X    | ✓     | X    | X    | X     | X      | X      |
| **FLOAT**       | X    | ✓    | X    | ✓     | X    | X    | X     | X      | X      |
| **DOUBLE**      | X    | ✓    | X    | ✓     | X    | X    | X     | X      | X      |
| **Character Type**      | ✓    | ✓    | ✓    | ✓     | ✓    | ✓    | ✓    | ✓      | X      |
| **DATE**        | X    | X    | X    | X     | X    | X    | X     | X      | X      |
| **TIMESTAMP**   | X    | X    | X    | X     | X    | X    | X     | X      | X      |
| **LTZ**   | X    | X    | X    | X     | X    | X    | X     | X      | X      |
| **TZ**   | X    | X    | X    | X     | X    | X    | X     | X      | X      |
| **YM_INTERVAL** | X    | X    | X    | X     | X    | X    | X     | X      | X      |
| **DS_INTERVAL** | X    | X    | X    | X     | X    | X    | X     | X      | X      |
| **TIME**        | X    | X    | X    | X     | X    | X    | X     | X      | X      |
| **BOOLEAN**     | ✓    | X    | X    | X     | X    | X    | X     | X      | X      |
| **BIT**         | --   | X    | X    | X     | X    | X    | X     | X      | X      |
| **CLOB**        | X    | --   | X    | ✓     | X    | ✓    | X     | X      | X      |
| **BLOB**        | X    | X    | --   | X     | ✓    | ✓    | X     | ✓      | X      |
| **NCLOB**       | X    | ✓    | X    | --    | X    | ✓    | X     | X      | X      |
| **RAW**         | X    | ✓    | ✓    | ✓    | --   | ✓    | ✓     | ✓      | X      |
| **JSON**        | X    | ✓    | ✓    | ✓     | ✓    | --   | X     | ✓      | X      |
| **ROWID**       | X    | X    | X    | X     | ✓    | X    | --    | ✓      | X      |
| **UROWID**      | X    | ✓    | ✓    | ✓     | ✓    | ✓    | ✓     | --     | X      |
| **BFILE**       | X    | ✓    | ✓    | X     | X    | ✓    | X     | X     |--     |

- ① Only specific strings are supported for conversion to BOOLEAN type; for details, please refer to [Boolean Type](Boolean Type).

Explicit Data Conversion
------------------------------

Explicit conversion specifies the direction of conversion clearly using type conversion functions. Compared to implicit conversion, explicit conversion makes SQL statements easier to understand and increases the predictability of output types.

The table below illustrates some built-in type conversion functions in YashanDB:

|Function |Functionality |
|------------------------------------------------|---------------------------------|
| [BIN](../Built-in Functions/BIN)                             | Convert other types to BIT type (the result is hosted as VARCHAR type).                   |
| [CAST](../Built-in Functions/CAST)                           | Specify conversion to any type.                      |
| [NUMTODSINTERVAL](../Built-in Functions/NUMTODSINTERVAL)     | Convert numeric types to INTERVAL DAY TO SECOND type. |
| [NUMTOYMINTERVAL](../Built-in Functions/NUMTOYMINTERVAL)     | Convert numeric types to INTERVAL YEAR TO MONTH type. |
| [ROWIDTOCHAR](../Built-in Functions/ROWIDTOCHAR)             | Convert ROWID type to VARCHAR type.                      |
| [SCN_TO_TIMESTAMP](../Built-in Functions/SCN_TO_TIMESTAMP) | Convert BIGINT type to TIMESTAMP type.         |
| [TIMESTAMP_TO_SCN](../Built-in Functions/TIMESTAMP_TO_SCN) | Convert TIMESTAMP type to BIGINT type.         |
| [TO_CHAR](../Built-in Functions/TO_CHAR)                    | Convert other types to VARCHAR type.               |
| [TO_DATE](../Built-in Functions/TO_DATE)                | Convert other types to DATE type.                |
| [TO_DSINTERVAL](../Built-in Functions/TO_DATE)                    | Convert other types to INTERVAL DAY TO SECOND type.                  |
| [TO_NUMBER](../Built-in Functions/TO_NUMBER)                | Convert other types to NUMBER type.                |
| [TO_TIMESTAMP](../Built-in Functions/TO_TIMESTAMP)          | Convert other types to TIMESTAMP type.             |
| [TO_TIMESTAMP_TZ](../Built-in Functions/TO_TIMESTAMP_TZ)   | Convert other types to TIMESTAMP WITH TIME ZONE type.|
| [TO_YMINTERVAL](../Built-in Functions/TO_YMINTERVAL)        | Convert other types to INTERVAL YEAR TO MONTH type. |
| [JSON](../Built-in Functions/JSON JSON_PARSE)                           | Convert string type to JSON type.                 |
| [JSON_SERIALIZE](../Built-in Functions/JSON_SERIALIZE JSON_FORMAT)      | Convert JSON type to string type.                 |