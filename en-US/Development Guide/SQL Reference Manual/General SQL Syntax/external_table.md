**[external\_table\_clause](#externaltableclause)::=**

```ebnf+diagram
syntax::=  "(" [TYPE access_driver_type] [external_table_data_props_clause] ")" [REJECT LIMIT (integer|UNLIMITED)]
```

**[external\_table\_data\_props\_clause](#externaltabledatapropsclause)::=**

```ebnf+diagram
syntax::=  [DEFAULT DIRECTORY directory_name] [ACCESS PARAMETERS "(" [record_format_info_clause] [field_definitions_clause] ")"] [LOCATION "(" [directory_name ":"] "'" location_specifier "'" ")"]
```

**[record\_format\_info\_clause](#recordformatinfoclause)::=**

```ebnf+diagram
syntax::=  RECORDS ( (DELIMITED BY NEWLINE) | (output_files_clause)) {"" ((DELIMITED BY NEWLINE) | (output_files_clause))}
```

**[output\_files\_clause](#outputfilesclause)::=**

```ebnf+diagram
syntax::=  ( (NOBADFILE | BADFILE file_pos_clause) | (NOLOGFILE | LOGFILE file_pos_clause) ) {"" ( (NOBADFILE | BADFILE file_pos_clause) | (NOLOGFILE | LOGFILE file_pos_clause) )}
```

**[file\_pos\_clause](#fileposclause)::=**

```ebnf+diagram
syntax::=  [directory_name ":"]  "'" file_name "'"
```

**[field\_definitions\_clause](#fielddefinitionsclause)::=**

```ebnf+diagram
syntax::=  FIELDS ( (TERMINATED BY "'"char"'") | (OPTIONALLY ENCLOSED BY "'"char"'") ) {"" ( (TERMINATED BY "'"char"'") | (OPTIONALLY ENCLOSED BY "'"char"'") )}
syntax::=  FIELDS ( (TERMINATED BY ("'"char"'" | ("0x" | "x") "'" "9" "'")) | (OPTIONALLY ENCLOSED BY "'"char"'") ) {"" ( ((TERMINATED BY ("'"char"'" | ("0x" | "x") "'" "9" "'")) | (OPTIONALLY ENCLOSED BY "'"char"'")) )}
```

<span id="externaltableclause" name="externaltableclause" class="yaslink"></span>

The external_table_clause is used to specify the corresponding external data information when creating an external table.

This statement does not verify whether external data exists and is correct. As long as the syntax is correct, the creation will succeed. When querying an external table, the system retrieves data based on the external data information defined by this statement, resulting in an error if the conditions are not met.

### TYPE

The TYPE keyword specifies the external table driver name and can be omitted. The values for access_driver_type can be YASDB_LOADER or ORACLE_LOADER, which are equivalent.

<span id="externaltabledatapropsclause" name="externaltabledatapropsclause" class="yaslink"></span>

### external\_table\_data\_props\_clause

Specifies the attributes of the data in the external table and is optional.

#### DEFAULT DIRECTORY

Specifies the default directory where the data file corresponding to the external table is located and is optional. If directory_name is not specified in subsequent statements, this directory will be used as the default directory for data or log files.

directory_name must be a directory object created in YashanDB. Refer to [CREATE DIRECTORY](../SQL Statements (yashan Mode)/CREATE DIRECTORY) for creation methods.

#### ACCESS PARAMETERS

Specifies the parameters for reading the external data file when querying the external table, including the source file name, column format, and logging information.

<span id="recordformatinfoclause" name="recordformatinfoclause" class="yaslink"></span>

#### record\_format\_info\_clause

Specifies the data row format and is optional.

##### DELIMITED BY NEWLINE

Specifies that the delimiter between data records in the external data file is a newline character.

<span id="outputfilesclause" name="outputfilesclause" class="yaslink"></span>

##### output\_files\_clause

Specifies the logging information when the database reads external data. If this clause is omitted, the system will create a log file with a default name for recording.

###### NOBADFILE | BADFILE

When BADFILE is specified, erroneous data encountered during the query of external data will be recorded in the file specified by file_pos_clause.

When NOBADFILE is specified, the system will not record erroneous data during the query of external data.

###### NOLOGFILE | LOGFILE

When LOGFILE is specified, operations querying external data will be recorded in the file specified by file_pos_clause.

When NOLOGFILE is specified, the system will not record operational information during the query of external data.

<span id="fileposclause" name="fileposclause" class="yaslink"></span>

###### file\_pos\_clause

Specifies the physical file corresponding to the above badfile or logfile, formatted as directory_name:file_name.

directory_name must be a directory object created in YashanDB, and if omitted, it defaults to the directory object specified by DEFAULT DIRECTORY.

>**Note**:
>
> For performance considerations, it is **not recommended** to set the directories for badfile and logfile in YFS in YAC Deployment.

file_name must be an operating system-acceptable file name. If the file does not exist, the system will create it during logging, and if it already exists, the system will overwrite badfile and append logfile data.

For file_name, the database will replace some symbols:

- `%%` will be replaced with `%`;

- `%a` and `%p` will be replaced with the current thread ID.

<span id="fielddefinitionsclause" name="fielddefinitionsclause" class="yaslink"></span>

#### field\_definitions\_clause

Specifies the column format of the external data and is optional.

##### TERMINATED BY

Specifies the delimiter between fields in the external data file. The delimiter can only be a character; 0x'9' and x'9' are parsed as tab characters, and it should match the actual delimiter used in the external data file. If omitted, the default is ','.

##### OPTIONALLY ENCLOSED BY

If the external data file uses enclosing symbols (only '"'), this needs to be specified in this statement and can only be specified as '"'.

#### LOCATION

Specifies the path where the external data file is located, formatted as (directory_name:location_specifier).

directory_name must be a directory object created in YashanDB, and if omitted, it defaults to the directory object specified by DEFAULT DIRECTORY.

location_specifier is the name of an existing CSV file.

### REJECT LIMIT

Specifies the maximum number of erroneous data rows that can be tolerated when querying external data. If the threshold is reached, the system will terminate the query operation. It is optional; the default is 0 rows. UNLIMITED means errors in all data are allowed.

***Example***

```sql
-- The following data exists in /data/area.csv:
8|load|801|loadbranch|8
9|load|901|loadbranch|9

-- Create directory object and external table in the database
CREATE DIRECTORY dir_ext AS '/data';
CREATE TABLE area_external(
 area_no CHAR(2),
 area_name VARCHAR2(60),
 DHQ VARCHAR2(20) DEFAULT 'ShenZhen')
ORGANIZATION EXTERNAL(
 TYPE YASDB_LOADER
 DEFAULT DIRECTORY dir_ext
 ACCESS PARAMETERS(
  RECORDS BADFILE 'area_bad.log' LOGFILE 'area_log.log'
  FIELDS TERMINATED BY ',')
 LOCATION ('area.csv')
)
REJECT LIMIT 1;

-- Query table data, as the data format in the file does not match the table definition format,
SELECT * FROM area_external;
YAS-02687 reject limit reached
```
