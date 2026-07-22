
JSON is a data interchange format independent of programming languages, represented in a text format to express data. Supporting JSON format in database systems can make data transfer between applications and databases more straightforward, understandable, and logically structured.

YashanDB supports defining and calculating JSON data using standard JSON format, including its two structural forms and six data types; it also supports using the [JSON extended format](#json-ext) to extend support for other data types in the database.

For a detailed description of the standard JSON syntax, please refer to its official website [ECMA-404 The JSON Data Interchange Standard](https://www.json.org/json-en.html).

## JSON Structural Forms

**Key-Value Pair Form**

This structure of JSON data is an unordered collection of "Key:Value" pairs that starts with `{` and ends with `}`, with multiple pairs separated by `,`. As shown below:

```json
{
    "key1": "abc",
    "key2": 123,
    "key3": true,
    "key4": null
}
```

**Array Form**

This structure of JSON data is an ordered collection of values, starting with `[` and ending with `]`, with values separated by commas `,`. As shown below:

```json
[
	"ghi", 
	789
]
```

**Multi-Structure Nested Mixing**

A JSON data can consist of multiple nested and mixed structural forms to meet various organizational requirements of business data. As shown below:

```json
{
    "key1": "abc",
    "key2": 123,
    "key3": true,
    "key4": null,
    "key5": [
        "def", 
        456, 
        false, 
        null, 
       	[
            "ghi", 
            789
        ], 
        {
            "key": "value"
        }
    ],
	"key6": {
        "key": "value"
    }
}
```

## JSON Data Types

According to the standard JSON format definition, the values in JSON data can be of the following types:

- Object
- Array
- String
- Number
- Boolean (True/False)
- Null

Among them:

1. Values in objects and arrays can be strings, numbers, booleans, null, or nested objects and arrays.
2. Strings are sequences of zero or more Unicode characters, marked with double quotes `"`, and supporting escape with a backslash `\`.
3. Boolean and null must be input in all lowercase, namely true/false/null.

<span id="json-ext" name="json-ext"></span>

### JSON Extended Format

In the JSON extended format, the values in JSON data can additionally include the following types:

- Tinyint
- Smallint
- Integer
- Bigint
- Float
- Double
- Number
- Binary
- Timestamp
- Date
- Time

<span id="json-ext-parse" name="json-ext-parse"></span>

#### JSON Extended Format Parsing

All supported extended formats can be represented using an object containing a single key-value pair, in the format: `{"$key": value}`.

Among them:

1. Tinyint, Smallint, Integer, Bigint, Float, Double, and Number:
   - The values of these types need to be either a string or a number without quotes. Both cases must comply with the parsing requirements for this type.
      - For integer types (Tinyint, Smallint, Integer, Bigint), the value must be a string containing only digits and signs, with no decimal points or exponents allowed.
      - For floating-point types (Float, Double), values can be "nan" or "inf" (case insensitive) with a positive or negative sign, or a numeric string.
   - These types are equivalent to the namesake data types in YashanDB, including their length and value range. See [Data Type Specifications](../../../Product Overview/Specifications/Data Type Specifications).
   - The keys for these types are as follows:
      - The key for Tinyint is `$numberByte`.
      - The key for Smallint is `$numberShort`.
      - The key for Integer is `$numberInteger`.
      - The key for Bigint is `$numberLong`.
      - The key for Float is `$numberFloat`.
      - The key for Double is `$numberDouble`.
      - The key for Number is `$numberDecimal`.
2. Binary: 
   - The key for this type is `$binary`.
   - The value can either be a base64 encoded string or an object with only the following two keys:
      - `base64`, whose value is a base64 encoded string;
      - `subType`, whose value is a Tinyint number used for syntax compatibility without practical significance.
   - The base64 encoded string can omit trailing padding character `=`.
   - If the string length is illegal (4n + 1), the last character will be ignored.
3. Timestamp, Date, and Time:
   - Timestamp: 
     - The key is `$yashanTimestamp`.
     - The value is a string that meets the ISO-8601 SCN standard (timezone unsupported): `yyyy-mm-ddThh24:mi:ss.ff`.
         - According to the standard, the letter `T` or a space can match `T`.
         - According to the standard, a period `.` or a comma `,` can match `.`.

      - For other date formats, see [Date-Time Types](../Data Types/Date-Time Types).



    - Date: 
      - The key is `$yashanDate`.
      - The value is a string that meets the ISO-8601 date format requirement (timezone unsupported): `yyyy-mm-ddThh24:mi:ss.ff`.
         - Since the Date precision is to seconds, milliseconds data (`.ff`, highest supporting 9 decimal places) will be ignored.
         - Other formats are the same as Timestamp.
    - Time:
      - The key is `$yashanTime`.
      - The value is a string that meets the ISO-8601 time format requirement: `Thh24:mi:ss.ff`.

When matching an object with supported extended formats, the object will only be parsed into its corresponding extended format data if the match is completely successful; otherwise, it will still be parsed as an object.

Common matching errors include:

- When matching integer types (Tinyint, Smallint, Integer, Bigint):
  - The input value contains illegal characters.
  - The input value exceeds the range that this type can express.
- When matching the Number type and the input value is infinite or NaN; note that the Number type does not support such values, but Float and Double types do.
- When matching the Binary type, the input base64 encoded string does not meet its encoding requirements (e.g., `{"$binary": "«»"}`).
- The input string does not meet other parsing requirements.

When in extended mode, the JSON function will further process numeric data without an explicitly indicated type:  
1. If a value is an integer, it attempts to express it in a smaller data format, trying Tinyint < Smallint < Integer < Bigint in order. For each data type, if the number falls within the range that it can express, the numeric data type will be parsed as this data type.  
2. If a value is a decimal, it will be parsed as Double type.  
3. In other cases, the value will still be stored as Number type.

***Example***
```sql
SELECT JSON('{"$numberByte": "-128"}' EXTENDED) BYTE,
       JSON('{"$numberByte": "-129"}' EXTENDED) OBJ FROM DUAL;

BYTE                                                             OBJ
---------------------------------------------------------------- ----------------------------------------------------------------
-128                                                             {"$numberByte":"-129"}

SELECT JSON_QUERY(JSON('1'), '$.type()' WITH ARRAY WRAPPER) STD,
       JSON_QUERY(JSON('1' EXTENDED), '$.type()' WITH ARRAY WRAPPER) EXT FROM DUAL ;

STD                                                              EXT
---------------------------------------------------------------- ----------------------------------------------------------------
["number"]                                                       ["tinyint"]

SELECT JSON_QUERY(JSON('0.1'), '$.type()' WITH ARRAY WRAPPER) STD,
       JSON_QUERY(JSON('0.1' EXTENDED), '$.type()' WITH ARRAY WRAPPER) EXT FROM DUAL ;

STD                                                              EXT
---------------------------------------------------------------- ----------------------------------------------------------------
["number"]                                                       ["double"]
```

<span id="json-ext-print" name="json-ext-print"></span>

#### JSON Extended Format Output

JSON data is output in extended mode only when using the `JSON_SERIALIZE()` function with the `EXTENDED` keyword.

In other cases (e.g., client output or invoking the `JSON_SERIALIZE()` function without the `EXTENDED` keyword), standard mode will be used for output.

In standard format, if JSON data contains extended types, the output follows these rules:

1. For Tinyint, Smallint, Integer, Bigint, Number, the value is output directly, following the same output rules as the equivalent types in YashanDB.
2. For Float and Double:
   - If the value is finite, output the value directly, where Float precision is 9 significant digits, and Double precision is 17 significant digits.
   - If the value is not finite (NaN and positive/negative infinity), output a corresponding string with an initial capital letter: `"Nan"`, `"Inf"`, `"-Inf"`.
3. For Binary type, the output will be a hexadecimal string representing the binary data.
4. For time types (Timestamp, Date, Time), the output will be a string representing the time type in ISO-8601 format.
   - Timestamp type outputs in `"yyyy-mm-ddThh24:mi:ss.ff"` format, where `T` is fixed as letter T, with millisecond precision, and the decimal point and milliseconds are only output when not equal to 0.
   - Date type outputs in `"yyyy-mm-ddThh24:mi:ss"` format, where `T` is fixed as letter T, with second precision.
   - Time type outputs in `"hh24:mi:ss.ff"` format, with millisecond precision, and the decimal point and milliseconds are only output when not equal to 0.

In extended format, the output follows these rules. For clarity, when stating that the output is an object with a unique key-value pair, describe it as outputting "key" format with the value corresponding to this key, such as `{"key": value}`.

1. For small integer types (Tinyint, Smallint, Integer), output the value directly, following the same rules as in standard mode.
2. For Bigint or Number types, if it is an integer not exceeding the range (-2^53, +2^53), output the value directly.
3. For Bigint or Number types, if it is an integer exceeding the abovementioned range but not exceeding the Bigint range, output in the `"$numberLong"` format object with its value as a string.
4. For Number type, if it does not meet the above criteria, output in the `"$numberDecimal"` format object, with its value as a string.
5. For Float type, output in the `"$numberFloat"` format object:
   - If it is finite, the value is represented as a string.
   - If it is not finite, represent it as a string output in standard format.
6. For Double type:
   - If it is finite, output the value directly, following the same output rules as in standard mode.
   - If it is not finite, output in the `"$numberDouble"` format object, with its value represented as a string in standard format.
7. For Binary type, output in the `"$binary"` format object, with its value as base64 encoding of the binary data.
8. For time (Timestamp, Date, Time) types, output in extended format objects with value as their string output in standard format.
   - Timestamp type's key is `$yashanTimestamp`.
   - Date type's key is `$yashanDate`.
   - Time type's key is `$yashanTime`.

## Path Expression

YashanDB provides several built-in functions to implement search functionalities within JSON data (such as checking whether a key exists in JSON data), which need to be used in conjunction with path expressions.

Path expressions are somewhat similar to XQuery or XPath expressions for XML data, and their syntax is defined as follows:

**[json_path](#jsonpath)::=**

```ebnf
= "$" [nonfunction_steps] [function_step].
```

**[nonfunction_steps](#nonfunctionsteps)::=**

```ebnf
= [object_step] [array_step] [descendent_step].
```

**[object_step](#objectstep)::=**

```ebnf
= "." ("*"|key).
```

**[array_step](#arraystep)::=**

```ebnf
= "[" ("*"|((array_index|(array_n "to" array_m)) {"," (array_index|(array_n "to" array_m))})) "]".
```

**[descendent_step](#descendentstep)::=**

```ebnf
= ".." key.
```

**[function_step](#functionstep)::=**

```ebnf
= "." item_method "()".
```

**[item_method](#itemmethod)::=**

```ebnf
= "count"|"size"|"type".
```

<span id="jsonpath" name="jsonpath"></span>

The path expression uses an absolute path, beginning with the symbol `$`, which denotes the matching path for retrieving JSON data. Following `$`, there can be zero or more nonfunction_steps and an optional function_step to support multi-step matching; if no content follows, it denotes a matching path completely consistent with the JSON data.

A path expression selects zero or more matching values. Generally, each step in the path expression is tried sequentially. If the current step fails to match, subsequent steps will not be attempted, and the path expression will fail. If each step matches successfully, the path expression succeeds.

In the path expression, keys without double quotes are only permitted to be alphanumeric characters without any other characters; when special characters are needed, they must be enclosed in double quotes according to standard JSON string syntax.

***Example***

```sql
SELECT JSON_QUERY(JSON('{"1":1,"2":2,"3":3}'), '$') RES FROM dual;
RES                                                              
---------------------------------------------------------------- 
{"1":1,"2":2,"3":3} 
```

<span id="nonfunctionsteps" name="nonfunctionsteps"></span>

### nonfunction_steps

Non-function matching steps can be one of object_step, array_step, or descendant_step.

<span id="objectstep" name="objectstep"></span>

#### object_step

A matching step for key-value pair structured JSON data, which starts with `.` followed by a Key or a wildcard `*`.

A Key that is not marked by double quotes must start with an uppercase or lowercase letter a-z and may only contain letters or decimal digits (0-9), or else it must be marked with double quotes.

A Key can be empty; if so, it must be written as `""`.

When using object_step to match, the system retrieves JSON data by the specified Key and returns the Value corresponding to that Key. When specifying a wildcard, all Values are returned.

***Example***

```sql
SELECT JSON_QUERY(JSON('{"1":1,"2":2,"3":3}'), '$.*' WITH WRAPPER) RES FROM dual;
RES                                                              
---------------------------------------------------------------- 
[1,2,3]   

SELECT JSON_QUERY(JSON('{"1":1,"2":2,"3":3}'), '$.""') RES FROM dual;
RES                                                              
---------------------------------------------------------------- 
   
SELECT JSON_QUERY(JSON('{"1":1,"2":2,"3":3}'), '$."1"') RES FROM dual;   
RES                                                              
---------------------------------------------------------------- 
1   
```

<span id="arraystep" name="arraystep"></span>

#### array_step

A matching step for array-structured JSON data, which starts with `[` and ends with `]`, where empty `[]` is not allowed, and the contents can be:

- Wildcard `*`: Matches all Values.
- Array index position `array_index`: Matches by Value position in the array, allowing specification of multiple positions separated by `,`. Array positions are zero-based integers, so the index of the first element in the array is 0. It can also be in the form of `last - N`, indicating searching from the end of the array, e.g., last - 1 indicates the index for the second-to-last element. The standalone `last` is equivalent to `last - 0`, indicating the index of the last element in the array.
- Array index range `array_n "to" array_m`: The form is `N to M`, where N and M are position indexes, matching by Value position range in the array, equivalent to explicitly specifying all index positions from N to M, e.g., [2 to 4] is equivalent to [2, 3, 4]. The order of N and M does not have special requirements, e.g., representing the index range from the third to the sixth element can be either [2 to 5] or [5 to 2]. Note that `[N to N]` is equivalent to `[N]` rather than `[N, N]`.

When matching with array_step, at least one of the three forms must appear, but the wildcard can only appear independently.

When array index positions and ranges overlap, elements in the overlapping region can be matched multiple times.

If specified index positions or ranges exceed the actual array range, the system will consider it as no data matched without reporting an error.

***Example***

```sql
SELECT JSON_QUERY(JSON('[1,2,3,4,5,6,7,8,9]'), '$[0 to 2, 5 to 3, last to last - 2, 1, 1]' WITH WRAPPER) RES FROM dual;
RES                                                              
---------------------------------------------------------------- 
[1,2,3,4,5,6,7,8,9,2,2]  
```

<span id="descendentstep" name="descendentstep"></span>

#### descendent_step

A recursive matching step for key-value structured JSON data, starting with two consecutive dots `..`, followed by a Key. Based on the data matched in prior steps, it performs a recursive search and returns all matched values.

***Example***

```sql
SELECT JSON_QUERY(JSON('{ "a" : { "b" : { "d" : 1 },
          						  "c" : [ 2, { "d" : 3 } ],
         						  "d" : 4 },
						  "d" : 5 }'), '$.a..d' WITH WRAPPER) RES FROM dual;
RES                                                              
---------------------------------------------------------------- 
[4,1,3] 
```

<span id="functionstep" name="functionstep"></span>

### function_step

Function class matching steps are not mandatory to appear in a path expression, but if they do, they can only appear as the last step of the path expression. Function steps begin with `.`, followed by the Function method name, and parentheses `()` with optional spaces within.

<span id="itemmethod" name="itemmethod"></span>

#### item_method

Function method names apply to the data matched in the prior steps, transforming it with the following methods:

- count: Returns the count of the data matched in the current preceding step.
- size: If the data matched in the preceding step is an array, returns the number of elements; if not, returns NULL.
- type: Returns the type of the data matched in the current preceding step.

***Example***

```sql
SELECT JSON_QUERY(JSON('{ "a" : { "b" : { "d" : 1 },
          						  "c" : [ 2, { "d" : 3 } ],
         						  "d" : 4 },
						  "d" : 5 }'), '$.a..d.count()' WITH WRAPPER) RES FROM dual;
RES                                                              
---------------------------------------------------------------- 
[3]
```

### Loose Matching

In strict matching mode, when searching through JSON data based on path expressions, a particular matching step can only be applied to specific data types. The specific requirements are:

- object_step can only be searched on Object type data; applying it to other types will be considered a match failure.
- array_step can only be searched on Array type data; applying it to other types will also be considered a match failure.

To provide more flexible querying, YashanDB supports a loose matching mode (this is the default mode of the system), with the following rules:

- If the current match is an array_step but the JSON data is non-Array type, the system will automatically wrap the JSON data into a single-element array and then match.

  Example

  ```sql
  SELECT JSON_QUERY(JSON('1'), '$[0]') RES FROM dual;
  RES                                                              
  ---------------------------------------------------------------- 
  1   
  ```

- If the current match is a non-array_step, such as object_step or function_step, but the JSON data is of Array type, the system will apply the current matching path to each element in the Array.

  Example
  
  ```sql
  SELECT JSON_QUERY(JSON('[{"a": 1}, {"a": 2}, {"a": 3}]'), '$.a' WITH WRAPPER) RES FROM dual;
  RES                                                              
  ---------------------------------------------------------------- 
  [1,2,3] 
  ```
