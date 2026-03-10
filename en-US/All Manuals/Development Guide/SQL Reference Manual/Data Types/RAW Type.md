The RAW type in YashanDB is a variable-length data type similar to VARCHAR that can represent binary data or byte strings.

When defining a table, you can declare a column field using the RAW type, which is typically used to store graphics, sound, documents, or arrays of binary data, with specific explanations depending on the use case.

Storage Properties
----

|Type |Byte Length |
| --- |---------|
| RAW | 1~65534 |

Definition format:

|Type |Format |Rules |
| --- | --- | --- |
| RAW      | RAW(Size) | A variable-length binary string. No padding with 0x00 occurs if the inserted string is shorter than Size. |

Size: Width, which indicates the maximum byte length.

Usage Rules
-----

### Usage Limitations

The usage limitations for RAW type data are as follows:

- Cannot be used for columnar indexing.
- Cannot be used as a partition key in ISC Distributed Cluster Deployment.

### Data Type Conversion

YashanDB supports conversions between RAW and character data types, including explicit and implicit conversions.

YashanDB also supports conversions between RAW and BLOB/ROWID/UROWID/JSON data types, as well as one-way conversions from RAW to CLOB.

**Explicit Conversion**

Users can use built-in functions such as CAST and TO_CHAR for data type conversion between RAW and character types.

***Example***

```sql
SELECT CAST('123' AS RAW(5)) raw_res FROM dual;
RAW_RES 
------- 
0123   

SELECT TO_CHAR(CAST('123' AS RAW(5))) char_res FROM dual;
CHAR_RES 
-------- 
0123    
```

**Implicit Conversion**

1. Implicit conversion from character type to RAW type:

The system parses each character entered consecutively into the hexadecimal representation of four consecutive bits in binary data and concatenates them to build RAW type data.

If the input string contains characters that do not belong to the range of hexadecimal representation (0-9, a-f, A-F), the system reports an error.

If the number of characters in the input string is odd, the system will first pad four consecutive bits of 0 before converting the string to RAW type data.

When inserting or updating a RAW column field, if the byte length of the input string exceeds twice the width of the RAW column, the system will report an error.

***Example*** for Heap tables

```sql
CREATE TABLE IF NOT EXISTS raw_table(c1 RAW(10));

-- Implicit conversion from character type to RAW type
INSERT INTO raw_table VALUES('1234ABCDEF');
INSERT INTO raw_table VALUES('1AB');

-- Invalid hexadecimal representation causes implicit conversion failure
INSERT INTO raw_table VALUES('G');
YAS-00223 invalid hex number

-- Inserting string with byte length exceeding twice the RAW column width causes insertion failure
INSERT INTO RAW_TABLE VALUES(LPAD(1,21,1));
YAS-04008 C1 size exceeding limit 10
```

2. Implicit conversion from RAW type to character type:

The system treats RAW type data as a hexadecimal representation in binary input and converts it to characters, where each character is a hexadecimal digit (0-9, a-F, A-F) representing four consecutive raw data bits. For example, a byte of raw data with a value of 10101011 is represented as the hexadecimal value AB and converted to the string 'AB'.

***Example*** for Heap tables

```sql
-- Performing string operations on RAW type data from the previous example, the system performs implicit conversion
SELECT GROUP_CONCAT(c1) FROM raw_table;
GROUP_CONCAT(C1)                                                 
---------------------------------------------------------------- 
1234ABCDEF,01AB 
```

3. Implicit conversion from RAW type to BLOB type:

Because both RAW and BLOB types can be used to store binary objects, the system can directly convert RAW data to BLOB data.

***Example*** for Heap tables

```sql
-- Creating a new BLOB table
CREATE TABLE IF NOT EXISTS blob_table(c1 BLOB);

-- Data from raw_table can be directly inserted into blob_table
INSERT INTO blob_table SELECT c1 FROM raw_table;
```

4. Implicit conversion from BLOB type to RAW type:

As long as the byte length of BLOB data does not exceed 65534 bytes, BLOB data can be converted into RAW data.

***Example*** for Heap tables

```sql
-- Data from blob_table can be directly inserted into raw_table
INSERT INTO raw_table SELECT c1 FROM blob_table;
```
