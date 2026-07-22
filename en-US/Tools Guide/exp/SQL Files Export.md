The *exp* tool supports the use of the --sql parameter to export database metadata to an SQL file.

## Command Format

```shell
exp --sql username/password@ip:port [KEYWORD[=value1,value2,...,valueN]]
```

**username/password@ip:port**

The password and server address of the database user executing the export command (login user). See specific descriptions for privilege requirements for different export modes.

**KEYWORD**

Command options:

- FILE: The name of the SQL file for exporting metadata. It must be provided and will overwrite the existing file with the same name.
- The export modes are as follows; only one mode can be selected for each export. If no export mode is specified in the command, it defaults to FULL=N.
    - FULL: Command for exporting all database metadata to SQL. Value can be either Y or N.
    - OWNER: Command for exporting user metadata to SQL. Value is the username.
    - TABLES: Command for exporting table metadata to SQL. Value is the table name.
- ROWS: Only supports exporting metadata SQL, meaning Value can only be a single value of N. It can be omitted, having the same effect as ROWS=N.
- LOG_PATH: Used to specify the path for the export log. Value is the path name. It can be omitted; if omitted, no log file is generated.
- LOG_LEVEL: Used to specify the log level for the export log. Value can be [ERROR, WARN, INFO, DEBUG, TRACE]. It can be omitted; if omitted, it defaults to INFO.
- HIDDEN_COLUMN: Specifies whether to export hidden columns. Value can be a single Y or N, optional; default is Y if omitted.

***Example***

```shell
$ exp --sql sales/sales@127.0.0.1:1688 FILE=export.full.dump FULL=Y
```

## Command Options

### FULL Mode

FULL mode is used to export the complete database metadata SQL, including:

- User metadata SQL
- Metadata SQL of all objects under the user
- All system privilege metadata SQL
- All role metadata SQL
- All audit policy/enablement metadata SQL
- All OUTLINE metadata SQL
- All PROFILE metadata SQL
- All SQLMAP metadata SQL
- All scheduled job metadata SQL

The database user executing this mode must have DBA role privilege. The meaning of the Value is as follows:

- Y: Export all data under all users.
- N: Export data under the logged-in user.

***Example***

```shell
# Execute the export of complete database metadata SQL
$ exp --sql sales/sales FILE=export.full.dump FULL=Y
```

### OWNER Mode

OWNER mode is used to export metadata SQL under specified users, including:

- All object metadata SQL under the user

The Value for the command in this mode is the username and can be multiple. If the same username is entered multiple times, it will only be exported once. A maximum of 1024 different users can be input.

For the database user executing this mode, if the Value matches this user (i.e., exporting data under this user), there are no privilege requirements; if the Value contains other users (i.e., exporting data under other users), then the DBA role privilege is required.

***Example***

```shell
# Execute Schema metadata SQL export
$ exp --sql sales/sales FILE=export.owner.export OWNER=sales
```

### TABLES Mode

TABLES mode is used to export metadata SQL for specified tables, including:

- Metadata SQL of the table
- Metadata SQL of indexes, comments, constraints, column attributes, etc., based on the table

The Value for the command in this mode is the table name and can be multiple. If the same table name is entered multiple times, it will only be exported once. A maximum of 1024 different tables can be input.

For the database user executing this mode, if they are the OWNER of the exported table, there are no privilege requirements; otherwise, DBA role privilege is required.

***Example***

```shell 
# Execute the export of table metadata SQL
$ exp --sql sales/sales FILE=export.table.export TABLES=sales_info
```

## Character Set Settings

When exporting metadata SQL, use the CHARACTER_SET parameter to set the character set of the export file.

> **Note**:
>
> - The parameter value range is [ASCII, GBK, UTF8, ISO88591, GB18030], affecting the character set of the export file.
> - The character set of command return messages during export is determined by the client character set parameter CHARACTER_SET configured in ${YASDB_HOME}/client/yasc_env.ini.
> - The character set of the export file name should be consistent with the character set of the shell input.
> - Please pay attention to the representation range of the character set. If the set character set does not support parsing certain metadata symbols in the database (e.g., GBK cannot parse table names with emojis), it may lead to invalid or garbled data export.

***Example***

```shell
$ exp --sql sales/sales@127.0.0.1:1688 FILE=%export_file% FULL=Y CHARACTER_SET=GBK
```
