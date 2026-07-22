## View Help

Use the command `yasldr -H` to view help information.

```shell
$ yasldr -H
YashanDB Loader Enterprise Edition Release {version_number} x86_64 431125c

YashanDB LOADER HELP:
To specify parameters, should use keywords:
  Command Format:
    YASLDR USERNAME/PASSWORD@IP:PORT KEYWORD=value

    "USERNAME/PASSWORD@URL" must be the first on the command line, "@URL" default 127.0.0.1:1688
    KEYWORD                    DESCRIPTION (DEFAULT)
    ---------------------------------------------------------------------------------------------------
    BATCH_SIZE                 the number of lines per batch, default 4032, range [1, 65535]
    CONTROL_FILE               a file containing the LOAD statement
    CONTROL_TEXT               the LOAD statement, only be used when CONTROL_FILE is not specified
    MODE                       import mode, default BATCH, range [BASIC, BATCH] 
    ......                     ......

  Example:
    YASLDR USERNAME/PASSWORD@IP:PORT CONTROL_FILE=LOAD.CTL
```

## View Version

Use the command `yasldr -V` to view version information.

```shell
$ yasldr -V
YashanDB Loader Enterprise Edition Release {version_number} x86_64 431125c
```

## Import Command Format

```shell
$ yasldr USERNAME/PASSWORD@IP:PORT {LOAD OPTIONS} {LOAD STATEMENT} 
```
**USERNAME/PASSWORD@IP:PORT**

The database username, password, and address for executing the import command, with the default address being 127.0.0.1:1688.

The privilege requirements for this user are:

- Must have write privilege and query privilege on the target table for the imported data.
- In an ISC Distributed Cluster Deployment, the user must also have the SELECT_CATALOG_ROLE role and privileges on the system tables ROUTE$, NODE_INFO$, DIST$, OBJ$, DISTCOL$, TAB$, USER$, PARTOBJ$, and PARTCOL$.

```sql
-- Grant SELECT_CATALOG_ROLE role to the user
grant select_catalog_role to user;

-- Grant the corresponding system table privileges to the user 
grant select on sys.route$ to user;
grant select on sys.node_info$ to user;
grant select on sys.dist$ to user;
grant select on sys.obj$ to user;
grant select on sys.distcol$ to user;
grant select on sys.tab$ to user;
grant select on sys.user$ to user;
grant select on sys.partobj$ to user;
grant select on sys.partcol$ to user;
```

### LOAD OPTIONS

The LOAD OPTIONS section is used to set command line parameters for running *yasldr*.

*yasldr* provides multiple optional command line control parameters for users to adjust and set the import process. When executing the import command, users can set multiple parameters simultaneously, for example:

- BATCH_SIZE: The number of rows of CSV data sent per batch during import.
- MODE: The import method, which can be specified as BASIC or BATCH.

Refer to [yasldr Command Line Parameters](Explanation of yasldr Parameters) for more descriptions of command line parameters.

### LOAD STATEMENT

#### Specification Method

The LOAD statement can be specified as follows:

- CONTROL_FILE: A control file containing a single LOAD statement.

  Example

  ```shell
  # Prepare a local CSV file (for example, /home/yasdb/area.csv)
  $ vi /home/yasdb/area.csv
  12|load|1201|loadbranch|12
  13|load|1301|loadbranch|13
  
  $ vi /home/yasdb/load.ctl
  LOAD DATA INFILE '/home/yasdb/area.csv' FIELDS TERMINATED BY '|' optionally enclosed by '"' INTO TABLE branches (branch_no,branch_name,area_no)
  
  $ yasldr sales/sales@127.0.0.1:1688 batch_size=4032 mode=batch packet_size=131072 control_file=/home/yasdb/load.ctl
  YashanDB LOADER VERSION: RELEASE 1.0.0.0
  [YASLDR] import succeeded
  ```

- CONTROL_TEXT: A single LOAD statement enclosed by double quotes and single quotes, only used when CONTROL_FILE is not specified.

  Example

  ```shell
  # Prepare a local CSV file (for example, /home/yasdb/area.csv)
  $ vi /home/yasdb/area.csv
  12|load|1201|loadbranch|12
  13|load|1301|loadbranch|13
  
  $ yasldr sales/sales@127.0.0.1:1688 batch_size=4032 control_text="'LOAD DATA INFILE '/home/yasdb/area.csv' FIELDS TERMINATED BY '|' optionally enclosed by '\"' INTO TABLE area (area_no,area_name,dhq) '"
  YashanDB LOADER VERSION: RELEASE 1.0.0.0
  [YASLDR] import succeeded
  ```

> **Note**: 
>
> When specifying CONTROL_TEXT, the operating system's requirements for escaping special characters must be met, for example, in Linux, double quotes must be escaped using \.

#### Statement Content

##### load data

```ebnf
= LOAD DATA [option_clause]
input_file_clause [bad_file_clause] [discard_file_clause]
{" " input_file_clause [bad_file_clause] [discard_file_clause]}
table_clause.
```

##### option_clause

```ebnf
= OPTIONS "(" ("PARAMETER_NAME" "=" PARAMETER_VALUE){",""PARAMETER_NAME" "=" PARAMETER_VALUE} ")".
```

This statement is used to specify options for the loading operation. Below are some commonly used parameters:

###### COMMIT_ROWS

Specifies the frequency of transaction commits. Assume its value is n, decode n rows of data from the data file and commit once. BULKLOAD import is an exception.

parameter_value must be an integer within the range [1,4294967295], with a default value of 4096.

##### DEGREE_OF_PARALLELISM

Specifies the parallelism of the data loading task, limited by the target database parameter MAX_PARALLEL_WORKERS. If degree_number exceeds this parameter, the value of this parameter will take precedence.

parameter_value must be an integer within the range [2,256], with a default value of 8. The system calculates the actual parallelism value based on this parameter, hardware environment, size of the data file, etc., allowing the loading task to run concurrently based on the actual value.

##### ERRORS

Specifies the upper limit for the number of fault-tolerant data rows during data import. When the number of data errors reaches the limit, data import will terminate. For multiple file imports, the number of erroneous data rows is accumulated.

parameter_value must be an integer within the range [0,4294967295], with a default value of 50.

For more detailed parameter descriptions, see [yasldr Parameter Description](Explanation of yasldr Parameters).

##### input_file_clause

```ebnf
= INFILE input_file_path
[FIELDS file_type]
[WITH EMBEDDED|WITHOUT EMBEDDED]
[FIELDS TERMINATED BY fields_terminated_char]
[OPTIONALLY ENCLOSED BY enclosed_by_char].
```

This statement specifies the relevant information for the import file. Multiple import files can be specified at once, separated by spaces.

Supports the use of * and ? wildcard for regular matching of file names, where * matches 0 or any number of characters, and ? matches a single character (i.e., occupies one character). Specific matching rules are as follows:

- If the file specified by INFILE matches the wildcard, it will result in a naming conflict error.
- If a directory is matched, it will be directly skipped and not processed recursively.
- A maximum of 250 files can be matched at once; if the number of matched files exceeds the limit, an error will be reported.

***Example***

```bash
# Import all files ending with .csv in the /home/yasdb directory
$ yasldr sales/sales@127.0.0.1:1688 control_text="'LOAD DATA INFILE '/home/yasdb/*.csv' FIELDS TERMINATED BY '|' OPTIONALLY ENCLOSED BY '\"' APPEND INTO TABLE area(area_no,area_name,dhq)'"

# Import all files from csv10 to csv20 in the /home/yasdb directory
$ yasldr sales/sales@127.0.0.1:1688 control_text="'LOAD DATA INFILE '/home/yasdb/csv1?.csv' FIELDS TERMINATED BY '|' OPTIONALLY ENCLOSED BY '\"' APPEND INTO TABLE area(area_no,area_name,dhq)'"
```

###### INFILE input_file_path

The path and name of the import file, supporting both absolute and relative paths.

The specified import file must exist, and the system user must have read privilege on it; otherwise, the loading task will report an error and exit.

An empty data file can be specified without affecting the continuation of the loading task.

###### FIELDS file_type

This statement is used to specify the type of the import file, which can be omitted. If omitted, the default is a CSV file.

The value of file_type can only be CSV; otherwise, the loading task will report an error and exit.

###### WITH EMBEDDED|WITHOUT EMBEDDED

This statement specifies whether line breaks can be enclosed as data. It can be omitted, and if omitted, the default is WITHOUT EMBEDDED, meaning line breaks cannot be treated as data.

###### FIELDS TERMINATED BY fields_terminated_char

This statement specifies the delimiter for the imported data columns. If not specified, a comma is used as the default delimiter.

The fields_terminated_char supports the following representations:

- Up to five single-byte characters
- A single hexadecimal
- A single integer

Value range: ASCII codes 1-126, excluding numbers, line breaks, and case letters.

###### OPTIONALLY ENCLOSED BY enclosed_by_char

This statement specifies the enclosing character for the imported data, which can be omitted. If omitted, the system defaults to enclosing by double quotes.

The enclosed_by_char supports the following representations:

- A single single-byte character
- A single hexadecimal
- A single integer

Value range: ASCII codes 1-126, excluding line breaks and spaces.

> **Note**: 
>
>- The enclosing character and the delimiter cannot be the same.
>- When specifying delimiters or enclosing characters, pay attention to the escaping of command line inputs, such as `\` being escaped as `\\`, `"` should be escaped as `\"`, `'` should be escaped as `''`, and `$` should be escaped as `\$`.
>- When specifying the delimiter as a space, character representations are not supported, and hexadecimal or integer representations should be used.

##### bad_file_clause

```ebnf
= BADFILE bad_file_path.
```

This statement specifies the path of the error data file, which can be omitted. If omitted, the default is to generate a bad data file in the same directory as the imported file specified in the input_file_clause. If a file with the same name already exists, it will be overwritten.

Regardless of whether this statement is specified, if erroneous data is imported, the corresponding error data file will be generated, which is limited by the number of erroneous data rows set by the ERRORS parameter.

Erroneous data will not be imported into the database. The following types of data will be written into the error data file:

- Data with type conversion failures
- Data violating constraints
- Data not conforming to CSV format
- Data not hitting partitions

***Example***

```bash
# Prepare a local CSV file (for example, /home/yasdb/area.csv)
$ vi /home/yasdb/area.csv
  "1"|"1"
  "nihao"
  "hello"

# Create the bad_load table
CREATE TABLE bad_load(c1 int,c2 int);

# Execute the import command
$ yasldr sales/sales@127.0.0.1:1688 control_text="'LOAD DATA INFILE '/home/yasdb/area.csv' FIELDS TERMINATED BY '|' OPTIONALLY ENCLOSED BY '\"' BADFILE '/home/yasdb/area.bad' INSERT INTO TABLE bad_load(c1,c2)'"

# Since "nihao" and "hello" cannot be converted to int type data, the above two data points will be recorded in the error data file. To view the contents of /home/yasdb/area.bad data file, execute the following command in the terminal
$ cat /home/yasdb/area.bad
"nihao"
"hello"
```

##### discard_file_clause

```ebnf
= DISCARDFILE discard_file_path [(DISCARDS|DISCARDMAX) discard_number].
```

This statement specifies the address for storing discarded data, which can be omitted. If omitted, the default is not to create a discard data file.

Even if this statement is specified, a corresponding discard data file will only be generated if the imported data meets the filtering conditions, and the filtered data will not be imported into the database.

The filtering condition is as follows:

- Rows where all mapped data are NULL.

###### (DISCARDS|DISCARDMAX) discard_number

This statement is used to specify the upper limit for the number of rows filtered during data import. When the number of discarded rows reaches the limit, the data import will terminate. It can be omitted; if omitted, the default is UINT64_MAX.

If the discard_file_clause statement is not specified but the DISCARDS|DISCARDMAX keyword is, a discard data file with the same name but a dsc suffix will be generated in the directory specified by the input_file_clause statement. If a file with the same name exists, it will be overwritten.

##### table_clause

```ebnf
= [load_type] INTO TABLE ([schema "."] table_name) [TRAILING NULLCOLS] column_clause
{" " INTO TABLE ([schema "."] table_name) [TRAILING NULLCOLS] column_clause} [directory_clause].
```

This statement specifies the relevant information for the target table for the import.

###### TRAILING NULLCOLS

When the number of data columns in the import file is less than the number of columns in the target table, specifying TRAILING NULLCOLS can supplement NULL values for the columns lacking mapped data (if there are constraints prohibiting NULL values on that column, supplementing NULL values will still cause errors). Otherwise, the loading task will report an error.

When the number of data columns in the import file exceeds the number of columns in the target table, the excess data columns will be automatically ignored without throwing an error. This behavior is independent of the trailing nullcols configuration parameter.

###### column_clause

```ebnf
= '(' (table_column_name (lob_column_clause|lls_column_clause|func_column_clause) |filler_column_clause)
{"," (table_column_name (lob_column_clause|lls_column_clause|func_column_clause)  |filler_column_clause) } ')'.
```

This statement is used to specify the mapping relationship between the columns in the target table and those in the CSV file. Here, table_column_name is used to specify the column names that need to import data.

When importing into a target table that contains virtual columns：

- If the CSV file itself does not include data for the virtual columns, simply specify the columns to import directly;

- If the CSV file includes data for the virtual columns, you need to set the vritual columns as `FILLER`.

**filler_column_clause**

```ebnf
= filler_column_name FILLER.
```

This statement is used to construct a pseudo column, which will map to a column of data in the CSV file. Unused FILLER pseudo columns are allowed, and in this case, the corresponding CSV data column will be skipped and not processed. However, it is recommended to use filler_column_clause and lob_column_clause together.

**lob_column_clause**

```ebnf
= LOBFILE "(" filler_column_name ")" [terminated_by_eof].
```

This statement specifies that the column's import mode is to be LOBFILE mode. In this mode, by referencing the FILLER pseudo column, the entire file is imported into the column specified in the target table.

**lls_column_clause**

```ebnf
= LLS .
```
This statement specifies that the column's import mode is to be LLS (Lob Location Specifier) mode. This mode selects part of the data file for import and allows specifying import from any position and any length.

The imported data column format in the target table must be `filename.ext.nnn.mmm/`:

- filename.ext: The file name containing LOB data, where the filename part supports both absolute and relative paths, and the path part supports dots (.) while the file name cannot contain dots (.).
- nnn: The byte offset of the LOB data in the file, which can only be empty, 0, or positive integers and cannot exceed the size of the data file.
  - A positive integer means the actual offset = input value - 1.
  - Empty or 0 means the actual offset = 0.
  - Negative values will return an error.
- mmm: The length of the LOB in bytes, which can only be empty, -1, 0, or positive integers.
  - A positive integer means the actual byte length to be imported.
  - -1 means NULL.
  - Empty or 0 means importing an empty LOB.
- The forward slash (/) is a terminator character that must be included in the format; otherwise, an error will be reported.

**func_column_clause**

```ebnf
= '"' func_name "(" func_arg {"," func_arg } ")" '"' .
```

*yasldr* supports importing computed values using specified functions, where the inserted data is the result after function computation. Currently, it only supports the ST_GeomFromText and BFILENAME functions, and nesting of functions is not allowed.

This statement is used to specify the function and its parameters, which can only be used in BASIC import mode.

- func_name is used to specify the function name.
- func_arg is used to specify the function parameters, where `?` can be used to represent the corresponding data column in the CSV file, and any other form of parameters will be passed to the function as is.

***Example***1: Importing out-of-line LOB data

```bash
# Prepare local files lob1.dat and lob2.dat, located in /home/yasdb/, with the contents:
abcdefg

# Create the /home/yasdb/load_lob.csv file with the contents:
"1"|"lob1.dat"|"lob1.dat.1.7/"

# Create the bad_load table
CREATE TABLE sqlldr_lob(c1 int,c2 clob,c3 clob);

# Execute the import command
$ yasldr sales/sales@127.0.0.1:1688 batch_size=4032 control_text="'LOAD DATA INFILE '/home/yasdb/load_lob.csv' FIELDS TERMINATED BY '|' OPTIONALLY ENCLOSED BY '\"' INTO TABLE sqlldr_lob(c1,file1 filler,c2 lobfile(file1), c3 LLS)'"
```

***Example***2: Importing GIS data

```bash
# Prepare the GIS data file load_gis_demo.csv, where part of the GIS column coordinate values and coordinate system values are stored adjacently, and some GIS columns only have coordinate values, the content is as follows:
1,"POINT(0 0)", 4326,11,"POINT(0 0)", "yashandb"
2,"LINESTRING(1 2,4 5)", 2018,22,"LINESTRING(1 2,4 5)", "postgresql"
3,"POLYGON ((1 0,1 1,2 2,1 0),(0 0,6 6,8 8,0 0))", 2025,33,"POLYGON ((1 0,1 1,2 2,1 0),(0 0,6 6,8 8,0 0))", "mysql"
4,"MULTIPOINT ((1 1),(2 2))", 4326,44,"MULTIPOINT ((1 1),(2 2))", "yashandb"

# Create the target GIS table
CREATE TABLE YASLDR_LOAD_GIS_DEMO (C1 INT, C2 ST_GEOMETRY, C3 INT, C4 ST_GEOMETRY, C5 VARCHAR(20));

# Execute the import command
$ yasldr sales/sales mode=basic control_text="'load data options(degree_of_parallelism=3) infile './load_gis_demo.csv' into table YASLDR_LOAD_GIS_DEMO(c1, c2 \"ST_GeomFromText(?,?)\", c3, c4 \"ST_GeomFromText(?, 3256)\", c5)'"
```

***Example***3: Importing BFILE data
```bash
# Prepare a data file load_bfile.csv containing BFILE columns, with the content as follows:
1|"MY_DIR"|"a.txt"|"sss1"|"MY_DIR"|"b.txt"|"MY_DIR"|"b1.txt"|"lobdata1"
2|"MY_DIR"|"testfile"|"sss2"|"MY_DIR"|"testimage"|"MY_DIR"|"b2.txt"|"lobdata2"

# Create the table to import BFILE
CREATE TABLE T_BFILE(C1 INT,C2 BFILE,C3 VARCHAR(10),C4 BFILE, C5 BFILE, C6 CLOB);

# Execute the import command
$ yasldr sales/sales mode=basic control_text="'load data OPTIONS(degree_of_parallelism=3) infile './load_bfile.csv' fields terminated by '|' optionally enclosed by '\"' append into table t_bfile (c1, c2 \"BFILENAME(?,?)\", c3, c4 \"BFILENAME(?,?)\", c5 \"BFILENAME(?,?)\", c6)'"
```

***Example***4：Importing data to the HEAP table with virtual columns

```sql
-- Create a business table containing virtual columns in the target database
create table tab_virtual_test (c1 int, v1 as (c1 + c2), c2 int);
```

```bash
# Prepare a data file virtual_col.csv on the yasldr tool side
$ echo '"1","2","5"' >> $YASDB_DATA/c5csv/virtual_col.csv
$ echo '"6","2","5"' >> $YASDB_DATA/c5csv/virtual_col.csv
$ echo '"7","2","5"' >> $YASDB_DATA/c5csv/virtual_col.csv

# When the CSV file includes both physical and virtual column data, import data while explicitly skipping the virtual column in BASIC import mode
$ yasldr user_name/passwd control_text="'LOAD DATA INFILE '?/c5csv/virtual_col.csv' FIELDS TERMINATED BY ',' optionally enclosed by '\"' INTO TABLE tab_virtual_test (C1, V1 FILLER, C2)'" mode=basic
```

###### directory_clause

This statement specifies the directory for storing files after splitting based on run_level options. It can be omitted; if omitted, the default output directory is the same as that of the first infile.

***Example***

```bash
# Create a file folder under /home/yasdb

# Specify directory_clause
$ yasldr sales/sales@127.0.0.1:1688 batch_size=4032 control_text="'LOAD DATA OPTIONS(RUN_LEVEL=SPLIT,DEGREE_OF_PARALLELISM=2) INFILE '/home/yasdb/area.csv' FIELDS TERMINATED BY '|' OPTIONALLY ENCLOSED BY '\"' APPEND INTO TABLE area(area_no,area_name,dhq) directory '/home/yasdb/file''"

#result: The split files will be stored in the /home/yasdb/file directory.

# Not specifying directory_clause
$ yasldr sales/sales@127.0.0.1:1688 batch_size=4032 control_text="'LOAD DATA OPTIONS(RUN_LEVEL=SPLIT,DEGREE_OF_PARALLELISM=2) INFILE '/home/yasdb/area.csv' FIELDS TERMINATED BY '|' OPTIONALLY ENCLOSED BY '\"' APPEND INTO TABLE area(area_no,area_name,dhq) '"

#result: The split files will be stored in the /home/yasdb directory.
```

## Import Detailed Introduction

### Pre-import Configuration

When *yasldr* executes the import in BATCH mode, it directly interfaces with the storage layer data structure using batch binding parameters to improve performance. Therefore, some relevant configuration parameters of the target database should be properly adjusted before importing. The specific parameter configuration is as follows:

|Configuration Parameter Item |Suggested Operation |
|----------------------|----------------------------------------|
| LARGE_POOL_SIZE              | Adjust to the maximum value within allowed range. If the import process gives a "no free blocks in large pool" error, it indicates that this value still does not meet the resource requirements for import. Solutions: <br>1. Adjust the import table to reduce the total number of heap partition tables <br>2. Lower the degree_of_parallelism parameter during import <br>3. Re-import               |
| WORK_AREA_STACK_SIZE         | reader thread count = degree_of_parallelism / (decoder_thread_times + 1), rounded up <br>sender thread count = degree_of_parallelism - reader thread count <br>SIZE = 512KB + 126KB * Number of tables * sender thread count |
| WORK_AREA_POOL_SIZE          | MIN_SIZE = Number of threads * Number of tables * Number of partitions * 256B + reader thread count * sender thread count * 1KB           |
| DATA_BUFFER_SIZE             | Specify the size of the data buffer. The larger the data buffer capacity, the better the import performance. The default is 64M, range [32M,64T]      |
| COLUMNAR_BUFFER_SIZE         | When importing LSC tables in BULKLOAD mode, it is recommended to increase this parameter, with a default value of 2G and a range of [256M,4T]              |
| COLUMNAR_DATA_BUFFER_PERCENT  | Specifies the percentage of the data buffer used by the LSC storage engine within COLUMNAR_BUFFER_SIZE. When importing LSC tables in BULKLOAD mode, a larger value for this parameter improves import performance, while a smaller value can increase computing performance. The default is 40, range [1,99]      |

>**Note**:
> 
> Other possible ways to avoid performance degradation:
>
> - If the performance degradation is significantly caused by the indexes created on the table for the data to be imported, consider importing the data first and then manually creating the indexes.
>
> - The automatic expansion of table space during the import will affect performance, so it is advisable to expand the table space in advance based on the size of the CSV file.
>
> - In an ISC Distributed Cluster Deployment, after completing the data import using the bulkload mode, it is recommended to perform operations such as compacting and collecting statistics on the table to improve subsequent query performance.

### Import

The import process of the *yasldr* tool is parallel and will use the value set in the `DEGREE_OF_PARALLELISM` parameter in `Load statements` to start multiple threads for parallel import.

The threads involved in the import process are divided into three types:

- CONTROLLER thread: the main thread, responsible for parsing the `Load Options` and `Load Statement`, splitting the CSV data file to be imported, and starting READER and SENDER threads for data import.
- READER thread: responsible for reading and parsing the CSV file, decoding the data, and organizing the data by partition before passing it to the SENDER thread for sending.
- SENDER thread: responsible for sending the data submitted by the READER thread to the server and parsing the messages returned by the server.

### Import Log

After the import execution is completed, it will display log path information such as `Check /home/yasdb/area_stats.log for more info.`. This log contains information about the target table for this import, the mapping relationship between table fields and CSV data columns, the location and reason for any erroneous data, and statistics, etc.

***Example***

```bash
YashanDB Loader Enterprise Edition Release {version_number} x86_64 a533f66
Production on 2023-10-13 11:06:58.212

Table AREA:
column_name                                                        infile_column
AREA_NO                                                            1
AREA_NAME                                                          2
DHQ                                                                3

infile /home/yasdb/area_stats.csv offset 1 is rejected by table AREA because
type convert error : not a valid number.

Table AREA:
  2 logical rows read.
  1 rows successfully loaded.
  1 rows not loaded due to data errors.
  0 rows not loaded because all fields were null.
```

<span id="statistic" name="statistic"></span>

### Statistics

The logs generated during the import will include the execution statistics of the import process, with the output statistics including:

**Overview**

Statistics are divided into CONTROLLER's Statistics, READER Statistics, SENDER Statistics, and in a distributed situation, NODE Statistics.

**CONTROLLER Statistics**

The CONTROLLER section displays the number of READER and SENDER threads, parsing time, and the time taken to prepare the execution environment.

**READER Statistics**

The READER statistics section displays I/O counts, I/O time, the number of decoded rows, maximum row width, average row width, total time for reading rows, total decoding time, total time for partition calculations, total time for row conversions, total wait time for partition availability, total time for column conversions, total enqueue time, and total elapsed time for the thread.

**SENDER Statistics**

The SENDER statistics section shows send counts, total send time, maximum time per send, total send packet time, total executing submit counts, total submit time, total time for fetching sending units, and total elapsed time for the thread.

**NODE Statistics**

The NODE statistics section shows the number of rows sent to the node, send counts, total send time, maximum time per send, submit counts, total submit time, and address information of the nodes.

***Example***

```bash
# area_stats.csv file content:
1|load|101|loadbranch|1
2|load|201|loadbranch|2
3|load|301|loadbranch|3
4|load|401|loadbranch|4
5|load|501|loadbranch|5
6|load|601|loadbranch|6

# Create table for area
CREATE TABLE area(
area_no   INTEGER,
area_name VARCHAR(10),
dhq       INTEGER);

$ yasldr sales/sales@127.0.0.1:1688 batch_size=4032 control_text="'LOAD DATA OPTIONS(DEGREE_OF_PARALLELISM=2,STATS=TRUE) INFILE '/home/yasdb/area_stats.csv' FIELDS TERMINATED BY '|' OPTIONALLY ENCLOSED BY '\"' APPEND INTO TABLE area(area_no,area_name,dhq)'"

# After execution, statistics example
Reader Execution Statistics:
   id    io_cnt     io_cost    avg_cost     lines   max_len   min_len   avg_len  fetch_cost  decode_cost locate_cost  to_row_cost    wait_cost  to_col_cost   enque_cost      elapsed
    0         1           0           0         6        24        24        24           0            0           0            0         1133            0            0         2225

Sender Execution Statistics:
   id    send_cnt   send_cost    max_cost packet_cost  commit_cnt commit_cost  deque_cost     elapsed
    0           6       49718       15515       49718           2      192412           0      395141

Node Send Statistics:
   id       lines    send_cnt   send_cost    max_cost  commit_cnt commit_cost                       address
    0           3           6       49718       15515           2      192412            xxx.xxx.xxx.xxx:xxxx
    1           3           6       49718       15515           2      192412            xxx.xxx.xxx.xxx:xxxx
```

### Import Result Verification

After the import is completed, the results can be verified to check whether all data has been correctly imported.

Verification can be done by comparing the number of data rows in the CSV file with the number of data rows in the database table.

***Example***
```bash
# area_stats.csv file content:
8|load|801|loadbranch|8
9|load|901|loadbranch|9

# Create table for area
CREATE TABLE area(
area_no   INTEGER,
area_name VARCHAR(10),
dhq       INTEGER);

$ yasldr sales/sales@127.0.0.1:1688 batch_size=4032 control_text="'LOAD DATA OPTIONS(DEGREE_OF_PARALLELISM=2,STATS=TRUE) INFILE '/home/yasdb/area_stats.csv' FIELDS TERMINATED BY '|' OPTIONALLY ENCLOSED BY '\"' APPEND INTO TABLE area(area_no,area_name,dhq)'"
YashanDB Loader Enterprise Edition Release {version_number} x86_64 a533f66
2 rows successfully loaded.
Check /home/yasdb/area_stats.log for more info.
[YASLDR] execute succeeded

# Count the number of data rows in the file
$ wc -l area_stats.csv
2 area_stats.csv

# Check whether the number of rows in the database table matches the file number
$ yasql sales/********@127.0.0.1:1688 -c "select count(*) from area"

             COUNT(*)
---------------------
                    2

1 row fetched.
```
