A trigger is a type of PL object in a database. Creating a trigger involves creating an executable procedure body, but unlike stored procedures and functions, the trigger's procedure body cannot be explicitly called by the user; instead, it is executed by the system in response to a specific event.

- In Standalone Primary-Standby or Primary-Standby Cluster Deployment, triggers can only be created, managed, and used on the primary database/primary cluster.

- Triggers are not suitable for ISC Distributed Cluster Deployment.

A trigger includes the following elements:

- Trigger action: The content to be executed, which is a procedure body.
- Trigger event: An event that can be determined by the system to trigger the execution of the procedure body, namely DML operations such as INSERT/UPDATE/DELETE on a table. A single trigger event can be defined, or combinations of multiple trigger events can be defined (using OR logic).
- Trigger timing: The time at which the procedure body is executed, including BEFORE (executed before the trigger event occurs) and AFTER (executed after the trigger event occurs).
- Trigger object: The object on which the trigger event is based, namely a table or view.
- Trigger type: Includes statement-level triggers (which execute the procedure body once when the trigger event occurs) and row-level triggers (which execute the procedure body once for each affected row of data when the trigger event occurs).
- Trigger condition: For row-level triggers, a condition expression can be specified using the WHEN statement, and the procedure body will only be executed when the trigger event occurs and the condition expression evaluates to true.

Defining a trigger on a table allows for real-time error interception, operation logging, or business logic processing when DML operations are executed on that table. Compared to stored procedures, triggers provide better real-time capability and access to the data at that moment, but they can negatively affect DML operations, such as execution efficiency or failure caused by issues with the trigger itself.

In addition to triggers, another functionality that is triggered during DML operations on a table is constraints. Constraints are used to ensure data integrity by executing field-level data checks. Compared to constraints, the procedural statements used in triggers can achieve more complex data processing. When a DML operation is executed on a table that has both constraints and triggers defined, the system processes in the following order:

1. Execute BEFORE statement-level trigger.

2. For each affected row of the DML operation:

   2.1 Execute BEFORE row-level trigger.

   2.2 Execute the DML operation and perform checks for non-FOREIGN KEY constraint items.

   2.3 Execute AFTER row-level trigger.

   2.4 Execute FOREIGN KEY checks.

3. Execute AFTER statement-level trigger.

Triggers are only applicable to HEAP tables.

## Creating a Trigger

This includes the definition and compilation of the trigger. If the trigger compilation fails, it will still be created but will cause an error during execution. A trigger that fails to execute will prevent all triggers on the DML event from firing until it is disabled, deleted, or successfully rebuilt and compiled.

A trigger is created using the [CREATE TRIGGER](../../SQL Reference Manual/SQL Statements/CREATE TRIGGER) statement, with the following syntax:

**[create\_trigger](#createtrigger)::=**

```ebnf
= CREATE [ OR REPLACE ] [EDITIONABLE|NONEDITIONABLE] TRIGGER [schema'.'] trigger_name  (simple_dml_trigger | instead_of_dml_trigger).
```

**[simple\_dml\_trigger](#simpledmltrigger)::=**

```ebnf
= ( BEFORE | AFTER ) dml_event_clause [ referencing_clause ] [ FOR EACH ROW ]  [trigger_ordering_clause] 
[ ENABLE | DISABLE ] [ WHEN ( condition ) ] trigger_body_clause.
```

**[instead\_of\_dml\_trigger](#insteadofdmltrigger)::=**

```ebnf
= INSTEAD OF ( DELETE | INSERT | UPDATE ){OR ( DELETE | INSERT | UPDATE )} ON [ schema'.'] ( view ) [ referencing_clause ] [ FOR EACH ROW ]  [trigger_ordering_clause] [ ENABLE | DISABLE ] trigger_body_clause.
```

**[dml\_event\_clause](#dmleventclause)::=**

```ebnf
= ( DELETE | INSERT | (UPDATE [ OF column{','column}] )){OR ( DELETE | INSERT | (UPDATE [ OF column{','column}] ))} ON [ schema'.'] ( table ).
```

**[referencing\_clause](#referencingclause)::=**

```ebnf
= REFERENCING ( OLD [ AS ] old | NEW [ AS ] new ) {( OLD [ AS ] old | NEW [ AS ] new )}.
```

**[trigger\_ordering\_clause](#triggerorderclause)::=**

```ebnf
=  FOLLOWS  ( [ schema '.' ]  trigger )  { ','  ( [ schema '.' ]  trigger ) }.
```

**[trigger_body_clause](#triggerbodyclause)::=**

```ebnf
= [DECLARE variable_declare] BEGIN pl_statements END ';'.
```

<span id="createtrigger" name="createtrigger"></span>

### OR REPLACE

When the trigger to be created already exists, it will be recreated.

### EDITIONABLE | NONEDITIONABLE

Used for syntax compatibility without real meaning.

### schema

The name of the schema containing the trigger. This can be omitted, defaulting to the schema of the currently logged-in user.

### trigger\_name

The name of the trigger to be created. Triggers in the same schema cannot have the same name. This is required and must conform to YashanDB's [object naming conventions](../../SQL Reference Manual/Basic SQL Elements/Identifiers).

<span id="simpledmltrigger" name="simpledmltrigger"></span>

### simple\_dml\_trigger

This statement is used to define the elements of a DML trigger.

#### BEFORE|AFTER

Defines the trigger timing—whether the trigger fires before or after the trigger event occurs. For row-level triggers, the trigger fires before or after each affected row operation.

<span id="dmleventclause" name="dmleventclause"></span>

#### dml\_event\_clause

Defines the trigger events and trigger objects.

Triggers are created on tables, and the trigger events can be combinations of DML statements DELETE, INSERT, and UPDATE using OR.

For MERGE statements, triggers can be created on the INSERT, UPDATE, and DELETE statements after the MERGE has been deconstructed, to allow for triggering operations on the MERGE statement.

**Condition predicates**

Since trigger events can be a combination of multiple triggering statements, when a specific statement causes a trigger event to occur, the trigger can identify which statement it was through the following condition predicates.

|Condition Predicate |TRUE Scenario |
| --------------------- | ------------------------ |
| INSERTING                     | Triggered by an INSERT statement   |
| UPDATING                      | Triggered by an UPDATE statement    |
| UPDATING ('*column*')        | Triggered by an UPDATE statement on a specific column |
| DELETING                      | Triggered by a DELETE statement      |

##### DELETE

Fires the trigger when a DELETE statement removes a row from the table.

##### INSERT

Fires the trigger when an INSERT statement adds a row to the table.

##### UPDATE [ …… OF column [, column ] ]

Fires the trigger when an UPDATE statement changes a value in a specified column.

**OF column [, column ]**

The OF column [, column] clause is used to specify the column fields to be changed by the UPDATE statement. It allows the DELETE or INSERT statements and their OR combinations to be interspersed between UPDATE and OF column [, column] (for example: INSERT OR UPDATE OR DELETE OF column, UPDATE OR INSERT OR DELETE OF column, and UPDATE OR INSERT OF column OR DELETE). However, the OF column clause cannot be placed after UPDATE (for example: INSERT OF column OR DELETE OR UPDATE is invalid). The column field specification rules are as follows:

- The specified column(s) cannot be of LOB type.

- Duplicate column names are not specified.

- The OF column [, column ] clause can be omitted, in which case the trigger will fire on any column update.

- Multiple OF column [, column] can be used in the same dml_event_clause, but all OF column [, column] must appear after UPDATE. When there are multiple OF column [, column], the specified column fields are based on the last OF column [, column], but when checking for duplicate specification of the same column field, all OF column [, column] will be checked.

##### schema

The name of the schema where the trigger is created. This can be omitted, defaulting to the schema of the currently logged-in user.

Triggers cannot be created on objects in the SYS schema.

##### table

The name of the table where the trigger is created.

Triggers cannot be created on private temporary tables.

<span id="referencingclause" name="referencingclause"></span>

#### referencing\_clause

The system provides the old and new values of the current data for reference, defaulting to OLD/NEW for identification, and may also define new names through this statement. If there are table objects named OLD or NEW in the system, new names should be defined in this statement to avoid errors due to name confusion.

#### FOR EACH ROW

Defines the trigger as a row-level trigger, meaning the system will execute the trigger once for each row that is affected and meets the trigger condition. 

If this clause is omitted, the trigger defaults to a statement-level trigger, indicating that the system will execute the trigger once when the trigger event occurs.

<span id="triggerorderclause" name="triggerorderclause"></span>

#### trigger\_order\_clause

When multiple triggers are defined for a specific trigger event on an object, this statement can be used to specify their relative execution order.

The FOLLOWS command indicates that the trigger being created must fire after the specified trigger. The specified trigger must exist and be successfully compiled, but it does not need to be enabled.

#### ENABLE|DISABLE

Specifies whether the created trigger is enabled or disabled. If not specified, the default is enabled.

#### WHEN (condition)

Defines the trigger condition, applicable only to row-level triggers. The trigger will fire for that row when the condition computed on the row evaluates to true.

It should be noted that the trigger condition affects the operation of the trigger itself, but does not affect the operation of the triggering event—the DML statement will execute regardless of whether the condition result is true or false.

The syntax of condition is consistent with the general SQL syntax [condition](../../SQL Reference Manual/General SQL Syntax/condition), but cannot contain subqueries or UDFs. Moreover, references to column fields must differentiate old and new values according to OLD/NEW (or the names defined by the [referencing\_clause](#referencingclause)), with the format being OLD.column, NEW.column.

<span id="triggerbodyclause" name="triggerbodyclause"></span>

#### trigger_body_clause

Defines the trigger action, which is a procedure body.

##### DECLARE variable\_declare

Declares global variables for the PL procedure body, which may be omitted.

[PL Variable Description](../PL Language Fundamentals/Variables/00Variables).

##### PL\_statements

Defines the execution statements within the procedure body, which may be one or more statements from [PL statements](../PL Statements/00PL Statements) but cannot include DDL and DCL statement contents.

References to the column fields of the trigger object must differentiate old and new values according to OLD/NEW (or names defined by [referencing\_clause](#referencingclause)), with formats and definitions being as shown in the table below:

|        | :OLD.column  | :NEW.column  |
| :----- | :----------- | :----------- |
| INSERT | NULL         | Value of INSERT   |
| DELETE | Value before DELETE | NULL         |
| UPDATE | Value before UPDATE | Value after UPDATE |

There are additional rules for references to old and new values:

* It is not allowed to read or write ':NEW' and ':OLD' in statement-level triggers, including BEFORE and AFTER.
* In BEFORE row-level triggers, ':NEW' can be read or written, while ':OLD' can only be read.
* In AFTER row-level triggers, ':OLD' and ':NEW' can only be read, not written.
* User-defined types (udt) columns, including properties of object columns, cannot be referenced.

***Example*** for Standalone Deployment Heap tables and YAC/Distributed Cluster Deployment Heap tables

```plsql
-- Create a row-level BEFORE trigger t on the product table, printing different messages during INSERT, DELETE, and UPDATE.
-- Specify new correlation names for old and new.
CREATE OR REPLACE TRIGGER tri 
    BEFORE 
    INSERT OR UPDATE OF product_name, price OR DELETE
    ON product 
    REFERENCING OLD AS told NEW AS tnew
    FOR EACH ROW
    ENABLE
    WHEN (told.product_no<>11003 or tnew.product_no <> 11003)
BEGIN
    IF INSERTING THEN
        DBMS_OUTPUT.PUT_LINE('Inserting. id is ' || :tnew.product_no);
    ELSIF DELETING THEN
        DBMS_OUTPUT.PUT_LINE('Deleting. id is ' || :told.product_no);
    ELSIF UPDATING('price') THEN
        DBMS_OUTPUT.PUT_LINE('Updating price. id is ' || :tnew.product_no);
        DBMS_OUTPUT.PUT_LINE('Updating old price:' || :told.price);
        DBMS_OUTPUT.PUT_LINE('Updating new price:' || :tnew.price);
        :tnew.price := :tnew.price + 10;
        DBMS_OUTPUT.PUT_LINE('Updating final price:' || :tnew.price);
    ELSE
        DBMS_OUTPUT.PUT_LINE('Updating Others. id is ' || :tnew.product_no);
    END IF;
END;
/

-- Execute DML statement
-- 1. The primary key constraint checks after the BEFORE row-level trigger
INSERT INTO product VALUES ('11001','product001',8,10);
Inserting. id is 11001
YAS-02030 unique constraint violated
-- 2. When condition is false, the trigger does not fire, but the DML statement still runs
DELETE product WHERE product_no=11001;
Deleting. id is 11001
ROLLBACK;
-- 3. Update price
UPDATE product SET price=20 WHERE product_no=11002;
Updating price. id is 11002
Updating old price:16
Updating new price:20
Updating final price:30
```

<span id="insteadofdmltrigger" name="insteadofdmltrigger"></span>

### instead\_of\_dml\_trigger

This statement defines the elements of INSTEAD OF DML triggers.

Rules for using INSTEAD OF DML triggers are as follows:

* INSTEAD OF DML triggers can only be created on views that are not WITH READ ONLY views or materialized views.

* Once an INSTEAD OF DML trigger is written on the view, user DML operations on the view will not be executed; instead, the PL statement block within the trigger will be executed. This allows appropriate code to be written in the INSTEAD OF trigger to operate on the tables comprising the view.

* When creating INSTEAD OF DML triggers, there are no BEFORE or AFTER keywords; INSTEAD OF DML is always equivalent to a row-level trigger using the AFTER keyword. It will be triggered once for each affected row, and the NEW and OLD values can only be read and not modified.

* INSTEAD OF DML triggers do not support WHEN conditions.

* The base table of the view on which the INSTEAD OF DML trigger resides cannot be an LSC table.

* UPDATE subqueries will not trigger.

***Example*** for Standalone Deployment Heap tables

```plsql
SQL> set serveroutput on;
SQL> drop table if exists tb_39656_instead_tri_007;

Succeed.

SQL> drop view if exists vi_39656_instead_tri_007;

Succeed.

SQL> drop table if exists tb_39656_instead_tri_007_2;

Succeed.

SQL> create table tb_39656_instead_tri_007 (id int,c1 clob);

Succeed.

SQL> create table tb_39656_instead_tri_007_2 (id int,c1 clob);

Succeed.

SQL> create view vi_39656_instead_tri_007 as select * from tb_39656_instead_tri_007;

Succeed.

SQL> BEGIN
   2 for i in 1..2 loop
   3 insert into tb_39656_instead_tri_007 values(i,'abc'||i);
   4 end loop;
   5 end;
   6 /

PL/SQL Succeed.

SQL> commit;

Succeed.

SQL> 
SQL> create or replace trigger tri_39656_instead_tri_007 instead of insert on vi_39656_instead_tri_007
   2 for each ROW
   3 BEGIN
   4 dbms_output.put_line('========instead of DML trigger=========');
   5 insert into tb_39656_instead_tri_007_2 values(:new.id+1,:new.c1||:new.c1);
   6 end;
   7 /

Succeed.

SQL> 
SQL> insert into vi_39656_instead_tri_007 values(1001,'abc1001');
========instead of DML trigger=========

1 row affected.

SQL> 
SQL> select * from tb_39656_instead_tri_007 order by id;

          ID C1                                                               
------------ ---------------------------------------------------------------- 
           1 abc1                                                            
           2 abc2                                                            

2 rows fetched.

SQL> select * from tb_39656_instead_tri_007_2 order by id;

          ID C1                                                               
------------ ---------------------------------------------------------------- 
        1002 abc1001abc1001                                                  

1 row fetched.

SQL> select * from vi_39656_instead_tri_007 order by id;

          ID C1                                                               
------------ ---------------------------------------------------------------- 
           1 abc1                                                            
           2 abc2                                                            

2 rows fetched.

SQL> 
SQL> drop table tb_39656_instead_tri_007;

Succeed.

SQL> drop table tb_39656_instead_tri_007_2;

Succeed.

SQL> drop view vi_39656_instead_tri_007;

Succeed.
```

## Creating an Autonomous Transaction Trigger

In the syntax of creating triggers described above, since the DML statement of the triggering event and the procedure body of the trigger exist in the same transaction, DCL and DDL statements cannot be executed within the procedure body to avoid inadvertently committing or rolling back.

When there is a need to commit a transaction, such as when a trigger is used for logging, and the log table is a separate independent table that should be committed promptly after gathering the new and old values of the triggering event, an autonomous transaction trigger can be created to fire the trigger action as a separate transaction.

The statement to define an autonomous transaction trigger is: *pragma autonomous_transaction;*.

***Example*** for Standalone Deployment Heap tables and YAC/Distributed Cluster Deployment Heap tables

```plsql
-- Create a log table
CREATE TABLE product_log 
(product_no CHAR(5),
 update_date DATE,
 newprice NUMBER,
 oldprice NUMBER);
 
-- Autonomous transaction trigger. Create a row-level BEFORE trigger log_product on the product table, writing the old and new values into the product_log table when updating the product.price column.
CREATE OR REPLACE TRIGGER log_product 
    BEFORE 
    DELETE 
    ON product 
    FOR EACH ROW
DECLARE
pragma autonomous_transaction;
BEGIN
    DBMS_OUTPUT.PUT_LINE('old.price is ' || :old.price);
    DBMS_OUTPUT.PUT_LINE('new.price is ' || :new.price);
    INSERT INTO product_log VALUES (:old.product_no, SYSDATE, :new.price, :old.price);
    COMMIT;
END;
/

-- Delete data from the product table, triggering log_product and tri, the log table will commit independently
DELETE product WHERE product_no=11002;
Deleting. id is 11002
1 row affected.

-- Rollback DML operation, product data is restored, but the log table still records the operation
ROLLBACK;
SELECT price FROM product WHERE product_no=11002;
      PRICE 
----------- 
         30
SELECT PRODUCT_NO,UPDATE_DATE,NEWPRICE,OLDPRICE FROM product_log  WHERE product_no=11002;
PRODUCT_NO UPDATE_DATE                         NEWPRICE    OLDPRICE 
---------- -------------------------------- ----------- ----------- 
11002      2022-06-26                                            16
```

## Enabling/Disabling Triggers

The [ALTER TRIGGER](../../SQL Reference Manual/SQL Statements/ALTER TRIGGER) statement can be used to enable or disable an already created trigger.

## Deleting Triggers

Triggers can be deleted by:

- Using the [DROP TRIGGER](../../SQL Reference Manual/SQL Statements/DROP TRIGGER) statement to delete an existing trigger.

- Using the [DROP TABLE](../../SQL Reference Manual/SQL Statements/DROP TABLE) statement to delete an object on which a trigger is based, which will also delete that trigger. If the recycle bin is enabled (configuration parameter RECYCLEBIN_ENABLED = ON), the trigger will enter the recycle bin along with the corresponding table; if the recycle bin is not enabled, the trigger will be completely deleted along with the corresponding table.

## Flashback Triggers

When using the [FLASHBACK](../../SQL Reference Manual/SQL Statements/FLASHBACK) statement to flash back a table, the corresponding trigger objects will also be flashed back. After the flashback, the names of the triggers will not automatically revert (they will retain the names after entering the recycle bin).