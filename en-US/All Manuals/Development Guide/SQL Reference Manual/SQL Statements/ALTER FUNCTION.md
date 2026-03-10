General Description  
----

The ALTER FUNCTION statement is used to explicitly recompile a UDF.  

For UDFs in the SYS schema, the ALTER FUNCTION statement must be executed by the SYS user.  

For UDFs in other schemas, the ALTER FUNCTION statement must be executed by the user who owns the UDF or by a user with the ALTER ANY PROCEDURE privilege.  

For the meaning and limitations of UDF, please refer to the [UDF](../../PL Reference Manual/PL Objects/User-Defined Functions) section.  

Statement Definition  
----

**alter_function::=**

```ebnf+diagram
syntax::= ALTER FUNCTION [schema "."] function_name (function_compile_clause|(EDITIONABLE|NONEDITIONABLE))
```

### 1. schema

The name of the schema that contains the UDF; if omitted, it defaults to the current logged-in user's schema.  

### 2. function\_name

The name of the UDF to be explicitly recompiled.  

### 3. function\_compile\_clause

Specifies the recompilation options; see the [compile_clause](../General SQL Syntax/compile_clause) description for details.  

### 4. EDITIONABLE | NONEDITIONABLE

Used for syntax compatibility, with no practical meaning.  

***Example*** for Standalone Deployment and YAC Deployment  

```sql
ALTER FUNCTION sales.ya_func COMPILE;
```
