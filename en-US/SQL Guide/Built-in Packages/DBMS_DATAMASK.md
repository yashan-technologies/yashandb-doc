DBMS_DATAMASK provides data masking interfaces for masking single column data.

Only users with sys privileges or those with the SECURITY_ADMIN role can execute this advanced package.

There is no data masking related functionality in ISC Distributed Cluster Deployment.

## ADD\_RULE

```plsql
DBMS_DATAMASK.ADD_RULE(
    POL_NAME   IN  VARCHAR,
    OBJ_NAME   IN  VARCHAR,
    USERNAME   IN  VARCHAR,
    FUNDESC    IN  VARCHAR,
    PARAMLIST  IN  VARCHAR);
```
DBMS_DATAMASK.ADD_RULE is used to add masking rules for single column data.

This advanced package follows the rules below:

- Only supports adding masking rules on non-hidden columns of HEAP tables.

- Does not support filter operations on columns with masking rules; otherwise, an error will be returned.

- If a strategy that causes non-empty columns to return null is added, an error will be returned during the query phase.

|Parameter |Description |
|:---------------------|:----------------------------------------------------------------------------------------------------------------------------------------------|
| POL_NAME             | Policy name, must be of CHAR or VARCHAR type, a maximum of 64 bytes, cannot be null, case-sensitive, uniquely identifies a policy.                               |
| OBJ_NAME             | The object to which the policy is added, must be CHAR or VARCHAR type, each field a maximum of 64 bytes, cannot be null, formatted as schema.table.column or table.column; the latter assumes schema is the current user and applies to the column of the table under the schema user.   <br/> Only 1 column can be specified at a time.             |
| USERNAME             | The user to whom the policy is applied, which can only be of CHAR or VARCHAR type and has a maximum length of 64 bytes.<br />Only a single username, ALL, or NULL is supported. When it is NULL, the default value ALL is used, indicating that the policy is applied to all users. |
| FUNDESC             | The masking strategy, which can only be of CHAR or VARCHAR type and has a maximum length of 64 bytes.<br />Optional values include EMAIL_MASK, PARTIAL, DEFAULT, or NULL. When it is NULL, the default value DEFAULT is used. The details of the specific desensitization policies are described below. |
| PARAMLIST            | Parameters applied when choosing the PARTIAL strategy, must be CHAR or VARCHAR type, a maximum of 4000 bytes; formatted as 'n, m', representing keeping the first n and the last m digits.<br />This parameter can only be configured when FUNDESC is specified as PARTIAL. When other strategies are specified, this parameter can only be NULL or ',' (equivalent to NULL). |

Currently supported masking strategies:

- DEFAULT: Default masking strategy. The masking results for various data types are as follows:

  - Numeric type: 0 (precision not guaranteed)

  - Boolean type: FALSE

  - Date type (excluding TIME type): year, month, day part 1970-01-01, hour, minute, second part 00:00:00

  - Other types: null

- PARTIAL: Partial masking (also known as "custom masking"), which supports masking data of VARCHAR/CHAR types. For other types, a null value is returned.

  - When using the PARTIAL strategy and the PARAMLIST parameter is not NULL, there must be exactly two positive parametersIf the parameters are decimals, they will be rounded to positive integers. The two parameters are separated by a comma ','. The first parameter represents the first n digits to be retained, and the second parameter represents the last m digits to be retained. The remaining digits are masked with '*'. For example, if the parameter passed in is '1,1', "test" will be masked to "t**t".

  - When using the PARTIAL strategy and the PARAMLIST parameter is NULL or the specified number of reserved digits (either at the front or back) exceeds the length of the string, no masking operation will be performed on the data, and the original string will be output.
  
- EMAIL_MASK: Exclusive masking for strings in email format. It supports masking data of VARCHAR/CHAR types, and returns a null value for other types. In the masked data, only the @ symbol is retained and all other characters are replaced with `*` in the email address. For example, "123@sics.ac.cn" is desensitized to "*\*\*\*@**********". 

***Example*** for Heap tables

```plsql
-- When no masking policy is created, the data presents the actual values directly
SELECT employee_no,employee_name,entry_date FROM employees;

EMPLOYEE_NO               EMPLOYEE_NAME             ENTRY_DATE
------------------------- ------------------------- --------------------------------
0101000001                Mask                      2022-06-17 10:45:14
0101000002                John                      2019-09-21 10:45:14
0201010011                Anna                      2024-05-17 10:45:14
0201008003                Jack                      2023-04-13 10:45:14
0201008004                Jim                       2024-08-25 10:45:14

-- Create a masking policy pol1 for the employee_name column in the employees table, with the rule of partial masking (keeping 1 character on each end)
SELECT dbms_datamask.add_rule('pol1','employees.employee_name','sales','partial','1,1') pol1 FROM dual;

        POL1
------------
            
-- Create a masking policy pol2 for the entry_date column in the employees table, using the default rule (date type replaced with 1970-01-01 00:00:00)
SELECT dbms_datamask.add_rule('pol2','employees.entry_date','sales','','') pol2 FROM dual;

        POL2
------------

-- The data query result set presents the masked content
SELECT employee_no,employee_name,entry_date FROM employees;

EMPLOYEE_NO               EMPLOYEE_NAME             ENTRY_DATE
------------------------- ------------------------- --------------------------------
0101000001                M**k                      1970-01-01 00:00:00
0101000002                J**n                      1970-01-01 00:00:00
0201010011                A**a                      1970-01-01 00:00:00
0201008003                J**k                      1970-01-01 00:00:00
0201008004                J*m                       1970-01-01 00:00:00
```

## DEL\_RULE

```plsql
DBMS_DATAMASK.DEL_RULE(
    POL_NAME   IN  VARCHAR);
```
DBMS_DATAMASK.DEL_RULE is used to delete masking policies for single column data.

***Example*** for Heap tables
```plsql
SELECT employee_no,employee_name,entry_date FROM employees;

EMPLOYEE_NO               EMPLOYEE_NAME             ENTRY_DATE
------------------------- ------------------------- --------------------------------
0101000001                M**k                      1970-01-01 00:00:00
0101000002                J**n                      1970-01-01 00:00:00
0201010011                A**a                      1970-01-01 00:00:00
0201008003                J**k                      1970-01-01 00:00:00
0201008004                J*m                       1970-01-01 00:00:00

-- Delete the masking policy pol1
SELECT dbms_datamask.del_rule('pol1') FROM dual;

DBMS_DATAMASK.DEL_RU
--------------------

-- In the data query result set, the employee_name column shows the original data
SELECT employee_no,employee_name,entry_date FROM employees;

EMPLOYEE_NO               EMPLOYEE_NAME             ENTRY_DATE
------------------------- ------------------------- --------------------------------
0101000001                Mask                      1970-01-01 00:00:00
0101000002                John                      1970-01-01 00:00:00
0201010011                Anna                      1970-01-01 00:00:00
0201008003                Jack                      1970-01-01 00:00:00
0201008004                Jim                       1970-01-01 00:00:00
```
