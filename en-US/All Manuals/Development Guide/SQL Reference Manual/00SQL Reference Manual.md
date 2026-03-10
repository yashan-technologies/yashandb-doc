SQL (Structured Query Language) is the primary interface for developers to operate databases. This manual comprehensively introduces the SQL functionality implementation of YashanDB from aspects including data types, operators, functions, and SQL statements.

## Explanation of Examples Used in the Manual

* The SQL and PL statements in the examples are executed in the *yasql* tool, with the output results from the *yasql* tool presented as samples.

* The example uses DBMS_OUTPUT.PUT_LINE statements to print output to the *yasql* tool. To achieve the output effect, ensure that the command set serveroutput on has been run in the *yasql* tool to enable control of the output. For details, refer to the tool manual [yasql User Guide](../../Tools Guide/yasql/User Guide for yasql).

* The system's default display width for floating-point types and NUMBER type data output is 10. The display width can be adjusted using SET NUMWIDTH.

* For date display format: To closely simulate actual business, the date format of the sample database in this manual is set to 'YYYY-MM-DD HH24:MI:SS'. If the DATE_FORMAT configuration parameter is not set to this format, the observed date format will be inconsistent with this manual.

* Regarding the general tables used in the examples: This manual uses sales as the example user (see [CREATE USER](SQL Statements (yashan Mode)/CREATE USER) for user creation) and creates a set of sample tables under this user. The sample tables come from a simplified business model listed below and are adjusted according to different architecture/storage features. The general tables used in the examples derive from these sample tables. When executing examples, switching to the sales user will allow querying these tables. The product documentation [Appendix: Sample Tables](../Appx2 Sample Tables) shows the creation scripts of these tables.
    
    > **Note**: 
    >
    > To achieve specific purposes, examples may require creating other tables or modifying the structure and data of general tables. These will not be reflected here but can be found in the specific examples.

* Before executing examples using the mysql mode, the USE statement must first be executed to specify the default database as the one with the same name as the user (for example, `USE sales;`). This operation will not be reiterated in specific examples.

### Sample Business Model

![](./image/simple_model.png)

```sql
-- Area Information Table: area, area1
-- Organization Information Table: branches, branches1
-- Department Information Table: department
-- Employee Information Table: employees
-- Product Information Table: product
-- Order Information Table: orders_info
-- Sales Information Table: sales_info, sales_info_range, sales_info_list, sales_info_hash
-- Financial Information Table: finance_info
```

### Description of Sample Table Types

YashanDB supports multiple deployment forms (Standalone Deployment/distributed/YAC) and various table types (HEAP/TAC/LSC). It provides the DEFAULT_TABLE_TYPE parameter for configuring the default table type when creating table objects and also offers the ORGANIZATION syntax for creating a table object of a specified type (see [CREATE TABLE](SQL Statements (yashan Mode)/CREATE TABLE) for detailed syntax explanation).

The types of sample tables used in this manual are determined by the system deployment form and the value of the DEFAULT_TABLE_TYPE parameter. When examples or the context of examples do not specify that a statement is suitable for a specific deployment form or table type, it means that the example statement can run in any deployment form and any table type. Otherwise, the example statement should run in the specified deployment form environment and with the specified table type; inappropriate deployment forms or table types may lead to abnormal execution of the example statement or output results that are inconsistent with expectations.