## View

Users can define a frequently used query as a persistent object, which is called a view. The tables that provide the raw data for the view are called base tables.

Using a view to query instead of the original tables can simplify SQL statement writing.

Assuming the COMPANY EMPLOYEE table contains all employee personal information, and the DEPARTMENT table records all department information. If a user needs to query all employee personal information and their corresponding department information (department name and manager name), they should be clearly aware of which tables store employee personal information and department information, as well as the relationship between the tables, before executing the following query on the relevant tables:

```sql
SELECT E.ID, E.NAME, D.NAME, DM.NAME 
FROM EMPLOYEE E, DEPARTMENT D, EMPLOYEE DM 
WHERE E.DEPARTMENT_ID = D.ID AND D.MNGR_ID = DM.ID;
```

Unlike other users, database administrators are usually more familiar with the information and relationships of various tables. If the database administrator creates a view for the above query in advance:

```sql
CREATE OR REPLACE VIEW EMPLOYEE_INFO(EMPLOYEE_ID, EMPLOYEE_NAME, DEPARTMENT, DEPARTMENT_MANAGER) 
AS SELECT E.ID, E.NAME, D.NAME, DM.NAME 
FROM EMPLOYEE E, DEPARTMENT D, EMPLOYEE DM 
WHERE E.DEPARTMENT_ID = D.ID AND D.MNGR_ID = DM.ID;
```

When users need to query all employee information, they only need to execute the following query on the view, making operations easier and more efficient.

```sql
SELECT * FROM EMPLOYEE_INFO;
```

In the above example, the EMPLOYEE_INFO view does not store any data; its data is entirely derived from the EMPLOYEE table and the DEPARTMENT table.

### Functions of View

- Simplifying Queries

  Using views instead of frequently used queries, especially complex queries, can reduce the complexity of SQL writing for users. Writing efficient SQL (SQL statement optimization) requires a higher level of professionalism from database users. For complex queries, higher-capability users (such as database administrators) can create them as views for other users. Thus, using views can effectively reduce the cost of optimizing SQL for users and lower the difficulty of use.

- Decoupling

  Under the premise that the column names and data types of the view remain unchanged, modifying other elements of the base table definition does not affect the normal use of the view. When business systems are upgraded, the definition of the base table (e.g., adding columns) can be adjusted relatively freely without changing the view, or the view can be updated easily using CREATE OR REPLACE VIEW to reflect changes in the query it replaces. Therefore, using views can decouple database design from application development to some extent.

- Privilege Isolation

  As an independent object, the privilege of users on a view is separate from the privilege on the base table. The user who creates the view must have DML privilege on the base table, but the user who uses the view does not need DML privilege on the base table, only the DML privilege on the view itself. Therefore, by reasonably designing view columns and privileges, it can effectively prevent the complete exposure of all information in the base table to users who only need partial information (view columns).

### View Dependency

Views are used to replace queries; therefore, views depend on other objects involved in the query, including base tables, other views, user-defined data types, UDFs, etc.

When a view is created for a certain query statement, the query statement must be compiled to ensure that the syntax is correct. During the compilation process, the dependencies of the view are detected. By default, if dependent objects do not exist, the view creation will fail. If the FORCE keyword is included when creating the view, YashanDB will create the view directly without requiring the dependent objects to exist.

Once the view is successfully created, if the dependent objects of the view are modified (e.g., adding/deleting columns from the base table referenced by the view or dropping the base table), it will cause the view's status to become invalid. The view will only trigger recompilation when a DML statement is executed on it again. If the recompilation detects that the corresponding query statement is still valid, the view will return to normal use; otherwise, it will return an error.

### Accessing Views

When a user creates a view, the database stores the original text of the view definition query, columns, and other information in the system table.

When a SQL statement involves querying a view, the database first rewrites the view in the original SQL statement as a subquery (the subquery is the query defined in the view), then parses the rewritten SQL and generates an execution plan. For example:

```sql
CREATE TABLE employees(id int, name varchar(32), level int);
create view exports as select * from employees where level > 3;
-- Executing the following select statement
select * from exports;
-- Will actually be rewritten as
select exports.* from (select id, name from employees where level > 3) alias_exports;
```

If the view depends on other views, they will also be rewritten into their defining query during the rewriting process, so ultimately, the view's query will be rewritten to query its directly and indirectly dependent base tables.

The rewritten SQL is semantically equivalent to the original SQL, but there are differences between the original SQL and the SQL obtained by the user directly executing the rewritten SQL:

- If the user executes the query view statement for the first time and the query is successful, if the view is deleted afterward (while user privileges, related base tables, etc., remain unchanged), executing the same query view statement again will result in an error and will not automatically query the base table.

- If the current user only has privileges to query the view and does not have privileges to query the view's base table, querying the view will succeed, but directly executing the rewritten SQL will fail.

### Materialized View

Typically, a view is only used to replace the original query statement and does not store any data. When users query a view, the database still needs to access the base table to obtain raw data, which can result in long query times for complex queries (like multiple table joins).

The difference between a materialized view and a regular view is that a materialized view stores the results of the query in a table (referred to as the main table of the materialized view), transforming complex queries into single-table queries, thus improving query performance.

Since materialized views store data, when the data in the base table changes, it requires reverse updating of the data in the materialized view; this process is called refreshing the materialized view.

Users can choose the refresh frequency of materialized views based on their sensitivity to data changes.

***Example*** Scenario 1: A bank's account asset rating system requires evaluating users' credit ratings and asset distributions and storing the result in a materialized view. This result is less sensitive to data changes and does not need to be updated in real-time after each transaction, so it can be set to refresh on a daily or weekly basis. Therefore, users can set the materialized view to refresh at scheduled intervals.

***Example*** Scenario 2: A bank's account regulatory system requires real-time monitoring of the status of special accounts and storing the result in a materialized view. Since the bank must respond promptly to account loss reporting and closures, when specific actions occur (e.g., loss reporting and closures), the data in the materialized view should be refreshed immediately; hence, this materialized view should be set to refresh immediately once committing DML on the base table.

Users can also decide on the refresh method of materialized views based on the scale of data changes in the base table.

***Example*** Scenario 3: In a bank's account asset rating system, as the number of active accounts is huge each day, and the trading volume for each account may have multiple transactions, analyzing each transaction to determine the corresponding changes in an account's asset status takes much longer than re-rating the account based on its current status; therefore, it is suitable for the materialized view to undergo a full refresh.

***Example*** Scenario 4: In a bank's account regulatory system, since the proportion of accounts that are reported lost or closed is very small, performing a full refresh of all account information may take a long time; hence, this materialized view is more suitable for incrementally refreshing the corresponding records in the materialized view based on the change records of the base table.

The significance of materialized views is as follows:

- Compared to regular views, materialized views store data, which can replace complex queries with a query to a single main table, thus significantly improving performance for queries with high computational loads (e.g., those containing window functions, aggregate functions, etc.).

- Even if users utilize materialized views in their SQL statement, the database will still consider whether it can use the materialized views to replace parts of the original SQL during the execution plan generation, improving performance. Therefore, operations and maintenance personnel creating materialized views for some time-consuming SQL statements mean that business developers do not need to rewrite the SQL statements used in their business operations.

## Sequence

A Sequence is a schema object maintained by the database that automatically generates an integer sequence, typically used to generate primary keys for tables.

### Functions of Sequence

For example, when a new employee joins, the HR department needs to register employee information and assign employee numbers, which requires executing the following SQL statement in the database:

```sql
insert into employees(id, ...) values((select max(id) from employee) + 1, ...);
-- This statement is equivalent to the following PL Block
declare
  nextId int;
begin
  select max(id) into nextId from employees;
  insert into employees(id, name, ...) values(nextId + 1, 'Zhang San', ...);
end;
/  
```

The above SQL statement is only applicable when there is one HR staff member responsible for registering new employees.

If a large number of new employees join at the same time and multiple HR staff are registering concurrently, the same employee number may be assigned to multiple new employees. Using sequence can avoid this issue and simplify the operation:

```sql
create sequence seq_empl_id;
insert into employees(id, name, ...) values(seq_empl_id.nextval, 'Zhang San', ...);
-- Alternatively, during table creation, the default value for the id column can be specified as the sequence
create table employees(id int default seq_empl_id.nextval, name varchar(32), ...);
insert into employess(name, ...) values('Zhang San', ...);
```

Obtaining the next value from the sequence via seq_emp_id.nextval ensures that even if multiple sessions request the value simultaneously, each session will still receive a unique value, as it is automatically maintained by the database system.

Sequence has the following characteristics:

- The value returned by a sequence is an integer.

- Users can set the maximum, minimum, and starting values for the sequence, as well as whether the sequence will cycle when it reaches the maximum or minimum values.

- When cycling is not allowed, a sequence generates unique values.

- The values returned by the sequence can be either incrementing or decrementing, depending on the increment specified when creating the sequence (INCREMENT BY). In the case where cycling is not allowed, the sequence's output values remain monotonically increasing or decreasing.

### Buffer for Sequence Values

In the absence of cycling, the main role of sequences is to provide unique values for systems; the starting point for the next value will be written to the system table for persistence. If values from the sequence are frequently retrieved, it may lead to high update frequency of the system table, impacting performance. Therefore, YashanDB provides a buffer mechanism for sequence values.

```sql
CREATE SEQUENCE seq1 CACHE 20;
CREATE SEQUENCE seq2 CACHE 10000;
CREATE SEQUENCE seq3 NOCACHE;
```

The above three statements create sequences with buffer sizes of 20, 10000, and no buffer, respectively.

The buffer for a sequence will maintain a set of continuous values that have not yet been retrieved. When all values in the buffer are used up, the database will update the system table, advancing the starting point for the next value by the buffer size and refreshing the buffer with the next batch of values.

- The larger the buffer, the less frequently the system table is updated, and the faster the sequence values can be retrieved.

- If the database process is restarted, any unused values in the buffer will be lost; a smaller buffer means that the frequency of system table updates will be higher, but the number of sequence values lost due to unexpected process restarts will be less.

- In YAC Deployment, it is possible to specify that each instance can independently manage the sequence. As long as the buffer is not exhausted, terminating the process and restarting will yield sequence values that differ from previously retrieved values but will not repeat (with the exception of cycling scenarios).

- In YAC Deployment, all instances maintain the same system table, but each instance has its independent sequence buffer. Thus, it can guarantee that the sequence values retrieved by each instance are unique when cycling is not allowed. However, it does not guarantee the monotonically increasing or decreasing order of sequence values retrieved by different instances in succession. If ordering is required, the Order option should be specified when creating the sequence, which will prevent the generation of buffers on each instance.

## Synonyms

A synonym is an alias that a user assigns to a schema object. Users can create synonyms for tables, views, stored procedures, UDFs, packages, sequences, and other synonyms.

Synonyms have the following characteristics:

- The database persists the definition of synonyms, meaning that the synonym's name as well as the name of the object it represents will be written to the system table, retaining validity even after database restarts.

- When creating a synonym, the database does not verify that the object the synonym represents actually exists; it only links to that object when the synonym is used.

### Private Synonyms

A private synonym can be created for table1 using the following statement:

```sql
create synonym user1.syn1 for user2.table1;
```

The synonym syn1 created through this method belongs to the schema user1 (the schema name can be omitted, in which case the current schema will be automatically used as the synonym's schema). When used in other schemas, syn1 must be explicitly specified as user1.syn1.

### Public Synonyms

A public synonym can be created for table1 using the following statement:

```sql
create public synonym syn1 for user2.table1;
```

The synonym syn1 created through this method belongs to the PUBLIC schema, so users do not need to specify the schema name when using it.

Public synonyms are widely used in YashanDB system views.

For example, in the SYS schema, the USER_TABLES view displays information about all tables in the current schema.

If the user user1 wants to query all tables belonging to their schema, they need to be aware that the USER_TABLES view belongs to the SYS schema and execute the following statement:

```sql
SELECT * FROM SYS.USER_TABLES;
```

Since YashanDB creates a public synonym for the SYS.USER_TABLES view during the database initialization phase, user1 can execute the following query to check their schema's tables:

```sql
SELECT * FROM USER_TABLES;
```

## PL Objects

PL objects include user-defined data types, UDFs, stored procedures, and packages. For further details, please refer to the [PL documentation](../../Data Access/PL).