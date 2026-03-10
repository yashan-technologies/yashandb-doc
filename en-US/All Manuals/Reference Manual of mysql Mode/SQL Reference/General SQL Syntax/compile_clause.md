
**compile_clause::=**

```ebnf+diagram
syntax::= COMPILE [DEBUG] [PACKAGE|SPECIFICATION|BODY] [compiler_parameters_clause] [REUSE SETTINGS]
```

**compiler_parameters_clause::=**

```ebnf+diagram
syntax::= parameter_name "=" "'" parameter_value "'"
```

The compile_clause is used to recompile a specific object.

If any dependent objects of the object being recompiled become invalid, the system will first recompile these dependent objects.

After a successful recompilation of an object, that object will be set to a valid state. If the recompilation fails, the system returns the corresponding error, the object becomes invalid, and the system will also invalidate other objects that depend on that object.

## Recompilation Options

#### DEBUG

Used for syntax compatibility, has no actual meaning.

#### PACKAGE|SPECIFICATION|BODY

Used to specify the scope of recompilation, optional, defaults to PACKAGE. This option can only be specified when recompiling package.

**PACKAGE**

Recompiles the HEAD and BODY of the advanced package (if present).

**SPECIFICATION**

Recompiles the HEAD of the advanced package.

**BODY**

Recompiles the BODY of the advanced package.

#### compiler\_parameters\_clause

Specifies the parameters for recompilation. The parameter_name and parameter_value that can be specified are described below.

#### REUSE SETTINGS

Used for syntax compatibility, has no actual meaning.

Recompilation Parameters
----

Detailed parameters are shown in the table below:

|parameter_name |Description |parameter_value |
| --- | --- | --- |
| PLSCOPE_SETTINGS | Used for syntax compatibility, has no actual meaning | v:c {, v:c}<br>v is IDENTIFIERS&#124;STATEMENTS<br>c is ALL&#124;NONE&#124;PLSQL&#124;SQL&#124;PUBLIC(for IDENTIFIERS) or ALL&#124;NONE(for STATEMENTS) |
| PLSQL_CCFLAGS | Used for syntax compatibility, has no actual meaning | v:c {, v:c}<br>v is a reserved keyword of YashanDB<br>c is true&#124;false |
| PLSQL_CODE_TYPE | Used for syntax compatibility, has no actual meaning | INTERPRETED&#124;NATIVE |
| PLSQL_OPTIMIZE_LEVEL | Used for syntax compatibility, has no actual meaning | An integer between 0 and 3 |
| PLSQL_WARNINGS | Used for syntax compatibility, has no actual meaning | v:c {, v:c}<br/>v is ENABLE&#124;DISABLE&#124;ERROR<br>c is ALL&#124;SEVERE&#124;INFORMATIONAL&#124;PERFORMANCE<br>&#124;integer&#124;(integer,interger...) |
| NLS_LENGTH_SEMANTICS | Used for syntax compatibility, has no actual meaning | NLS_LENGTH_SEMANTICS=BYTE&#124;CHAR |
| PERMIT_92_WRAP_FORMAT | Used for syntax compatibility, has no actual meaning | true&#124;false |
| PLSQL_DEBUG | Used for syntax compatibility, has no actual meaning | true&#124;false |

***Example*** for Standalone Deployment and YAC Deployment

```sql
ALTER PACKAGE sales.calc_fee COMPILE PLSCOPE_SETTINGS='IDENTIFIERS:PLSQL,IDENTIFIERS:SQL,STATEMENTS:NONE';
```
