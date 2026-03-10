## discovery ac

This command can discover recommended AC statements based on the SQL file or historical SQL specified by the user.

|Option |Meaning |
| ---------------- | ------------------------------------------------------------ |
| *-c, --cluster*  | The cluster name of YashanDB (required parameter)          |
| *--file*         | Specify the SQL file, choose between file and history      |
| *--history*      | Use recorded historical SQL, choose between history and file|
| *--explain*      | Specify SQL file to check the correctness of SQL           |
| *-s, --schema*   | Specify the schema for the SQL file, default is SYS       |
| *-sp, --schema-password* | Specify the schema password                     |
| *-f, --force*    | Skip confirmation prompt for explain parameter              |
| *-m, --mode*     | Specify AC discovery mode, options are 'NORMAL' and 'BASE' for normal and basic modes |
| *-o, --output*   | Specify output path to generate AC list file               |
| *--parallelism*  | Parallelism for explain and SQL parsing, range from 1-16, default is 1 |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified   |

***Example***

```shell
$ yasboot discovery ac -c yashandb --file ac.sql

$ yasboot discovery ac -c yashandb --history
```

**SQL File Specifications**

The current specifications for a single SQL file are: file size must not exceed 200M, and the number of SQL statements must not exceed 1 million.

**Example of ac.sql**

```sql
SELECT a, b FROM yasom.test01 WHERE c=2 AND d=3;
SELECT a FROM "yasom".test01 WHERE b=3 AND d=7;
```

**AC Discovery Results**

```text
+------------------------------------------------------------------------------------------------------------------------+
| Schema | Table  | Statement                                                                                            |
+------------------------------------------------------------------------------------------------------------------------+
| YASOM  | TEST01 | CREATE ACCESS CONSTRAINT YASOM.YASOM_TEST01_AC1 FROM YASOM.TEST01 ON C,D TO A,B;                     |
+--------+--------+------------------------------------------------------------------------------------------------------+
| yasom  | TEST01 | CREATE ACCESS CONSTRAINT "yasom".YASOM_TEST01_AC2 FROM "yasom".TEST01 ON B,D TO A;                   |
+--------+--------+------------------------------------------------------------------------------------------------------+
```
