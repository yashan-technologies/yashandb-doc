General Description
----

CREATE SQLMAP is used to create an SQL mapping. It allows the database to receive requests for statement A while actually executing statement B. This functionality serves as an auxiliary method for SQL tuning, wherein specific SQL is replaced with equivalent SQL via a mapping table. This is useful in scenarios where certain optimizers do not select the optimal execution plan, allowing the database to execute under the optimal plan.

Please note the following when executing this statement:

- The user must have DBA privilege to create an SQL mapping.
- The SQL statement type to be mapped must be a DML statement.
- The system parameter SQL_MAP (default value `FALSE`) must be set to true for the mapping to take effect. When set to false, this statement can execute successfully, but the mapping will not take effect.
- This statement does not verify the correctness of the original statement and the mapped statement. Inputting erroneous statements can lead to execution errors or unexpected results.

In ISC Distributed Cluster Deployment, when executing this statement, the system will check for any abnormal unrecovered DDL statements. If any exist, an error will be reported, and execution of this statement must wait until the corresponding DDL successfully recovers.

Statement Definition
----

**create\_sqlmap::=**

```ebnf
= CREATE SQLMAP map_name "(" user_name "," "'sql'" "," "'map_sql'"  ")" .
```

### map\_name

The name of the SQL mapping to be created. This name must be globally unique, cannot duplicate existing SQL mapping names, cannot include a schema name specification, and must comply with YashanDB's [object naming conventions](../Basic SQL Elements/Identifiers).

### user\_name

Specifies the username corresponding to the SQL mapping. It can take the following values:

- ALL: Indicates that the mapping is effective for all users.
- CURRENT_USER: Indicates that the mapping is effective for the current user.
- A username that exists in the database. For users created with double quotes, the actual case-sensitive name must be used; otherwise, it is case-insensitive.

### sql

The original statement. A mapping cannot be created again if the specified statement has already been mapped.

### map_sql

The mapped statement, which must be of the same SQL statement type as the original statement, for example, both must be SELECT statements.

***Example***

```sql
-- Create an SQL mapping to optimize the query for all branches, changing it to retrieve only active branches in the area and only obtaining useful fields
CREATE SQLMAP map_branch (sales,'SELECT * FROM branches','select branch_no,branch_name from branches where area_no in (select area_no from area)');

-- Effect when user sales executes SELECT * FROM branches
SELECT * FROM branches;
BRANCH_NO BRANCH_NAME                                                      
--------- ----------------------------
0101      Shanghai                                                          
0102      Nanjing                                                          
0103      Fuzhou                                                          
0104      Xiamen                                                          
0401      Beijing                                                          
0402      Tianjin                                                          
0403      Dalian                                                          
0404      Shenyang                                                          
0201      Chengdu                                                          
0502      Changsha   

-- Cannot create multiple mappings for the original statement
CREATE SQLMAP map_branch (all,'SELECT * FROM branches','select branch_no,branch_name from branches where area_no in (select area_no from area)');
[1:31]YAS-04398 duplicate sql
```
