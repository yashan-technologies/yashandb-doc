*exp* tool supports exporting CSV data through two interactive methods: command line and configuration file.

## Exporting Data Using Command Line

### Command Format

```shell
exp --csv {Common Options} {Export Options} {CSV Options}
```

### Command Options - Common Options

The specific options are shown in the table below, where [] indicates optional options.

|Option |Meaning |
|---------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| *-f, --format*              | Data file format, csv                                                                                                                                                         |
| *-u, --user*                | Database username, either system user or regular user; regular user needs to have access to system table privilege                                                              |
| *-p, --password*            | Database user password                                                                                                                                                         |
| *[-L, --logfile]*           | Log file path; if not specified, logs will be printed on the front-end directly; supports relative paths (not ../ relative paths)                                           |
| *[-F, --file]*              | CSV file export path; if not specified, exports to the current path; supports relative paths (not ../ relative paths)                                                        |
| *[--lob]*                   | Set the export method for LOB data; selectable values are lls/csv; default value is lls:<br> lls indicates that LOB data is exported separately to a single file, and the name of that file is recorded in the current CSV file; the file name format is described in the [LOAD DATA](../../Development Guide/SQL Reference Manual/SQL Statements/LOAD DATA) lls_column_clause<br> csv indicates that LOB is exported to the current CSV file |
| *[--inline-blob-format]*    | When --lob is set to csv, this option sets the export method for BLOB data; selectable values are binary/string; default value is binary:<br/>binary indicates that the BLOB is stored in binary format<br/>string indicates that the BLOB is stored in string format                                                                        |
| *[--loglevel]*              | Log level, off/error/warn/info/debug/trace; default value is info                                                                                                           |
| *[--server-host]*           | Database IP and listening port; default value is "127.0.0.1:1688"                                                                                                    |
| *[--use-threads]*           | Number of working threads; default value is 1                                                                                                                                 |
| *[-q, --query]*             | Set the query statement to export the corresponding result set                                                                                                              |
| *[-qf, --query-file]*       | Specify SQL file to export the corresponding result set from the queries in the file                                                                                       |
| *[-qo, --query-out-file]*   | Set the file name for the exported result set from the query statement; used in conjunction with the -F parameter to specify the path; if not configured, the exported file name will be outfile         |
| *[--bit-format]*            | Set the export method for BIT data; selectable values are binary/decimal; default value is binary:<br/>binary indicates that BIT column data is exported in binary string format<br/>decimal indicates that BIT column data is exported in decimal string format                                                                                      |
| *[--hidden-column]*         | Set whether to export hidden column data; selectable values are true/false; default value is true                                              |
| *[-ch, --character-set]*    | Set the character set of the exported file; valid values are [ASCII,GBK,UTF8,ISO88591,GB18030]; default value is the same as the client character set                               |

> **Note**: 
>
> - If the export fails, the specified export file will be deleted; it is recommended to specify a different export file name each time to avoid mistakenly deleting a successfully exported file with the same name.
> - Command line parameters should be less than 16KB.

#### Parameter Description

##### query-file Parameter

- The --query or --query-file parameter is mutually exclusive with the --table and --owner parameters; you can only choose one mode when exporting.
- The --query and --query-file parameters can specify the schema of the corresponding table; if not specified, the default is the schema of the user logging in with -u.
- The --query and --query-file parameters only support exporting a single SQL statement; --query does not support newlines or comments, while --query-file supports newlines and comments in the SQL file.
- If --query and --query-file are specified at the same time, --query takes precedence.
- When the --query or --query-file parameter is effective, the default value of --use-threads is set to 1, and multi-threaded export is not possible.

##### character-set Parameter

- The -ch parameter affects the character set of the exported CSV and the character set of the LOB file.
- During export, the character set of the command return information and the exported log file is determined by the client character set parameter CHARACTER_SET configured in ${YASDB_HOME}/client/yasc_env.ini.
- When exporting CSV, the character set of the file name specified by -qo and -T should be consistent with the shell input character set.
- When exporting SQL result set to CSV, the character set of the specified -qf parameter is determined by the client character set parameter CHARACTER_SET configured in ${YASDB_HOME}/client/yasc_env.ini.
- When exporting CSV using a configuration file, the character set for parsing the configuration file exp.ini is determined by the client character set parameter CHARACTER_SET configured in ${YASDB_HOME}/client/yasc_env.ini.
- When exporting date format data types to CSV, the character set for parsing the --date-format parameter is determined by the client character set parameter CHARACTER_SET configured in ${YASDB_HOME}/client/yasc_env.ini.

### Command Options - Export Options

The specific options are shown in the table below.

|Option |Meaning |
| --- | --- |
| *-T, --tables*               | Name of the exported table; multiple tables are separated by commas     |
| *-O, --owner*                | The owner user of the table; only one can be specified                |

### Command Options - CSV Options

The specific options are shown in the table below, where [] indicates optional options.

|Option |Meaning |
|----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| *[--fields-enclosed-by]*     | Specify a single character as the surrounding symbol for each field in the file; the character range is ASCII code 1-126 corresponding characters, excluding newline and space. The default value is `"`; supports character and hexadecimal representation<br/>Note:<br/>* Numeric, bool, rowid, and bit types are exported without surrounding symbols.<br/>* If the character type (char, nchar, varchar, nvarchar) or date/time types (date, time, timestamp, timestamp_ltz, timestamp_tz) field contains the specified surrounding symbol, it will be escaped with two surrounding symbols during export.<br/>* If specified as null, no surrounding symbols will be added for all data types during export. |
| *[--fields-terminated-by]*   | Supports the use of up to five characters as field separators; the character range is ASCII code 1-126 corresponding characters, excluding letters, numbers, and newline. The default value is `|`, supports character and hexadecimal representation          |
| *[--lines-terminated-by]*    | Use a single character as the line separator; this character can be any ASCII character, default value is `\n`                                                                                                                    |
| *[--date-format]*            | Supports modification of date format, consistent with all date formats supported by the database, default value is `yyyy-mm-dd`                                                                                                   |
| *[--time-format]*            | Supports modification of time format, consistent with all time formats supported by the database, default value is `hh24:mi:ss.ff`                                                                                                 |
| *[--timestamp-format]*       | Supports modification of SCN format, consistent with all SCN formats supported by the database, default value is `yyyy-mm-dd hh24:mi:ss.ff`                                                                                       |
| *[--timestamp-tz-format]*    | Supports modification of timezone format, consistent with all timezone formats supported by the database, default value is `yyyy-mm-dd hh24:mi:ss.ff tzh:tzm`                                                                    |

#### Parameter Description

##### Surrounding Symbols and Separators

- Surrounding symbols, line separators, and field separators cannot be the same.
- When specifying separators or surrounding symbols in the command line, character forms should be enclosed in single quotes; if not enclosed, special characters should be escaped; hexadecimal forms do not require single quotes.
- To specify character forms with single quotes, for example --fields-terminated-by `'"'`, --fields-terminated-by `'@'`.
- When specifying character forms without quotes, characters that require escaping: `'` can be escaped as `\'\'` or as `\'`, `` ` ``, `"`, `#`, `&`, `(`, `)`, `*`, `;`, `<`, `>`, `|`, `~`, `\` are all escaped using `\`, for example --fields-enclosed-by `\"`, --fields-enclosed-by `\&`.
- When specifying that --fields-enclosed-by is set to null to avoid using surrounding symbols, ensure that no repetition of data with the separator occurs, or errors in data import will occur.
- Hexadecimal forms can be specified and do not require single quotes, for example --fields-terminated-by `0x01`.
- When specifying single quotes as surrounding symbols or separators in the command line, they can be escaped as `''`, for example --fields-terminated-by `"''"`, --fields-terminated-by `"'"`, --fields-enclosed-by `\'\'`, --fields-enclosed-by `\'`.
- When specifying spaces, horizontal tabs `\t`, and page breaks `\r` as surrounding symbols or separators in the command line, use double quotes plus single quotes for enclosure, for example --fields-terminated-by `"' '"`, --fields-terminated-by `"'$(echo -e "\t")'"`.
- When specifying multiple characters as separators in the command line, refer to --fields-terminated-by `"'$(echo -e "\t\r\t")'"`, --fields-terminated-by `"'''"`, --fields-terminated-by `'???'`.

## Exporting Data Using Configuration File

### Configuration Template

Export option configuration can follow the configuration template below:

```ini
owner = sales
format = csv
user = sys
password = sys
server-host = 127.0.0.1:1688
tables = area,branches
logfile = /home/yasdb
file = /home/yasdb
loglevel = info
fields-enclosed-by = "
fields-terminated-by = ,
```

> **Note**: 
>
> - Parameters in the configuration file that are the same as those in the command line take precedence from the command line.
> - If the same parameter is configured multiple times, the last configuration takes precedence.
> - Please replace the values of each item in the above configuration file with the actual values.
> - The paths above can be configured as relative paths but cannot be ../ relative paths.
> - When configuring the character forms of space, horizontal tabs `\t`, and page breaks `\r` as separators or surrounding symbols in the configuration file, use single quotes for enclosure, for example fields-terminated-by = `' '`, and when specifying hexadecimal or single quotes as separators or surrounding symbols, no enclosure is needed, for example fields-terminated-by = `''`, fields-terminated-by = `0x02`.
> - When specifying multiple characters as separators in the configuration file, refer to fields-terminated-by = `'''`, fields-terminated-by = `'???'`.

### Operation Example

1. As an example, exporting the sample tables area and branches of the sales user in YashanDB.

2. Create a new export task configuration file expconfig.ini:

    ```ini
    owner = sales
    format = csv
    user = sys
    password = sys
    server-host = 127.0.0.1:1688
    tables = area,branches
    logfile = /home/yasdb
    file = /home/yasdb
    loglevel = info
    fields-enclosed-by = "
    fields-terminated-by = ,
    ```

3. Execute the export command exp --csv --config-file expconfig.ini:

    ```shell
    $ exp --csv --config-file expconfig.ini
    YashanDB Export Release 22.2.0.0 x86_64 8f54fba
    [EXPORT] export sales.area succeeded
    [EXPORT] export sales.branches succeeded
    ```

4. The generated CSV data files named by table name are located in the export path /home/yasdb:

    ```shell
    $ ll
    total 1792
    -rw-r-----.  1 yasdb yasdb     121 Nov  6 00:06 area
    -rw-r-----.  1 yasdb yasdb     332 Nov  6 00:06 branches

    $ vi area
    "01","EastChina","Shanghai"
    "02","WestChina","Chengdu
    "03","SouthChina","Guangzhou"
    "04","NorthChina","Beijing"
    "05","CentralChina","Wuhan"
    ```
