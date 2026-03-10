PL objects are database objects organized based on the syntax rules of PL language, such as stored procedures, anonymous blocks, UDFs, etc.

The structure of a typical PL object is as follows:

*Header*

IS/AS

*Procedure Body*

The header contains information such as the name, parameters, and return type; the procedure body serves as the main carrier for implementing PL language and mainly includes the following content:

*   Variable definition and management
*   Operations
*   Control
*   Passing (variables, parameters)
*   Specific data processing

Creating PL Objects
----------

This includes the definition and compilation of PL objects.

The definition of a PL object describes its structure through writing PL language.

Compiling a PL object is the process of analyzing, translating, and generating executable code from the defined content using YashanDB's compiler.

For PL objects created using SQL statements like [CREATE PROCEDURE](../../SQL Reference Manual/SQL Statements (yashan Mode)/CREATE PROCEDURE) and [CREATE FUNCTION](../../SQL Reference Manual/SQL Statements (yashan Mode)/CREATE FUNCTION), they will be stored in the database upon creation, achieving persistence.

For PL objects that only have a procedure body without an object name and parameter definition, such as anonymous blocks, they will not be stored upon creation but executed directly.

Running PL Objects
----------

When an anonymous block is created or when a persistent PL object is called (either internally in SQL or PL or via SQL statements like CALL/EXEC), YashanDB's executor will locate the compiled code of the object and execute the operations defined by various PL statements, returning results or throwing exceptions.

Deleting PL Objects
----------

For PL objects that are no longer in use, they can be deleted from the database using SQL statements such as [DROP PROCEDURE](../../SQL Reference Manual/SQL Statements (yashan Mode)/DROP PROCEDURE) and [DROP FUNCTION](../../SQL Reference Manual/SQL Statements (yashan Mode)/DROP FUNCTION).

## Recompiling PL Objects

Using SQL statements like [ALTER FUNCTION](../../SQL参考手册/SQL语句（yashan模式）/ALTER FUNCTION), [ALTER PACKAGE](../../SQL参考手册/SQL语句（yashan模式）/ALTER PACKAGE), [ALTER PROCEDURE](../../SQL参考手册/SQL语句（yashan模式）/ALTER PROCEDURE), and [ALTER TRIGGER](../../SQL参考手册/SQL语句（yashan模式）/ALTER TRIGGER), UDFs, packages, stored procedures, and triggers can be recompiled.

If any dependent objects of the object being recompiled are invalid, the system will first recompile these dependent objects.

Once a PL object is successfully recompiled, it will be marked as valid. If recompilation fails, the system will return the appropriate error, the object will become invalid, and other objects that depend on it will also become invalid.

## PL Object Privilege Control

YashanDB uses a system privilege mechanism (please refer to [System Privilege](../../../Product Security/Data Access Control/Privilege and Role Management (yashan Mode)/System Privileges)) to control the security of the creation and use of PL objects, specifically including the following privileges:

- CREATE/DROP PROCEDURE: privilege to create/delete stored procedures, functions, advanced packages, and types.
- CREATE/ALTER/DROP ANY PROCEDURE: privilege to create/modify/delete stored procedures, functions, advanced packages, and types of all users (except for sys).
- EXECUTE ANY PROCEDURE: privilege to execute stored procedures, functions, and advanced packages of all users (except for sys).
- CREATE TRIGGER: privilege to create/modify/delete triggers.
- CREATE/ALTER/DROP TRIGGER: privilege to create/modify/delete triggers of all users (except for sys).
- ADMINISTER DATABASE TRIGGER: privilege to manage database-level triggers.
- CREATE/DROP TYPE: privilege to create/delete UDTs.
- CREATE/ALTER/DROP ANY TYPE: privilege to create/modify/delete UDTs of all users (except for sys).
- EXECUTE ANY TYPE: privilege to execute UDTs of all users (except for sys).
- UNDER ANY TYPE: create subtypes under non-final OBJECT types.

<span id="invoker" name="invoker" class="yaslink"></span>

### invoker\_rights\_clause

The aforementioned privileges define the privileges of a user over PL objects, where the user may be the owner of the PL object or the user invoking the PL execution. Due to the presence of SQL statements within the PL body, there will be corresponding checks for executing SQL privileges.

The invoker_rights_clause is used to define the AUTHID attribute of a PL object when it is defined, specifying the execution privilege mode in this scenario.

**invoker\_rights\_clause::=**

```ebnf+diagram
syntax::= AUTHID (CURRENT_USER | DEFINER)
```

In the statement defining a PL object, the invoker_rights_clause can only appear once.

It is used in independent stored procedures and functions to specify the AUTHID attribute of the object.

In advanced packages, it is used to specify the AUTHID attribute of all subprograms under that advanced package.

In types, it is only used to specify the AUTHID attribute of methods of Object UDT, and cannot be specified when defining Varray/Table UDTs; subtypes must specify the same AUTHID attribute as the parent type.

This statement cannot be used when defining anonymous blocks and triggers; instead, fixed AUTHID attributes are used:

- The AUTHID attribute of anonymous blocks is CURRENT_USER.
- The AUTHID attribute of triggers is DEFINER.

#### CURRENT\_USER

Indicates that the default mode during the execution of the procedure body is the user invoking the PL object, and privilege checks will be performed for SQL statements in the procedure body on that user.

#### DEFINER

Default behavior. Indicates that the default mode during execution in the procedure body is the user owning the PL object, and privilege checks will be performed for SQL statements in the procedure body on that user.

> **Note**: 
>
> You can view the execution privilege mode (AUTHID attribute) of PL objects through the dba/all/USER_PROCEDURES views.
>
> CURRENT_USER is currently a syntax compatibility mode, meaning that regardless of whether the AUTHID attribute is specified, the system executes privilege checks in DEFINER mode.