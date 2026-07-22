
The behavior of pseudocolumns is similar to that of ordinary columns, but their data is not stored in the table. Users can SELECT pseudocolumns but cannot perform INSERT, UPDATE, or DELETE operations on them.

YashanDB has the following five pseudocolumns:

- ROWSCN
- ROWID
- ROWNUM
- SEQUENCE
- USER

## ROWSCN Pseudocolumn

The ROWSCN pseudocolumn indicates the last SCN (System Change Number) at which a row was modified. The ROWSCN in YashanDB is at the page level. Typically, the returned ROWSCN represents the BLOCK SCN of the last modification and commit of the page containing the row. However, using special query conditions may return the SCN of the last modification and commit of that row, such as when using ROWID as a query condition.

If a transaction modifies data in a row of the table and commits the changes, subsequent queries for the ROWSCN of that row will return a value that is greater than or equal to the ROWSCN before the modification.

- This pseudocolumn is applicable only to HEAP tables.

- ROWSCN cannot be used in the WHEN clauses, insert_values_clause, or subquery for [multi-table conditional inserts](../SQL Statements/INSERT.md#conditionalinsertclause).

***Example*** for Heap tables

```plsql
SELECT ROWSCN FROM area;
```

## ROWID Pseudocolumn

The ROWID pseudocolumn indicates the address of the row. The type of the ROWID pseudocolumn is ROWID. For usage restrictions and more information, please refer to [ROWID UROWID](../Data Types/ROWID and UROWID Types).

- This pseudocolumn is applicable only to HEAP tables.

- ROWID cannot be used in the WHEN clauses, insert_values_clause, or subquery for [multi-table conditional inserts](../SQL Statements/INSERT.md#conditionalinsertclause).

- In PL, it is allowed to use ROWID as a variable name and type name. If ROWID is declared as a variable in a PL block, the SELECT INTO clause in the same block will recognize ROWID as a variable, while the projection columns and WHERE clause will still identify ROWID as a pseudocolumn. Forms like `ROWID.VAR` will be recognized as variables.

***Example*** for Heap tables

```plsql
-- ROWID as a pseudocolumn
SELECT ROWID FROM area;

-- ROWID as a variable name and type name in PL block, recognized as variable in SELECT INTO clause, while being recognized as pseudocolumn in projection columns and WHERE clause
DECLARE
  rowid CHAR(2);
  area_rowid rowid;
BEGIN
  SELECT rowid, area_no INTO area_rowid, rowid from area WHERE area_name = 'SouthChina';
  DBMS_OUTPUT.PUT_LINE('area_no is ' || rowid);
  DBMS_OUTPUT.PUT_LINE('rowid is ' || area_rowid);
END;
/
-- Output
area_no is 03
rowid is 2821:0:0:4996:2

PL/SQL Succeed.

-- Forms like ROWID.VAR are recognized as variables
DECLARE
  TYPE rec IS RECORD(area_no CHAR(2), DHQ VARCHAR2(20));
  rowid rec := rec('03', 'Shenzhen');
  area_no CHAR(2);
  DHQ VARCHAR2(20);
BEGIN
  SELECT rowid.area_no, rowid.DHQ INTO area_no, DHQ FROM DUAL;
  DBMS_OUTPUT.PUT_LINE('area_no is ' || area_no);
  DBMS_OUTPUT.PUT_LINE('DHQ is ' || DHQ);
END;
/
-- Output
area_no is 03
DHQ is Shenzhen

PL/SQL Succeed.
```

## ROWNUM Pseudocolumn

The ROWNUM pseudocolumn is a system-assigned sequential number for rows returned by a query, where the first row is assigned 1, the second row is assigned 2, and so on. This pseudocolumn can be used to limit the total number of rows returned by a query.

- ROWNUM cannot be used in ISC Distributed Cluster Deployment.
- In PL, it is allowed to use ROWNUM as a variable name. If ROWNUM is declared as a variable in a PL block, the SQL operations in the same block will recognize ROWNUM as a variable.

The usage restrictions of ROWNUM in LSC tables are as follows:

-  ROWNUM can only be compared with constants.
- Comparisons using less than, less than or equal to, greater than, or greater than or equal to operators are not allowed in conditional statements.
- When using equality comparisons, the only valid object for ROWNUM is the constant 1.
- ROWNUM cannot be used on both sides of an OR condition.
- ROWNUM can only be used once in filtering conditions and must be used alone, not allowed as part of calculations or as function parameters.
- ROWNUM cannot appear in non-comparative predicates and inequalities, such as IN.
- ROWNUM cannot be used in the join conditions of outer joins.

Common scenarios for the ROWNUM pseudocolumn:

**Limiting the number of rows in the WHERE condition**

- WHERE ROWNUM < constant returns constant - 1 rows of records.
- WHERE ROWNUM = constant counts from 1, thus ROWNUM = 1 can return 1 record; when ROWNUM = any value other than 1, the condition is always FALSE, returning no results.
- WHERE ROWNUM > constant, if the constant is less than 1, the condition is always TRUE; if the constant is greater than or equal to 1, the condition is always FALSE (resulting in an empty query).

***Example*** for Heap tables

```plsql
SELECT area_no,area_name,DHQ FROM area WHERE ROWNUM < 2;

AREA_NO AREA_NAME                                                     DHQ                   
------- ------------------------------------------------------------- --------------------- 
01      EastChina                                                        Shanghai             
```

**Using ROWNUM with ORDER BY**

First, rows matching the ROWNUM condition are retrieved from the table, followed by sorting. The result may depend on the execution plan, and different execution plans may yield different rows.
  
***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
-- Create table STUDENTS
CREATE TABLE STUDENTS(num INT,score INT);
INSERT INTO STUDENTS VALUES(1,99);
INSERT INTO STUDENTS VALUES(1,76);
INSERT INTO STUDENTS VALUES(1,84);
INSERT INTO STUDENTS VALUES(1,63);
INSERT INTO STUDENTS VALUES(1,88);
COMMIT;

-- Query the top 3 student scores, fetching 3 rows then sorting, result may not be reliable
SELECT score FROM STUDENTS WHERE ROWNUM < 4 ORDER BY score DESC;

-- Query the top 3 student scores, using order by + limit
SELECT score FROM STUDENTS ORDER BY score DESC LIMIT 3;

-- Query the top 3 student scores, using subquery + ROWNUM
SELECT score FROM (SELECT score FROM STUDENTS ORDER BY score DESC) WHERE ROWNUM < 4;
```

**Using ROWNUM with JOIN**

In JOIN statements, ROWNUM evaluates based on the same rules as used in WHERE.

**Assigning a unique value to each row in the table using ROWNUM**

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
UPDATE STUDENTS SET num = ROWNUM;
```

## SEQUENCE Pseudocolumn

The SEQUENCE pseudocolumn generates a unique sequence number when creating a sequence object. It can be divided into the following two types:

-  CURRVAL: Returns the current sequence number, which is the last retrieved NEXTVAL value for the current session.
- NEXTVAL: Increments the sequence number and returns the NEXTVAL of the sequence.

SEQUENCE pseudocolumn cannot be used in ISC Distributed Cluster Deployment.

To obtain a sequence number:
```sql
sequence.CURRVAL
sequence.NEXTVAL
```

To get the sequence number of a specific schema:
```sql
schema.sequence.CURRVAL
schema.sequence.NEXTVAL
```

To fetch a remote sequence number via dblink:
```sql
[schema.]sequence.CURRVAL@DBLINK_NAME
[schema.]sequence.NEXTVAL@DBLINK_NAME
```

The CURRVAL and NEXTVAL pseudocolumns can be used in the following scenarios:

-  In SELECT statements where the queried columns do not include subqueries
- In SELECT clauses of INSERT statements
- In VALUES clauses of INSERT statements
- In SET clauses of UPDATE statements

Prohibited scenarios include:

-  In subqueries of DELETE, SELECT, or UPDATE statements
- In SELECT statements containing DISTINCT operators
- In SELECT statements with GROUP BY or ORDER BY clauses
- In cases where multiple SELECT statements are combined with UNION
- In WHERE clauses of SELECT statements

For any specific sequence number, the CURRVAL pseudocolumn must first be initialized with the NEXTVAL pseudocolumn within the same session.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

To obtain sequence numbers using the following syntax:
```sql
CREATE SEQUENCE seq_yashan_pseudo;
 
SELECT seq_yashan_pseudo.NEXTVAL FROM dual;
  SEQ_YASHAN1.NEXTVAL
---------------------
                    1

SELECT seq_yashan_pseudo.CURRVAL FROM dual;
  SEQ_YASHAN1.CURRVAL
---------------------
                    1
```

## USER Pseudocolumn

The USER pseudocolumn returns the username of the currently logged-in user. The return value type is VARCHAR(64).

- USER pseudocolumn cannot be used in ISC Distributed Cluster Deployment.
- In PL, it is allowed to use USER as a variable name. If USER is declared as a variable in a PL block, SQL operations in the same block will identify USER as a variable.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
SELECT USER FROM DUAL;
```
