In a relational database, a table is a relation, and a relational database can contain multiple tables. A table is a logical structure with related information arranged in rows and columns, and it is the fundamental unit for organizing data in a relational database.

## Elements of a Table

### Columns

A table in a database contains several columns (or fields); each column represents an attribute of the relational model, describing the meaning of the data it contains. For example, a byte of data 0x61 represents the value `97` when it is data of a one-byte integer column, and it represents the character `a` when it is data of a character column.

The definition of each column typically includes the column name, data type, maximum length, etc. For variable-length data types, it is usually necessary to explicitly declare the maximum length of the column. For fixed-length data types, it is often not necessary to explicitly specify the maximum length.

Columns declared when creating a table typically have the following characteristics:

- The database will store the data of the column, occupying a certain amount of storage space.
- Querying the data of the table will display the data of that column.

However, YashanDB also includes the following three types of special columns that do not meet the above characteristics:

- **Virtual Column**: Its data does not occupy storage space; instead, the value of the column is calculated using the column's default expression only when the data needs to be accessed.
   
   For example, the following statement creates a function index for the table:

   ```sql
   create table employee(id int, name varchar(32));

   create index idx_emp_name on employee(upper(name));
   ```

   The index idx_emp_name is not based on the columns of the employee table but on upper(name). In this case, the database adds a virtual column to the employee table, and the default value of this column is upper(name).

- **Hidden Column**: Its data occupies storage space, but users cannot directly query the data of the hidden column.
   
   For example, a table created by the user contains a column of a custom Object type. This Object type may contain several attributes, and each attribute will be stored in at least one hidden column in the table. Users cannot directly query these hidden columns, but can only access it attributes by querying the Object type column.  

   ```sql
   create or replace type person as object (id char(18), name varchar(32), birthday date);
   /
   create table employee(emp_id int, personal_info person);
   insert into employee values(1, person('11011120000101011X', 'Zhang San', '2000-01-01'));

   -- Query all columns of the table (excluding hidden columns)
  select column_id, column_name, data_type from user_tab_columns where table_name = 'EMPLOYEE' order by 1;

      COLUMN_ID COLUMN_NAME                                                      DATA_TYPE                                                        
   ------------ ---------------------------------------------------------------- ---------------------------------------------------------------- 
            0 EMP_ID                                                           INTEGER                                                         
            1 PERSONAL_INFO                                                    PERSON                                                      PERSON                                                        
   -- Query all columns of the table (including hidden columns)
   select column_id, column_name, data_type from user_tab_cols where table_name = 'EMPLOYEE' order by 1;

      COLUMN_ID COLUMN_NAME                                                      DATA_TYPE                                                        
   ------------ ---------------------------------------------------------------- ---------------------------------------------------------------- 
            0 EMP_ID                                                           INTEGER                                                         
            1 PERSONAL_INFO                                                    PERSON                                                          
            2 SYS_NC00002$                                                     CHAR                                                            
            3 SYS_NC00003$                                                     VARCHAR                                                         
            4 SYS_NC00004$                                                     DATE   

   select e.personal_info.name from employee e;

   PERSONAL_INFO.NAME                
   --------------------------------- 
   Zhang San                        
   ```

   In the example above, the employee table contains a personal_info column, which is a user-defined person data type that includes three attributes: id, name, and birthday. These three attributes do not appear in the user_tab_columns view and cannot be accessed directly.

- **Deleted Column**: A deleted column cannot be accessed.

   When a user deletes a column from a table using the alter table statement, the definition of the column is not removed from the system table; instead, it is only marked as deleted, and its data is not removed from storage.

### Rows

A row in a table corresponds to a tuple (or record) in the relational model. It consists of a set of data from the table's columns. Each row in the table represents all attribute information of a particular entity (or object) stored in that table.

### Data Types

Each column in a table must specify a data type and specification. When users insert data into the table, the data to be inserted is enforced to be converted according to the column's data type and specification. The specification includes maximum length, precision, and other information (which is optional).

YashanDB provides commonly used basic types, including character types, numeric types, and date-time types.

#### Character Type

**VARCHAR Type**

The VARCHAR type is used to store variable-length strings.

When a user specifies the data type of a column in a table as VARCHAR, it is necessary to specify the maximum length (in characters or bytes) for that column. Syntax examples:

```sql
-- Declare VARCHAR type with a maximum length of 10 bytes
VARCHAR(10)
-- Declare VARCHAR type with a maximum length of 10 bytes
VARCHAR(10 byte)
-- Declare VARCHAR type with a maximum length of 10 characters
VARCHAR(10 char)
```

When users insert a string into the table, the string in the SQL statement must be enclosed in single quotes and behaves differently based on its length:

- If the string length exceeds the column's maximum length, an error will be returned.
- If the string length is less than the column's maximum length, it will be stored as per the actual input string.

**CHAR Type**

The CHAR type is used to store fixed-length strings, and users can specify maximum length (in characters or bytes).

When users insert a string into the table, the string in the SQL statement must also be enclosed in single quotes, with different behaviors based on its length:

- If the string length exceeds the column's maximum length, an error will be returned.
- If the string length is less than the column's maximum length, the string will be padded with spaces until its length equals the maximum length.

Due to the space-padding mechanism in CHAR type (the actual stored data gets filled to the maximum length), the length remains consistent during data updates, which may make its performance superior to VARCHAR type in some scenarios, but it consumes relatively more storage space than VARCHAR type.

When comparing two CHAR type values, any trailing padded spaces will be ignored.

When comparing CHAR and VARCHAR types, the trailing padded spaces of the CHAR type will still be considered in the comparison.

**NCHAR and NVARCHAR Types**

NCHAR is used to specify fixed-length strings that support UNICODE, while NVARCHAR is used to specify variable-length strings that support UNICODE. These two character types are available only in databases configured with UNICODE character sets, allowing storage of multi-language data.

When using these two types, the length refers only to character length.

#### Numeric Type

**NUMBER Type**

YashanDB's NUMBER type can store fixed-point and floating-point numbers. When using NUMBER type, precision and scale can be specified, formatted as follows:

```sql
NUMBER(precision, scale)
```

The meanings of precision and scale are as follows:

- Precision indicates the maximum number of digits for the number. User input values will be rounded according to precision to ensure that the result's digit count does not exceed precision.

- Scale indicates the number of digits between the decimal point and the last significant digit. If scale is a positive number, it represents the number of decimal places; if scale is a negative number, it indicates how many zeros are after the last significant digit of the integer part, with no decimal part. For example, 123 stored as `NUMBER(5,2)` becomes 12300E-2, while as `NUMBER(5,-2)`, it becomes 1E2.

The encoding format of the NUMBER type's storage format is independent of hardware and operating systems; it follows YashanDB's internal encoding format and can be migrated across platforms.

The NUMBER type supports a maximum of 38 significant digits.

**Native Numeric Types**

Native numeric types mean the encoding method of the type matches the numeric types supported by the CPU, including native integer types and floating-point types.

- Native Integer Types: YashanDB supports four integer types that use native formats supported by the CPU architecture. As such, they have good operational performance, but their storage format is related to the CPU's byte order and does not maintain cross-platform compatibility.

|Type Name |Length/Bytes |Minimum Value |Maximum Value |
| -------------- | --------- | -------------------- | ------------------- |
| BINARY_TINYINT  | 1         | -128                 | 127                 |
| BINARY_SMALLINT | 2         | -32768               | 32767               |
| BINARY_INTEGER  | 4         | -2147483648          | 2147483647          |
| BINARY_BIGINT   | 8         | -9223372036854775808 | 9223372036854775807 |

- Native Floating-Point Types: YashanDB supports the following two floating-point types: BINARY_FLOAT and BINARY_DOUBLE, corresponding to the single and double precision floating-point numbers defined in ANSI/IEEE Std 754-1985.

|Type Name |Length/Bytes |Minimum Value |Maximum Value |Significant Digits |
| ------------- | --------- | ---------- | ---------- | -------- |
| BINARY_FLOAT  | 4         | -3.40E+38  | +3.40E+38  | 7        |
| BINARY_DOUBLE | 8         | -1.79E+308 | +1.79E+308 | 16       |

**Differences Between Native Types and NUMBER Type**

- Compared to native integer types, NUMBER type supports a larger range of values and significant digits.

- NUMBER type uses a variable-length method to store significant digits, thus usually occupying less storage space when the stored number is much smaller than the native integer type boundaries.

- Compared to native floating-point types, NUMBER type supports a maximum of 38 significant digits, offering greater accuracy in results.

- Arithmetic operations on native types can directly use CPU instructions, thus offering better performance.

#### Date-Time Type

**Date-Time Types**

These represent a specific date or moment, which includes three data types: DATE, TIME, and TIMESTAMP (SCN). These three types store the number of intervals from a specific starting time, thus allowing for addition and subtraction operations on date and time types.

Users can also convert date and time into a string for display in specific formats.

**Interval Types**

These represent the length of the interval between two dates or moments, specifically including YEAR TO MONTH interval and DAY TO SECOND interval.

#### Large Object Types

Some data may exceed a significant length (beyond the size of one data block), occupying considerable storage in the database. If YashanDB were to store such data the same way as other character or binary types, each row of data could occupy a significant number of data blocks. Even if users do not query that column of data, the database would still access numerous data blocks while scanning the table, affecting response speed. Therefore, YashanDB provides a set of LOB (Large Object) types, including CLOB (Character Large Object), BLOB (Binary Large Object), and NCLOB (National Character Large Object).

YashanDB will only store smaller LOB data together with other row data, while larger blob types will store only a location pointer of the LOB data within the row, while the actual data will be separately stored in other data blocks, minimizing the space used by LOB data within the row itself.

When users scan a table containing LOB types, LOB data blocks will not be scanned. If users need to access LOB data, they must retrieve the location information from the row, followed by locating the corresponding data block. Thus, when a table contains a large number of lengthy LOB data, and if the user's SQL statement does not involve LOB column access, the execution performance of that SQL statement will not be significantly impacted by the presence of LOB data. However, when accessing LOB data, reading performance may be lower than for other data types.

#### Other Data Types

**ROWID**

When data in the table is organized by rows, the system generates a globally unique record using the physical address information of each row. ROWID is used for addressing when retrieving table data and can serve as a unique identifier for each row of data.

**RAW**

A variable-length binary type.

**BIT**

The BIT type supports binary bitmaps with a width (size) of 1-64 bits, allowing only 0/1 values for each BIT.

**Boolean Type**

The Boolean type data can only take values of 1 (TRUE) and 0 (FALSE), occupying 1 byte in length, which can indicate the state of a binary feature.

**JSON**

The JSON type in YashanDB is a variable-length data type that produces binary data through parsing strings that conform to the standard JSON format.

## Organization of Tables

Data in tables is akin to a two-dimensional matrix. When storing table data, it can be stored by rows, meaning all column data of one row is stored together, and after that row is completed, the next row is stored; it can also be stored by columns, meaning all row data of one column is stored, and after that column is completed, the next column is stored.

### Heap Table

When creating a table, if the organization method is specified as HEAP, then the data of the table will be organized by rows. Each row of data in the table will be stored in the order of the columns defined in the table. Heap tables are suitable for scenarios where data is retrieved by rows, thus commonly used in transaction-based business scenarios.

For instance, the data of the employees table is shown below:

| id   | name      | department |
| ---- | --------- | ---------- |
| 1    | Zhang San | marketing  |
| 2    | Li Si     | sales      |
| 3    | Wang Wu   | support    |

Thus, the organization method is:

```sql
row1: 1, Zhang San, marketing; row2: 2, Li Si, sales; row3: 3, Wang wu, support
```

### LSC Table

The LSC table is characterized by concentrated column data storage; querying data by columns is significantly faster than reading by rows, making it suitable for analytical scenarios. In analytics, a large amount of data does not require updates; the business will only query this data, which is referred to as stable data. These stable data can undergo further processing (compression, sorting, etc.) to enhance query performance. A smaller amount of data that requires updates is termed mutable data.

#### LSC Table

When creating a table, the organization method can be specified as LSC. LSC tables are suited for large-scale stable data analysis scenarios, with data stored in concentrated columns. LSC tables separate data into mutable data areas and stable data areas. When data is written to the database, it is first written to the mutable data area, where data is stored using segments to accommodate the randomness of data writing. Once the data becomes stable, it is transferred to the stable data area, which utilizes large-granularity, highly compressed object storage, and employs data sorting and sparse indexing to accelerate queries.

#### TAC Table

When creating a table, the organization method can be designated as TAC. TAC is a type of LSC table focused on real-time data analytics, where the data is stored in the mutable data area, and each column has concentrated data storage. Since the data is in the mutable data area, the update performance exceeds that of LSC tables.

## Special Table Types

Special table types refer to temporary tables and external tables, excluding regular tables.

### Temporary Table

Temporary tables are used to store session-private data that exists only during a transaction or session, and this type of data is not shared between different sessions.

#### Classification of Temporary Tables

Temporary tables can be classified into Global Temporary Table and Private Temporary Table based on whether their definition is shared across different sessions.

Global Temporary Tables are further divided into transaction-level Global Temporary Table (on commit delete rows) and session-level Global Temporary Table (on commit preserve rows) based on whether to retain data at the end of a transaction.

Private Temporary Tables are also divided into transaction-level Private Temporary Table (on commit drop definition) and session-level Private Temporary Table (on commit preserve definition) regarding whether to retain table definition and data after the transaction ends.

The following table illustrates the behavioral differences of various types of temporary tables:

|Feature |Global Temporary Table |Private Temporary Table |
| ---------------------- | ----------------- | ---------------------- |
| Is the table's definition globally visible? | Yes, visible to all sessions   | No, only visible to the session that created it |
| Is the table's definition persistent?          | Yes, temporary table remains after database restart | No, temporary table definition does not exist after database restart |
| Impact of transaction commit on temporary tables | If specified as On commit delete rows, table definition is preserved but data is cleared<br/>If specified as On commit preserve rows, both table definition and data are preserved | If specified as On commit drop definition, both table definition and data are cleared<br/>If specified as On commit preserve definition, both table definition and data are preserved |
| Impact of session disconnection on temporary tables | Table definition preserved, data cleared | Both table definition and data are cleared |
| Naming rules for tables         | Consistent with the naming rules of regular tables | Starts with `YAS$PTT_` or `ORA$PTT_` |

#### Storage of Temporary Table Data

When a session inserts data into a temporary table, the database system will create a segment for that temporary table in the temporary tablespace and allocate data blocks. When the same session queries the temporary table again, the database will retrieve data from the segment, but this segment is invisible to other sessions. Transaction-level Global/Private Temporary Tables will release the space occupied by the segment at the end of the transaction, while session-level Global/Private Temporary Tables will release the space at the end of the session.

The definition of a Global Temporary Table is persisted in the system table and will not be lost due to transaction end, session end, or process restart unless a deletion operation is performed to remove that table definition. The definition of a Private Temporary Table is stored in memory; transaction-level Private Temporary Tables will clear the table's definition from memory at transaction end, while session-level Private Temporary Tables will clear the definition at session end.

### External Table

External tables refer to data tables whose data is stored outside the database, where their storage space is not managed by database processes. The database stores only metadata such as the table definition, enabling access to external tables similarly to regular tables.

#### Significance of External Tables

For importing data into a database, only the metadata of external tables needs to be written to the database since the actual data does not occupy database storage space. Therefore, in scenarios such as data warehousing, this can greatly reduce data loading times and storage space consumption.

The actual storage location of external table data can vary flexibly, such as file systems, cloud storage, distributed file systems, etc.

#### Accessing Data in External Tables

Accessing external table data in the database is implemented through the corresponding external table driver, which can parse data in external table files to access them like regular table data.

YashanDB currently only supports CSV format for external table data access drivers.

#### Creating External Tables

The syntax for creating an external table is as follows:

```sql
CREATE TABLE ...
ORGANIZATION (HEAP | TAC | LSC | EXTERNAL [external_table_clause])

external_table_clause=
([TYPE access_driver_type]                       -- Specify external table driver
 [external_table_data_props])                    -- Specify external table data attributes
 [REJECT LIMIT (integer | UNLIMITED)]            -- Specify external table data error tolerance

external_table_data_props=
[DEFAULT DIRECTORY directory]                    -- Specify default directory for external table
[ACCESS PARAMETERS ( opaque_format_spec )]       -- Specify access parameters for external table
[LOCATION [directory :] ('location_specifier')]  -- Specify location of external table file
```

When creating a table, the organization can be declared as EXTERNAL by specifying ORGANIZATION EXTERNAL. It is also necessary to specify access_driver_type to indicate the data access driver for the external table and specify LOCATION to declare the location of the external table file.

When creating an external table, the database writes only the necessary metadata to the system table and does not store data or data organization methods from external table files. Additionally, the database stores essential information for accessing the external table file (e.g., the type of data access driver, location of the external table file, etc.) and the methods for obtaining data from the external table file as a table (e.g., the mapping between columns in CSV files and columns in the external table).
