DBMS_DATAMASK provides data masking interfaces, which are used for dynamic masking of data.



When using data masking in YashanDB, the following constraints must be adhered to:

- This package can only be called in the projection columns of a table-less query. The executing user must have the SECURITY_ADMIN role or be the sys user. The specific syntax format is `SELECT DBMS_DATAMASK.ADD_RULE(……) …… FROM DUAL`. It cannot be called through PL syntax such as the CALL statement or anonymous blocks.

- Data masking only applies to non-UDT columns and non-hidden columns of HEAP tables and cannot be used for temporary tables.

    >**Note**:
    >
    > If you correctly configure a data masking strategy for a non-UDT column in a HEAP table, then delete the table, and later recreate the table and the column with the same name but change the data type of the column with the same name to UDT, the original data masking strategy will automatically become invalid.

- Columns configured with data masking policies cannot be used for filtering or screening operations.

- It is not recommended to configure policies with data masking results that may result in null values for columns with a not-null constraint. Otherwise, errors will be returned due to null values during queries.

- There is no data masking related functionality in ISC Distributed Cluster Deployment.



## ADD_RULE

```plsql
DBMS_DATAMASK.ADD_RULE(
    POL_NAME                IN VARCHAR,
    OBJ_NAME                IN VARCHAR,
    USERNAME                IN VARCHAR,
    FUNDESC                 IN VARCHAR,
    PARAMLIST               IN VARCHAR,
    REGEXP_PATTERN          IN VARCHAR,
    REGEXP_REPLACE_STRING   IN VARCHAR,
    REGEXP_POSITION         IN BIGINT,
    REGEXP_OCCURRENCE       IN BIGINT,
    REGEXP_MATCH_PARAMETER  IN VARCHAR);
```
DBMS_DATAMASK.ADD_RULE is used to add masking rules for single column data.

|Parameter |Description |
|:---------------------|:----------------------------------------------------------------------------------------------------------------------------------------------|
| POL_NAME             | The name of the policy, which is globally unique. It can only be of CHAR or VARCHAR type, with a maximum length of 64 bytes. It is not allowed to be empty and is case-sensitive.                               |
| OBJ_NAME             | The object to which the policy is added, which can only be of CHAR or VARCHAR type and has a maximum length of 64 bytes. It is not allowed to be empty, and the format is '[schema.]table.column', which means adding a policy to the column in the table of the schema user. The schema. can be omitted when the table where the target column is located belongs to the current user. <br/>Configuration requirements: <br/>* It must be specified as an existing column <br/>* The data type of the target column cannot be UDT<br/>* Only one column can be specified at a time             |
| USERNAME             | The user to whom the policy is applied, which can only be of CHAR or VARCHAR type and has a maximum length of 64 bytes.<br />Only a single existing username or an empty value (NULL, an empty string, or omitting this parameter) is supported. An empty value means the policy applies to all users. |
| FUNDESC             | The name of the data masking function, which can only be of CHAR or VARCHAR type and has a maximum length of 64 bytes.<br />Optional values include EMAIL_MASK, PREFIX_EMAIL_MEDIUM, PREFIX_EMAIL_LOW, PARTIAL, RANDOM, REG_EXP, SHUFFEL, DEFAULT, or NULL. When it is NULL, the default value DEFAULT is used. The details of the specific data masking policies are described below. |
| PARAMLIST            | The number of characters to remain unchanged during custom partial data masking, which can only be of CHAR or VARCHAR type, with a maximum length of 4000 bytes, in the format `'n,m'`, indicating that the first n digits and the last m digits remain unchanged. The values of n and m must be in the range [0, 2<sup>63</sup>). When specified as decimals, they are rounded to the nearest integer. If n exceeds the total length of the original data, the original data is retained without any masking.<br />This parameter can only be configured when FUNDESC is specified as PARTIAL. When other strategies are specified, this parameter can only be NULL or ',' (equivalent to NULL). |
| REGEXP_PATTERN | Regular expression matching pattern, that is, specifying the strings to be masked. It can only be of CHAR or VARCHAR type, with a maximum length of 512 bytes. When specified as empty, it means returning the real data without masking.<br />This parameter can only be configured when FUNDESC is specified as REG_EXP. When other strategies are specified, this parameter can only be NULL. |
| REGEXP_REPLACE_STRING | The strings used to replace the original data, which can only be of CHAR or VARCHAR type, with a maximum length of 4000 bytes. When omitted, an empty string is used.<br />This parameter can only be configured when FUNDESC is specified as REG_EXP. When other strategies are specified, this parameter can only be NULL. |
| REGEXP_POSITION | The offset at which the matching starts, which need to be of BIGINT type or other types that can be converted to BIGINT type. The value is rounded to an integer, and the rounded value range should be in [1, 65535]. It can be omitted, and the default value is 1.<br />This parameter can only be configured when FUNDESC is specified as REG_EXP. When other strategies are specified, this parameter can only be NULL. |
| REGEXP_OCCURRENCE | The target position for replacement, which need to be of BIGINT type or other types that can be converted to BIGINT type. The value is rounded to an integer, and the rounded value range should be [0, 65534]. It can be omitted, and the default value is 0.<br />\* 0: Replace all matching characters<br />\* n: Replace only the nth matching character<br />This parameter can only be configured when FUNDESC is specified as REG_EXP. When other strategies are specified, this parameter can only be NULL. |
| REGEXP_MATCH_PARAMETER | Regular expression matching parameters, that is, specifying rules such as case - sensitivity, line - breaks, and multi - line for regular matching. It can only be of CHAR or VARCHAR type, with a maximum length of 10 bytes, and the values are combinations of `c/i/n/m/x` or an empty string.<br />\* c: Case-sensitive, case - sensitive by default<br />* i: Case-insensitive<br />\* n: Allow the period (`.`) to match line-break characters, does not match by default<br />\* m: Multi-line mode, use `^` to match the start of each line and `$` to match the end of each line. By default, only the start and end of the entire string are matched<br />\* x: Ignore whitespace characters, does not ignore by default<br />\* Empty string: Use the default behavior, that is, case-sensitive, the period (`.`) does not match line-breaks, single-line mode<br />This parameter can only be configured when FUNDESC is specified as REG_EXP. When other strategies are specified, this parameter can only be NULL. |



The data masking rules supported by YashanDB are shown in the following table. All types of data masking rules will mask data of inapplicable data types to null values.

|Function |Rule Type  |Applicable Data Types      |Data masking Effect         |
| ------------ | -------------- | ---------------------- | --------------- |
| DEFAULT      | Default full data masking | CHAR, VARCHAR<br />TINYINT, SMALLINT, INT, BIGINT, FLOAT, DOUBLE, NUMBER, BIT<br />BOOLEAN<br />DATE, TIMESTAMP, LTZ, TZ, INTERVAL YEAR TO MONTH, INTERVAL DAY TO SECOND | * For character types: Null<br />* For numeric types: 0 (precision is not guaranteed)<br />* For boolean types: FALSE<br />* For date - time types (except TIME type): Date part 1970-01-01, time part 00:00:00 |
| EMAIL_MASK   | Email data masking | CHAR, VARCHAR | Data masking according to character set:<br />* Email strings: retain the `@` symbol and replace other parts character by character with asterisks (`*`). For example, `yashan@sics.ac.cn` will be masked to `******@**********`<br />* Non-email strings: replace each character with asterisks (`*`). For example, `yashan` will be masked to `******` |
| PREFIX_EMAIL_LOW | Email prefix data masking (low) | CHAR, VARCHAR | Data masking according to character set:<br />* Email strings: remain the first 3 characters of the prefix and replace the rest part of the prefix with 3 asterisks (`***`). If the prefix has 3 characters or fewer, retain the entire prefix and append 3 asterisks (`***`) between the prefix and the `@` symbol. For example, `yashan@sics.ac.cn` will be masked to `yas***@sics.ac.cn`, and `ya@sics.ac.cn` will be masked to `ya***@sics.ac.cn` <br />* Non-email strings: replace each character with asterisks (`*`). For example, `yashan` will be masked to `******` |
| PREFIX_EMAIL_MEDIUM | Email prefix data masking (medium) | CHAR, VARCHAR | Data masking according to character set:<br />* Email strings: replace each character of the prefix with asterisks (`*`). For example, `yashan@sics.ac.cn` will be masked to `******@sics.ac.cn`, and `ya@sics.ac.cn` will be masked to `**@sics.ac.cn` <br />* Non-email strings: replace each character with asterisks (`*`). For example, `yashan` will be masked to `******` |
| PARTIAL      | Custom partial data masking | CHAR, VARCHAR           | Based on the specified parameters, remain the first n characters and the last m characters unchanged, and replace the middle part character by character with asterisks (`*`) according to the character set. For example, if the parameter is specified as `(2,1)`, `yashan` will be desensitized to `ya***n` |
| RANDOM       | Random data masking | CHAR, VARCHAR           | Replace all characters one by one with any printable characters, and the result may vary for each query. For example, `yashan` may be desensitized to `9SV;1l` |
| REG_EXP      | Regular expression data masking | CHAR, VARCHAR           | Based on the regular expression matching the format of the target data, replace the matched content according to the character set. |
| SHUFFLE      | Shuffled data masking | CHAR, VARCHAR           | Randomly rearrange the positions of all characters within the string, and the result may be different for each query. For example, `yashan database` may be masked to `eaa aasandyshbt` |



***Example*** for Heap tables

```plsql
-- When no masking policy is created, the data presents the actual values directly
SELECT employee_no,employee_name,entry_date from employees;

EMPLOYEE_NO               EMPLOYEE_NAME             ENTRY_DATE
------------------------- ------------------------- --------------------------------
0101000001                Mask                      2022-06-17 10:45:14
0101000002                John                      2019-09-21 10:45:14
0201010011                Anna                      2024-05-17 10:45:14
0201008003                Jack                      2023-04-13 10:45:14
0201008004                Jim                       2024-08-25 10:45:14

-- Create a masking policy pol1 for the employee_name column in the employees table, with the rule of partial masking (keeping 1 character on each end)
SELECT dbms_datamask.add_rule('pol1','employees.employee_name','sales','partial','1,1') pol1 from dual;

        POL1
------------
            
-- Create a masking policy pol2 for the entry_date column in the employees table, using the default rule (date type replaced with 1970-01-01 00:00:00)
SELECT dbms_datamask.add_rule('pol2','employees.entry_date','sales','','') pol2 from dual;

        POL2
------------

-- The data query result set presents the masked content
SELECT employee_no,employee_name,entry_date from employees;

EMPLOYEE_NO               EMPLOYEE_NAME             ENTRY_DATE
------------------------- ------------------------- --------------------------------
0101000001                M**k                      1970-01-01 00:00:00
0101000002                J**n                      1970-01-01 00:00:00
0201010011                A**a                      1970-01-01 00:00:00
0201008003                J**k                      1970-01-01 00:00:00
0201008004                J*m                       1970-01-01 00:00:00
```

## DEL_RULE

```plsql
DBMS_DATAMASK.DEL_RULE(
    POL_NAME   IN  VARCHAR);
```
DBMS_DATAMASK.DEL_RULE is used to delete masking policies for single column data.

***Example*** for Heap tables
```plsql
SELECT employee_no,employee_name,entry_date from employees;

EMPLOYEE_NO               EMPLOYEE_NAME             ENTRY_DATE
------------------------- ------------------------- --------------------------------
0101000001                M**k                      1970-01-01 00:00:00
0101000002                J**n                      1970-01-01 00:00:00
0201010011                A**a                      1970-01-01 00:00:00
0201008003                J**k                      1970-01-01 00:00:00
0201008004                J*m                       1970-01-01 00:00:00

-- Delete the masking policy pol1
SELECT dbms_datamask.del_rule('pol1') from dual;

DBMS_DATAMASK.DEL_RU
--------------------

-- In the data query result set, the employee_name column shows the original data
SELECT employee_no,employee_name,entry_date from employees;

EMPLOYEE_NO               EMPLOYEE_NAME             ENTRY_DATE
------------------------- ------------------------- --------------------------------
0101000001                Mask                      1970-01-01 00:00:00
0101000002                John                      1970-01-01 00:00:00
0201010011                Anna                      1970-01-01 00:00:00
0201008003                Jack                      1970-01-01 00:00:00
0201008004                Jim                       1970-01-01 00:00:00
```
