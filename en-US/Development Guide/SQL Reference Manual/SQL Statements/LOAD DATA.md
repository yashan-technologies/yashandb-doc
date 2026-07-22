## General Description

LOAD DATA is used to load data from a CSV file into the physical table of a database.

To execute the LOAD DATA statement to import files using the *yasql* tool, users must have the FILE privilege, and the target file must be located in the secure directory specified by the SECURE_FILE_PRIV parameter, which can be viewed using SHOW PARAMETER SECURE_FILE_PRIV.

This statement performs SQL-level data loading (SQL LOADER), which is equivalent to performing a DDL operation on the target table. Therefore, it must satisfy all constraints defined on the target table, and it will also apply row locks on the target table. Ensure that there are no uncommitted transactions on the rows to be operated before executing the data load, as waiting for row locks may cause the loading task to be suspended.

In the initial phase of execution, a transaction commit will be executed once to avoid blocking due to transaction wait with other statements executed before this statement in the same transaction.

Users cannot execute this statement in an ISC Distributed Cluster Deployment.

**Transaction Commit for Importing Data**

During the loading process, the SQL LOADER commits transactions based on the size of the buffer holding the CSV data. Once the buffer containing the CSV data is filled, the current transaction is committed.

The CSV data buffer starts with a complete data row and ends with a complete data row. The data length in the buffer varies depending on the situation but does not exceed 2M bytes.

If a data insertion fails in the buffer, the whole buffer's data insertion fails, and the current transaction is rolled back.

**Setting Nologging Import**

If the statement is used for data migration, setting nologging to true in the options parameter enables nologging import.

The nologging property is only recommended in data migration scenarios, and the following matters need attention:

- The primary/standby environment cannot enable nologging; a standby database should be created after completing data imports.
- After using nologging import, executing a full checkpoint can ensure data persistence; otherwise, abrupt shutdowns may lead to data loss.
- When the table is in nologging mode and a crash occurs, the table will be marked as corrupted after a restart. It can only be restored by truncating the table, and the above import process must be re-executed to restore the data.
- If a transaction fails, all nologging tables that have performed insert operations in that transaction will be marked as corrupted.
- Nologging property LSC tables use bulkload mode to import data and are not affected by the nologging property (performance remains unchanged, and failures will not be marked as corrupted).
- When a table is nologging, update and delete operations cannot be performed on it. The table should be modified to logging as soon as the import operation is completed.
- Setting the table state to nologging is a DDL operation that will acquire a table lock. Concurrently executing DML statements may cause DML statements to fail.
- When a table is nologging or imported using nologging, fault-tolerant failures may occur when violating constraints, specifically affected by the position of the violating constraint data among all data.

**Import Environment Preparation**

Users should ensure that the actual configuration values are greater than the specified values. It is recommended to adjust these values according to the requirements of online services after the import is completed.

|Configuration Parameter |Suggested Actions |
|----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| LARGE_POOL_SIZE          | Adjust to the maximum value within the parameter range. If a no free blocks in the large pool error occurs during the import process, it indicates that this value still cannot meet the import resource requirements. At this point, you should:<br>1. Adjust the import table and reduce the total number of heap partition tables.<br>2. Reduce the degree_of_parallelism parameter during the import and re-import. |
| WORK_AREA_STACK_SIZE     | Number of reader threads = degree_of_parallelism / (decoder_thread_times + 1), rounded up.<br>Number of decoder threads = degree_of_parallelism - number of reader threads.<br>SIZE = 512KB + 126KB * number of tables * number of decoder threads.                                                                |
| WORK_AREA_POOL_SIZE      | MIN_SIZE = number of threads * number of tables * partition count * 256B + number of reader threads * number of decoder threads * 1KB.                                                                                                                                                                   |

Statement Definition
----

**load data::=**

```ebnf
= LOAD DATA [option_clause]
input_file_clause [bad_file_clause] [discard_file_clause]
{" " input_file_clause [bad_file_clause] [discard_file_clause]}
table_clause.
```

**[option_clause](#optionclause)::=**

```ebnf
= OPTIONS "(" [PARAMETER_NAME "=" PARAMETER_VALUE {"," PARAMETER_NAME "=" PARAMETER_VALUE}] ")".
```

**[input_file_clause](#inputfileclause)::=**

```ebnf
= INFILE input_file_path
[FIELDS file_type]
[WITH EMBEDDED|WITHOUT EMBEDDED]
[FIELDS TERMINATED BY fields_terminated_char]
[OPTIONALLY ENCLOSED BY enclosed_by_char].
```

**[bad_file_clause](#badfileclause)::=**

```ebnf
= BADFILE bad_file_path.
```

**[discard_file_clause](#discardfileclause)::=**

```ebnf
= [DISCARDFILE discard_file_path] [(DISCARDS|DISCARDMAX) discard_number].
```

**[table_clause](#tableclause)::=**

```ebnf
= [load_type] INTO TABLE ([schema "."] table_name) [when_clause] [TRAILING NULLCOLS] column_clause
{" " INTO TABLE ([schema "."] table_name) [when_clause] [TRAILING NULLCOLS] column_clause} [directory_clause].
```

**[load_type](#loadtype)::=**

```ebnf
= INSERT|APPEND.
```

**[when_clause](#whenclause)::=**

```ebnf
= WHEN load_condition_clause {AND load_condition_clause}.
```

**[load_condition_clause](#loadconditionclause)::=**

```ebnf
= ["("] (table_column_name|"(" csv_column_order ")") compare_oper "'column_value'" [")"].
```

**[compare_oper](#compareoper)::=**

```ebnf
= "="|"!="|"<>".
```

**[column_clause](#columnclause)::=**

```ebnf
= '(' (normal_column_clause|filler_column_clause|lob_column_clause|lls_column_clause)
{"," (normal_column_clause|filler_column_clause|lob_column_clause|lls_column_clause) } ')'.
```

**[normal_column_clause](#normalcolumnclause)::=**

```ebnf
= table_column_name [COLUMN "(" csv_column_order ")"] [nullif_clause] .
```

**[nullif_clause](#nullifclause)::=**

```ebnf
= NULLIF load_condition_clause {AND load_condition_clause}.
```

**[filler_column_clause](#fillercolumnclause)::=**

```ebnf
= filler_column_name FILLER [COLUMN "(" csv_column_order ")"] .
```

**[lob_column_clause](#lobcolumnclause)::=**

```ebnf
= table_column_name LOBFILE "(" filler_column_name ")" [terminated_by_eof] [nullif_clause] .
```

**[lls_column_clause](#llscolumnclause)::=**

```ebnf
= table_column_name [COLUMN "(" csv_column_order ")"] [nullif_clause] LLS .
```

**[directory_clause](#directoryclause)::=**

```ebnf
= directory split_file_path.
```

<span id="optionclause" name="optionclause"></span>

### option_clause

This statement allows users to specify options related to the loading operation, separated by commas (`,`). It can be omitted to default to OPTIONS() without any user-specified options.

**COMMIT_ROWS**

Determines the frequency of transaction commits. If its value is n, n data rows from the data file will be decoded before committing once.

The parameter value must be an integer within the range [1,4294967295], with a default value of 4096.

**DECODER_THREAD_TIMES**

Determines the allocation ratio of threads. In the *yasldr* client during import, it refers to the ratio of READER threads to SENDER threads. In LOAD DATA import, it refers to the ratio of READER threads to DECODER threads, with the total of both types of threads not exceeding DEGREE_OF_PARALLELISM.

The parameter value must be an integer within the range [1,255], with a default value of 7.

**DEGREE_OF_PARALLELISM**

Specifies the degree of parallelism for the data loading task, restricted by the MAX_PARALLEL_WORKERS parameter in server mode. If degree_number exceeds this parameter, it will be constrained by it.

The parameter value must be an integer within the range [2,256], with a default value of 8. This value represents the user's expected degree of parallelism. The system will compute the actual degree based on the user's expected value, hardware environment, file size, and ensure that the loading task runs concurrently at the actual value.

**ENABLE_BULK**

Specifies the import mode for HEAP tables and LSC tables.

The parameter value can be TRUE or FALSE, with a default value of FALSE.

When set to TRUE, HEAP tables will use BCP mode for import, and LSC tables will use bulkload mode.

**ENABLE_DEDUP**

Specifies the conflict handling method for LSC tables.

This option must be used in conjunction with the ENABLE_BULK option. The parameter value can be TRUE or FALSE, with a default value of FALSE.

When set to TRUE, unique constraint conflicts generated during the import process for LSC tables will be deduplicated.

**ERRORS**

Specifies the upper limit of fault-tolerant data records during the import. Import will be terminated once the number of erroneous records reaches this limit. When importing multiple files, the count of erroneous records is accumulated.

The parameter value must be an integer within the range [0,4294967295], with a default value of 50.

**NOLOGGING**

When set to TRUE, tables declared as LOGGING in the statement will be converted to NOLOGGING for import. After the import, the LOGGING state of the table will be restored. When set to FALSE, no operations will be performed on the table's LOGGING state.

If the initial state of the table is LOGGING, after the import is completed, the LOGGING state needs to be restored. The mechanism is asynchronous, and the transition of the table's LOGGING state will occur in the background. This behavior may fail, and the results of the transition will be recorded in the logs; for details, refer to the LOGGING ASYNC action.

The parameter value must be TRUE or FALSE, with a default value of FALSE.

**NULL_LOB_FORMAT_SIZE**

A value of 1 indicates a specific LOB type null value representation, organized as four bytes full F, used only in LLS import adaptation. A value of 0 indicates a normal LOB type null value representation, organized as eight bytes full F, which is a generic representation for LOB type null values.

The parameter value can only be 0 or 1; other values are invalid, with a default value of 0.

**RUN_LEVEL**

Specifies the type of task to be executed by the program, either SPLIT or SPLIT_TO_PART mode.

The RUN_LEVEL option is only implemented when using the *yasldr* tool for data import and cannot be specified directly through the LOAD DATA statement. Specific operations can refer to the [User Guide for yasldr](../../../Tools Guide/yasldr/User Guide for yasldr) section.

The parameter value must be SPLIT or SPLIT_TO_PART. If RUN_LEVEL is not specified, no file splitting operation will occur by default.

**LOB_PATH_RELATIVE**

Specifies the path type for LOB files in outline lob import form during file splitting.

The parameter value can be TRUE or FALSE, with a default value of FALSE.

- If set to TRUE, during file splitting, only LOB files can be specified as relative paths. Absolute paths will cause an error during statement execution.
- If set to FALSE, during file splitting, LOB files can be specified as both relative and absolute paths. If an absolute path is specified, *yasboot* will not copy the data to remote nodes.

**LOG**

Specifies the generation of a log file that records the process information during the execution of the data loading task, including reasons for data import failure, number of successful imports, and other information related to the imported file specified by input_file_clause. A log file will be generated regardless of whether this parameter is specified. If omitted, a log file with the same name as the first imported file and with a .log suffix will be created in the directory of that file by default.

The parameter value supports both relative and absolute paths but only supports specifying the filename.

> **Note**:
>
> The parameters related to log file generation for YashanDB data import include SILENT, STATS, and LOG, as described below:
>
> - When SILENT is TRUE, STATS and LOG parameters become invalid, and no log file, error data file, or filtered data file will be generated.
> - When SILENT is FALSE:
>   - If STATS is TRUE, the log file will contain statistics.
>   - If STATS is FALSE, the log file will not contain statistics but will include process information.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Create the area_log.csv file yourself in the /data directory with the following content:
1|load|101|loadbranch|1
2|load|201|loadbranch|2

LOAD DATA OPTIONS(DEGREE_OF_PARALLELISM=2,LOG='/data/area_log.log')
INFILE '/data/area_log.csv' fields terminated by '|'
INTO TABLE area(area_no,area_name);

-- Log file content
YashanDB Loader Release {version_number} x86_64 ff1fe7a
Production on 2023-08-24 04:34:29.409

Table AREA:
column_name                                                        infile_column
AREA_NO                                                            1
AREA_NAME                                                          2
DHQ                                                                NULL

Table AREA:
  2 Rows successfully loaded.
  0 rows not loaded due to data errors.
  0 Rows not loaded because all WHEN clauses were failed.
  0 Rows not loaded because all fields were null.

Total logical records read: 2
Total logical records rejected: 0
Total logical records discarded: 0

Elapsed time was: 00:00:00.07
```

**SILENT**

Specifies whether to generate record files during the import process, including log files, error data files, and filtered data files. When set to TRUE, no record files will be generated; when set to FALSE, record files will be generated.

The parameter value must be TRUE or FALSE, with a default value of FALSE.

**SINGLE_PART**

Specifies whether the data in the import file belongs to the same partition. If it belongs to the same partition, it will simplify partition calculations and improve performance. If specified as TRUE, ensure the data in the import file belongs to the same partition.

The parameter value must be TRUE or FALSE, with a default value of FALSE.

This parameter is only supported when specified in the *yasldr* tool.

**STATS**

Specifies whether to print statistics during the import process. When TRUE, the statistics will be printed to the log; when FALSE, statistics will not be printed. The printed log will be saved in the path of the imported data file and will have the same name as the data file, with a log format.

The parameter value must be TRUE or FALSE, with a default value of FALSE.

The statistics printed include:

- Imported objects
- Number, time, and memory of READER and DECODER threads
- Storage time and memory
- Total time and memory

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Create the area_stats.csv file yourself in the /data directory, with the following content:
1|load|101|loadbranch|1
2|load|201|loadbranch|2

LOAD DATA OPTIONS(DEGREE_OF_PARALLELISM=2,STATS=TRUE)
INFILE '/data/area_stats.csv' fields terminated by '|'
INTO TABLE area(area_no,area_name);

-- Log content excerpt
YashanDB Loader Release 23.1.0.100 x86_64 1a34179
Production on 2023-07-31 20:18:59.849

Table AREA:
column_name                                                        infile_column
AREA_NO                                                            1
AREA_NAME                                                          2
DHQ                                                                NULL

In this Task, degree of parallelism is 2.
The number of reader is 1.
The number of decoder is 1.
```

**TRIM**

Indicates how to handle spaces in the CSV data file. LDRTRIM indicates trimming spaces and tabs at the start of data columns; NOTRIM indicates no processing of spaces and tabs at the start of data columns. For data with enclosures, characters inside the enclosure are treated as data and are not restricted by this parameter.

The parameter value must be LDRTRIM or NOTRIM, with a default value of LDRTRIM.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Create the area.csv file yourself in the /data directory, with the following content:
8|load|801|loadbranch|8
9|load|901|loadbranch|9

LOAD DATA OPTIONS(DEGREE_OF_PARALLELISM=2,TRIM=NOTRIM)
INFILE '/data/area.csv' fields terminated by '|'
INTO TABLE area(area_no,area_name);
```

<span id="inputfileclause" name="inputfileclause"></span>

### input_file_clause

This statement is used to specify the relevant information of the input files. Multiple import files can be specified at once, separated by spaces.

A single data loading command can specify up to 250 files, and the same file cannot be specified multiple times.

<u>The data content format of the imported files is</u>:

*Column 1 data delimiter Column 2 data delimiter ...*

In which the maximum number of columns within each file is 4096, and the same-position columns across multiple files represent the same meaning. The maximum length of a single line of data is 63KB.

<u>The reading rules for data loading from files are</u>:

- Each line represents one record to be imported.
- The mapping relationship between data columns and target table column fields is determined by the [column_clause](#columnclause) statement.
- Columns that are not mapped will be discarded.
- If a data column starts with a double quotation mark, that quotation mark will be recognized as a delimiter, and it is required that:
  1. The data column must end with a double quotation mark.
  2. Any ordinary double quotation marks that appear in the middle must be escaped with an additional double quotation mark before them.
- If the data column does not start with a double quotation mark, subsequent double quotation marks will be treated as ordinary double quotation marks and need not be escaped.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```json
-- Create the customer_intro table with JSON fields
CREATE TABLE customer_intro (id INT, intro JSON);

-- Create the customer.csv file in the /data directory, containing the following data, where the json data containing double quotes is escaped
1,"[755,false,null,""city"",{""no"":1},[12]]" 
2,""""""
3,"{""city"":""shenzhen"",""no"":2}"
4,"""shenzhen,2"""

-- Execute the loading statement
LOAD DATA 
INFILE '/data/customer.csv'
INTO TABLE customer_intro (id,intro);

SELECT id,intro FROM customer_intro;

          ID INTRO                                                            	
------------ ---------------------------------------------------------------- 	
           1 [755,false,null,"city",{"no":1},[12]]                           
           2 ""                                                              	
           3 {"city":"shenzhen","no":2}                                     	
           4 "shenzhen,2"            
```

#### input_file_path

Specifies the path and name of the input files, including the following two formats:

- An absolute path starting with '/', and wildcard characters such as *, ? are not supported for regex matching.
- A relative path starting with '?/' indicates that the import file is located within the YASDB_DATA directory.

Neither format can contain './' or '../'.

The specified files must exist, and the database system user must have read privilege on them; otherwise, the loading task will report an error and exit.

An empty data file can also be specified without affecting the continuation of the loading task.

#### fields

Specifies the type of the input files, which can be omitted. The default is CSV files.

The value of file_type can only be CSV; otherwise, the loading task will report an error and exit.

#### with embedded|without embedded

Specifies whether newline characters can be enclosed by delimiters as data. By default, this statement is WITHOUT EMBEDDED, meaning newline characters cannot be treated as data.

#### fields terminated by

Specifies the delimiter for importing data columns, which defaults to a comma if not specified.

The fields_terminated_char supports three representations:
1. Single-byte character representation (comma, pipe, semicolon, horizontal tab).
2. Hexadecimal representation (0x01, 0x02, 0x03,..., 0x1F).
3. Integer representation (1, 2, 3,..., 31).

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Create the loadterm table
create table loadterm(c1 varchar(10),c2 varchar(10),c3 varchar(10),c4 varchar(10), c5 varchar(10));

-- Create the loadterm.csv file yourself in the /data directory with the following content:
8|load|801|loadbranch|8
9|load|901|loadbranch|9

LOAD DATA OPTIONS(DEGREE_OF_PARALLELISM=2)  
INFILE '/data/loadterm.csv' FIELDS csv
WITH EMBEDDED FIELDS TERMINATED BY '|' 
OPTIONALLY ENCLOSED BY '"'
INSERT INTO TABLE loadterm(c1,c2,c3,c4,c5);

-- Change the content of the loadterm.csv file to:
"1""2""3""4""5"
"6""7""8""9""10"

LOAD DATA OPTIONS(DEGREE_OF_PARALLELISM=2)  
INFILE '/data/loadterm.csv' FIELDS csv
WITH EMBEDDED FIELDS TERMINATED BY 0x01 
OPTIONALLY ENCLOSED BY '"'
INSERT INTO TABLE loadterm(c1,c2,c3,c4,c5);

-- Change the content of the loadterm.csv file to:
"1"	"2"	"3"	"4"	"5"
"6"	"7"	"8"	"9"	"10"

LOAD DATA OPTIONS(DEGREE_OF_PARALLELISM=2)  
INFILE '/data/loadterm.csv' FIELDS csv
WITH EMBEDDED FIELDS TERMINATED BY 9 
OPTIONALLY ENCLOSED BY '"'
INSERT INTO TABLE loadterm(c1,c2,c3,c4,c5);
```

#### optionally enclosed by

Specifies the enclosure for imported data, which can only be specified as double quotes. By default, the system assumes double quotes as enclosures.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
LOAD DATA OPTIONS()  
INFILE '/data/area.csv' FIELDS csv
WITH EMBEDDED FIELDS TERMINATED BY '|' 
OPTIONALLY ENCLOSED BY '"'
INSERT INTO TABLE area(area_no,area_name);
```

<span id="badfileclause" name="badfileclause"></span>

### bad_file_clause

This statement is used to specify the address where the file containing erroneous data will be stored, related to the imported file specified in the input_file_clause statement. The number of erroneous data write-ins is limited by the ERRORS parameter. A bad data file will be generated regardless of whether this statement is specified if there is erroneous data during import. If omitted, a bad data file with the same name as the imported file and a .bad suffix will be generated in the same directory. When a file with the same name exists, it will be overwritten.

Both relative and absolute paths are supported, with the ability to specify a directory or a filename. If specified as a directory, a bad data file with the same name as the imported file, along with a .bad suffix, will be generated within that directory, overwriting any existing file with the same name.

Erroneous data will not be imported into the database, including:

- Data that fails type conversion.
- Data that violates constraints.
- Data that does not conform to CSV format.
- Data that does not hit the partition.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Create badfile.csv and badfile.csv1 in the /data directory with the following content:
"1","2"
"nihao"
"hello"

-- Create the bad_load table
CREATE TABLE bad_load(c1 int,c2 int);

-- Execute the import command
LOAD DATA OPTIONS(DEGREE_OF_PARALLELISM=3) 
INFILE '/data/badfile.csv' FIELDS TERMINATED BY ',' 
BADFILE '/data/badfile.bad' 
INSERT INTO TABLE bad_load(c1,c2);

-- "nihao" and "hello" cannot be converted to int type, these two data entries will be written to the bad data file. Check the content of the badfile.bad using the terminal command:
$ cat badfile.bad
"nihao"
"hello"

-- Import multiple data files 
LOAD DATA OPTIONS(DEGREE_OF_PARALLELISM=3) 
INFILE '/data/badfile.csv' FIELDS TERMINATED BY ',' 
BADFILE '/data/badfile.bad' INFILE '/data/badfile.csv1' FIELDS TERMINATED BY ',' 
BADFILE '/data/badfile1.bad' 
INSERT INTO TABLE bad_load(c1,c2);
```

<span id="discardfileclause" name="discardfileclause"></span>

### discard_file_clause

This statement is used to specify the address where the file containing filtered data will be stored, related to the imported file specified in the input_file_clause. It can be omitted; if omitted, no filtered data file will be created. If this statement is specified but the imported data does not meet the corresponding conditions, a filtered data file will still not be generated.

Both relative and absolute paths are supported, with the ability to specify to a directory or a filename. If specified as a directory, a filtered data file with the same name as the imported file and a .dsc suffix will be created in that directory.

Filtered data will not be imported into the database, including:

- Data that does not satisfy WHEN clause statements.
- Whole rows mapped as NULL.

### DISCARDS|DISCARDMAX

Used to specify the upper limit of filtered data during the import. When the number of filtered data reaches the limit, the data import will be terminated. This can be omitted; if omitted, it defaults to UINT64_MAX.

If the discard_file_clause statement is not specified but the DISCARDS|DISCARDMAX keywords are specified, a filtered data file with the same name as the imported file will be created in the directory of the imported file, overwriting any existing file with that name.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Create discardfile.csv and discardfile1.csv yourself in the /data directory with the following content:
"3","4"
""
""

-- Create the discard_load table
CREATE TABLE discard_load(c1 int,c2 int);

-- Execute the import command
LOAD DATA OPTIONS(DEGREE_OF_PARALLELISM=3) 
INFILE '/data/discardfile.csv' FIELDS TERMINATED BY ',' 
DISCARDFILE '/data/discardfile.dsc' 
INSERT INTO TABLE discard_load(c1,c2);

-- Since the second row data in the imported files maps to NULL, it will be written to the filtered data file. View the content of the discardfile.dsc with the terminal command:
$ cat discardfile.dsc
""
""

-- Import multiple data files 
LOAD DATA OPTIONS(DEGREE_OF_PARALLELISM=3) 
INFILE '/data/discardfile.csv' FIELDS TERMINATED BY ',' 
DISCARDFILE '/data/discardfile.dsc' 
INFILE '/data/discardfile1.csv' FIELDS TERMINATED BY ',' 
DISCARDFILE '/data/discardfile1.dsc' 
INSERT INTO TABLE discard_load(c1,c2);
```

<span id="tableclause" name="tableclause"></span>

### table_clause

This statement is used to specify information about the target tables for import and can include multiple target tables separated by spaces.

When specifying multiple target tables, they must be of the same type, such as all being HEAP tables.

A single data loading command can specify up to 16 target tables, and the same target table cannot be specified multiple times.

<u>The main rules for loading data into multiple target tables are:</u>

- If one target table fails to import due to format errors, mapping mismatches, etc., all target tables will fail to import.
- When all target tables can be imported, the transactions for the import of multiple target tables are independent. Therefore, the failure of one table during import will not cause the failure of imports for all tables.
- When importing two target tables that have foreign key relationships, the parent table should be listed first.
- When the mapping relationship between the data columns and the target table columns is not explicitly indicated using COLUMN(csv_column_order), the multiple target tables will be sequentially mapped to the columns of the imported file.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- The area table and branches table are both HEAP tables, where area.area_no is the parent key of branches.area_no.
-- Create the area.csv file yourself in the /data directory with the following content:
12|load|1201|loadbranch|12
13|load|1301|loadbranch|13

-- The data loading command is:                     
LOAD DATA 
INFILE '/data/area.csv' FIELDS TERMINATED BY '|'
INTO TABLE area (area_no,area_name)
INTO TABLE branches (branch_no,branch_name,area_no);
```

<span id="loadtype" name="loadtype"></span>

#### load_type

The execution type of the data loading can be omitted; the default is INSERT.

Specifying either INSERT or APPEND indicates that data insertion will occur on the target table (both empty and non-empty tables).

#### table_name

The name of the target table, which may include a schema specification; the current user must possess the privilege to execute the specified operation on the table.

<span id="whenclause" name="whenclause"></span>

#### when_clause

Specifies filtering conditions for the data to be imported; multiple filtering conditions can be specified and connected with AND. Only data that meets the filtering conditions will be imported.

<span id="loadconditionclause" name="loadconditionclause"></span>

##### load_condition_clause

The content of the filtering conditions.

<span id="compareoper" name="compareoper"></span>

###### compare_oper

The comparison operator can be =, !=, <>.

###### table_column_name|csv_column_order

Filtering can be specified by either target table column fields or columns from the imported file data, and duplicate columns can be specified across multiple conditions. 

The table_column_name must be the name of the target table column field, while csv_column_order refers to the column index of the data in the imported file.

###### column_value

A string surrounded by '' for column value comparison. When it is an empty string (''), the results are:

-  Comparison with = '' will always returnfalse.
- Comparison with != '' and <> '' will always return true.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
LOAD DATA 
INFILE '/data/area.csv' FIELDS TERMINATED BY '|'
INTO TABLE area WHEN area_no='12' (area_no,area_name)
INTO TABLE branches WHEN (3)='1201' (branch_no,branch_name,area_no);
```

<span id="trailingnullcols" name="trailingnullcols"></span>

#### trailing nullcols

When a certain column of a target table is not mapped to data in the imported file but the target table has other columns that can map to data (i.e., the number of data columns in the imported file is less than the number of columns in the target table), specifying TRAILING NULLCOLS allows NULL values to be filled in for the columns lacking mapped data. (Even if there are definitions stating some null values cannot be allowed on that column, filling in NULL values will still cause an error). Otherwise, data import will fail.

When none of the columns in the target table are mapped to data in the imported file (i.e., the imported file contains no data for the target table), specifying TRAILING NULLCOLS allows import for that table to be ignored (not affecting import of other target tables). Otherwise, this data will result in import failure.

The errors from the two cases (specifying TRAILING NULLCOLS causes NULL value errors, and the lack of specifying TRAILING NULLCOLS leads to import task errors) will affect all target tables in the same data loading command, causing all to fail.

Thanks to the fault tolerance mechanism, when the number of failed data entries does not exceed the fault-tolerant data limit specified by ERRORS, the execution result of the statement will return Success. The details of the import situation can be found in the log file generated for the imported files.

When the number of columns in the imported file exceeds the number of columns in the target table, the excess columns will be automatically ignored without throwing an error, and this behavior is unrelated to the trailing nullcols configuration parameter.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Create area1.csv and area2.csv yourself in the /data directory
-- The content of area1.csv file is:
13|load
-- The content of area2.csv file is:
14|load|1401|loadbranch

-- Without specifying TRAILING NULLCOLS, the data import will fail, and all target tables cannot import that line of data    
LOAD DATA 
INFILE '/data/area1.csv' FIELDS TERMINATED BY '|'
INTO TABLE area (area_no,area_name)
INTO TABLE branches (branch_no,branch_name,area_no);

-- Log (content excerpt from /data/area1.log)
infile /data/area1.csv offset 1 is rejected by table BRANCH because table column mismatch csv column.

Table AREA:
  0 Rows successfully loaded.
  0 rows not loaded due to data errors.
  0 Rows not loaded because all WHEN clauses were failed.
  0 Rows not loaded because all fields were null.

Table BRANCHES:
  0 Rows successfully loaded.
  1 rows not loaded due to data errors.
  0 Rows not loaded because all WHEN clauses were failed.
  0 Rows not loaded because all fields were null.

-- Specifying TRAILING NULLCOLS loading
-- Importing area1.csv will only load data for the area table
LOAD DATA 
INFILE '/data/area1.csv' FIELDS TERMINATED BY '|'
INTO TABLE area (area_no,area_name)
INTO TABLE branches TRAILING NULLCOLS (branch_no,branch_name,area_no);

-- Log excerpt
Table AREA:
  1 Rows successfully loaded.
  0 rows not loaded due to data errors.
  0 Rows not loaded because all WHEN clauses were failed.
  0 Rows not loaded because all fields were null.

Table BRANCHES:
  0 Rows successfully loaded.
  0 rows not loaded due to data errors.
  0 Rows not loaded because all WHEN clauses were failed.
  1 Rows not loaded because all fields were null.

-- Importing area2.csv will load data for both area table and branches table, with branches.area_no filled as NULL
LOAD DATA 
INFILE '/data/area2.csv' FIELDS TERMINATED BY '|'
INTO TABLE area (area_no,area_name)
INTO TABLE branches TRAILING NULLCOLS (branch_no,branch_name,area_no);

-- Log excerpt
Table AREA:
  1 Rows successfully loaded.
  0 rows not loaded due to data errors.
  0 Rows not loaded because all WHEN clauses were failed.
  0 Rows not loaded because all fields were null.

Table BRANCHES:
  1 Rows successfully loaded.
  0 rows not loaded due to data errors.
  0 Rows not loaded because all WHEN clauses were failed.
  0 Rows not loaded because all fields were null.
```

<span id="columnclause" name="columnclause"></span>

#### column_clause

The column fields of the target table, specified with commas between multiple column fields.

For column fields in the target table that exist but are not specified in the data loading, NULL values will be automatically filled in by the system; therefore, non-nullable column fields must be specified, or data loading will fail.

<span id="normalcolumnclause" name="normalcolumnclause"></span>

##### normal_column_clause

This statement designates the import mode as normal, using all content from the corresponding columns of the import file for import.

Given that CSV formatted files have data size limitations, for larger data files, it is recommended to use LLS mode for partial import or LOBFILE mode for full import.

When importing into a target table that contains virtual columns：

- If the CSV file itself does not include data for the virtual columns, simply specify the columns to import directly;

- If the CSV file includes data for the virtual columns, you need to specify the positions of the physical columns in the CSV file and skip importing the virtual columns, or set the vritual columns as `FILLER`.

***Example***：Importing data to the HEAP table with virtual columns

```sql
-- Create a business table containing virtual columns
create table tab_virtual_col_exp_imp(c1 int, v1 as (c1 + c2), c2 int);

-- When the CSV file does not include virtual column data, the import is no different from that of a regular table; simply omit the virtual column and import CSV data into the table in order
LOAD DATA INFILE '/ssd_data/yashandb/tab_virtual_col_exp_imp.csv' fields terminated by ',' into table tab_virtual_col_exp_imp(c1, c2);​

-- When the CSV file includes both physical and virtual column data, specify the positions of physical columns C1 and C2 in the CSV file for import
LOAD DATA INFILE '/ssd_data/yashandb/tab_virtual_col_exp_imp.csv' fields terminated by ',' into table tab_virtual_col_exp_imp(c1 column(1), c2 column(3));​

-- When the CSV file includes both physical and virtual column data, import data while explicitly skipping the virtual column
LOAD DATA INFILE '/ssd_data/yashandb/tab_virtual_col_exp_imp.csv' fields terminated by ',' into table tab_virtual_col_exp_imp(c1, v1 FILLER, c2);
```

###### table_column_clause

The name of the existing column field in the target table.

<span id="csvcolumnorder" name="csvcolumnorder"></span>

###### COLUMN(csv_column_order)

Specifies the mapping of target table column fields to the imported file data column. <u>This may be omitted, in which case the following rules apply</u>:

- When the first column field of the first target table is omitted, it defaults to mapping to the first data column of the imported file.
- When the first column field of a target table other than the first is omitted, it defaults to mapping to the next data column of the last mapped data column of the previous target table. If that data column does not exist, it will be processed according to the [trailing nullcols](#trailingnullcols) statement rules.
- When a non-first column field is omitted, it defaults to mapping to the next data column in the previous column field's mapping in the imported file. If that data column does not exist, it will be processed according to the [trailing nullcols](#trailingnullcols) statement rules.

<u>The rules for handling non-omitted mappings are as follows</u>:

- Multiple target table column fields are allowed to map to the same data column in the imported file.
- Mapping to a non-existing data column is permitted, in which case NULL values will be imported.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Prepare area3.csv yourself, the file is located in the /data directory, with the following content:
15|16|17|18|19

-- Execute the loading statement
LOAD DATA 
INFILE '/data/area3.csv' FIELDS TERMINATED BY '|'
INTO TABLE area (area_no,area_name)
INTO TABLE branches TRAILING NULLCOLS (branch_no,branch_name,area_no COLUMN(1));

SELECT area_no,area_name,DHQ FROM area WHERE area_no='15';
AREA_NO AREA_NAME       DHQ           
------- --------------- --------------
15      16              ShenZhen 
SELECT branch_no,branch_name,area_no,address FROM branches WHERE area_no='15';
BRANCH_NO BRANCH_NAME        AREA_NO ADDRESS            
--------- ------------------ ------- -------------------
17        18                 15    
```

<span id="nullifclause" name="nullifclause"></span>

###### nullif_clause

This statement specifies whether to import the column with NULL values under certain conditions, with multiple conditions allowed to be specified and connected by AND. When the result of the condition is true, the data loading will import NULL values for that column rather than the mapped data from the import file.

**load_condition_clause**

Same as the [load_condition_clause](#loadconditionclause) in the when_clause.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Create area4.csv yourself, the file is located in the /data directory with the following content:
16|load|1601|loadbranch|16

-- Execute the loading statement; branches.area_no will be NULL instead of 16
LOAD DATA 
INFILE '/data/area4.csv' FIELDS TERMINATED BY '|'
INTO TABLE area (area_no,area_name)
INTO TABLE branches (branch_no,branch_name,area_no NULLIF (2)='load');

SELECT branch_no,branch_name,area_no,address FROM branches WHERE branch_no='1601';
BRANCH_NO BRANCH_NAME        AREA_NO ADDRESS            
--------- ------------------ ------- -------------------
1601      loadbranch               
```

<span id="fillercolumnclause" name="fillercolumnclause"></span>

##### filler_column_clause

This statement constructs a dummy column, which is used to map the address data of LOB files in the CSV file, which can be referenced by the lob_column_clause statement.

###### filler_column_name

The name of the dummy column, ideally different from existing column names in the target table to avoid import errors.

###### csv_column_order

Same as the csv_column_order in the normal_column_clause.

###### nullif_clause

Same as the nullif_clause in the normal_column_clause.

<span id="lobcolumnclause" name="lobcolumnclause"></span>

##### lob_column_clause

This statement sets the import mode to LOBFILE mode, where the entire file referenced by the filler dummy column will be imported into the specified column of the target table.

###### table_column_name

The name of the existing column field in the target table.

###### filler_column_name

The name of the dummy column specified in the filler_column_clause.

###### terminated_by_eof

Only used for syntax compatibility, with no actual meaning.

###### nullif_clause

Same as the nullif_clause in the normal_column_clause.

> **Note**: 
>
> Unreferenced FILLER dummy columns are allowed, but it is recommended to use filler_column_clause and lob_column_clause statements in conjunction.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Create load_lls.csv file yourself in the /data directory, with the following content:
LOB_FILE.dat.1.2/
LOB_FILE.dat.2.3/

---- Create the sqlldr_lob table in the database
CREATE TABLE sqlldr_lob(c1 int,c2 clob);

LOAD DATA INFILE '/data/load_lls.csv' 
INTO TABLE sqlldr_lob(c1,file1 filler,c2 lobfile(file1) terminated by eof);
```

<span id="llscolumnclause" name="llscolumnclause"></span>

##### lls_column_clause

This statement sets the import mode to LLS (Lob Location Specifier) mode, which specifies that only part of the contents of the data file will be selected for importing. Lengths and offsets can be specified from any starting position for import.

The format of the imported file column data with the LLS keyword must be ` filename.ext.nnn.mmm/ `:

-  filename.ext denotes the name of the file containing LOB data.
- nnn is the byte offset of the LOB data within the file, which only allows an integer. The offset cannot exceed the size of the data file and cannot be negative.
  - If the input offset value is positive, the actual offset = input value - 1.
  - If the value is 0, the actual offset = 0.
  - If the value is negative, an error will be returned.
- mmm is the length of the LOB within the bytes, which only allows an integer. A value of -1 denotes null; a value of 0 signifies the import of an empty LOB. Values less than -1 are not permitted.
- A forward slash (/) is the terminator character and must be included in the format; otherwise, an error will occur.

###### table_column_clause

The name of the existing column field in the target table.

###### csv_column_order

Same as the csv_column_order in the normal_column_clause.

###### nullif_clause

Same as the nullif_clause in the normal_column_clause.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- The above example includes the same files and table. Also create the LOB_FILE file in the /data directory, with the following content:
abcdefg
-- Import file column data as LOB_FILE.dat.1.2/, which means starting from the LOB_FILE.dat file's offset of 0, importing the data of length 2.
-- Import file column data as LOB_FILE.dat.2.3/, which means starting from the LOB_FILE.dat file's offset of 1, importing the data of 3, denoted by the forward slash.

-- The import command is:
LOAD DATA OPTIONS (degree_of_parallelism=3) 
INFILE '/data/load_lls.csv' INTO TABLE sqlldr_lob(c2 LLS);
```

<span id="directoryclause" name="directoryclause"></span>

#### directory_clause

This statement specifies the directory where files created during splitting will be stored when run_level is set to split. Since SPLIT mode is implemented only in the *yasldr* tool, the specific usage can be found in the [User Guide for yasldr](../../../Tools Guide/yasldr/User Guide for yasldr) section.

If not specified, the default output directory will be the location of the first file in the infile.