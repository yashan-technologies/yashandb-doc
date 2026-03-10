General Description
----

CREATE CONTEXT is used to create a context object. Any user with the create any context privilege can execute this statement to create a context object, but the context object is always owned by the sys user.

Statement Definition
----

**create context::=**

```ebnf+diagram
syntax::= CREATE [OR REPLACE] CONTEXT namespace USING [ schema. ] package
```

### 1. namespace

This statement is used to specify the name of the context being created. It is mandatory and must comply with YashanDB's [object naming convention](../../All Manuals/Development Guide/SQL Reference Manual/Basic SQL Elements/Identifiers).

### 2. schema

This statement is used to specify the user name associated with the package for creating the context.

### 3. package

This statement is used to specify the name of the package associated with the context being created.

The statement must meet the following rules:

- Creating a context does not validate if the package user and package are valid.
- Creating a context does not support associating with the built-in advanced package DBMS_SESSION.

***Example*** for Standalone Deployment and YAC Deployment

```sql
CREATE CONTEXT ctx1 USING REGRESS.CTX_PKG1;
```
