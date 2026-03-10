General Description
----

The ALTER PACKAGE statement is used to explicitly recompile the package HEAD, package BODY, or an entire package.

For packages in the SYS schema, the ALTER PACKAGE statement needs to be executed by the SYS user.

For packages in other schemas, the ALTER PACKAGE statement needs to be executed by the user who owns the package or by a user who has the ALTER ANY PROCEDURE privilege.

For the meaning and restrictions of package, please refer to [Package](../../PL参考手册/PL对象/自定义高级包).

Statement Definition
----

**alter_package::=**

```ebnf+diagram
syntax::= ALTER PACKAGE [schema "."] package_name (package_compile_clause|(EDITIONABLE|NONEDITIONABLE))
```

### 1. schema

The name of the schema that contains the package. If omitted, it defaults to the schema of the currently logged-in user.

### 2. package\_name

The name of the package that needs to be explicitly recompiled.

### 3. package\_compile\_clause

Specifies the recompilation options; see the [compile_clause](../General SQL Syntax/compile_clause) description for details.

### 4. EDITIONABLE | NONEDITIONABLE

Used for syntax compatibility, without actual significance.

***Example*** for Standalone Deployment and YAC Deployment

```sql
ALTER PACKAGE calc_fee COMPILE;

ALTER PACKAGE calc_fee COMPILE PACKAGE;

ALTER PACKAGE calc_fee COMPILE SPECIFICATION;

ALTER PACKAGE calc_fee COMPILE BODY;
```
