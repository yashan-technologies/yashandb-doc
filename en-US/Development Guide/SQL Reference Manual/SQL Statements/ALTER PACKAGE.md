General Description
----

The ALTER PACKAGE statement is used to explicitly recompile the package HEAD, package BODY, or an entire package.

For packages in the SYS schema, the ALTER PACKAGE statement needs to be executed by the SYS user.

For packages in other schemas, the ALTER PACKAGE statement needs to be executed by the user who owns the package or by a user who has the ALTER ANY PROCEDURE privilege.

For the meaning and restrictions of package, please refer to [Package](../../PL Reference Manual/PL Objects/Packages).

Statement Definition
----

**alter_package::=**

```ebnf
= ALTER PACKAGE [schema '.'] package_name (package_compile_clause|(EDITIONABLE|NONEDITIONABLE)).
```

### schema

The name of the schema that contains the package. If omitted, it defaults to the schema of the currently logged-in user.

### package\_name

The name of the package that needs to be explicitly recompiled.

### package\_compile\_clause

Specifies the recompilation options; see the [compile_clause](../General SQL Syntax/compile_clause) description for details.

### EDITIONABLE | NONEDITIONABLE

Used for syntax compatibility, without actual significance.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
ALTER PACKAGE calc_fee COMPILE;

ALTER PACKAGE calc_fee COMPILE PACKAGE;

ALTER PACKAGE calc_fee COMPILE SPECIFICATION;

ALTER PACKAGE calc_fee COMPILE BODY;
```
