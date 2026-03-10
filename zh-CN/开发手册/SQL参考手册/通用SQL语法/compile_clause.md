**compile_clause::=**

```ebnf+diagram
syntax::= COMPILE [DEBUG] [PACKAGE|SPECIFICATION|BODY] [compiler_parameters_clause] [REUSE SETTINGS]
```

**compiler_parameters_clause::=**

```ebnf+diagram
syntax::= parameter_name "=" "'" parameter_value "'"
```

compile_clause用于执行某个对象的重编译。

如果重编译的对象有任何依赖的对象失效，系统将首先重编译这些依赖的对象。  

对一个对象重编译成功后，该对象将被置为有效状态。如果重编译失败，系统返回相应报错，该对象变为无效状态，且系统会同时失效依赖于该对象的其他对象。

## 重编译选项

#### DEBUG

用于语法兼容，无实际含义。

#### PACKAGE|SPECIFICATION|BODY

用于指定重编译的范围，可省略，则默认为PACKAGE。只能在重编译自定义高级包时指定此选项。

**PACKAGE**

重编译高级包的HEAD和BODY（如果存在）。  

**SPECIFICATION**

重编译高级包的HEAD。

**BODY**

重编译高级包的BODY。

#### compiler\_parameters\_clause

指定重编译的参数，可指定的parameter_name及parameter_value见下文描述。

#### REUSE SETTINGS

用于语法兼容，无实际含义。

重编译参数
----

详细参数见下表：

|  parameter_name| 描述| parameter_value|
| --- | --- | --- |
| PLSCOPE_SETTINGS | 用于语法兼容，无实际含义 | v:c {, v:c}<br>v为IDENTIFIERS&#124;STATEMENTS<br>c为ALL&#124;NONE&#124;PLSQL&#124;SQL&#124;PUBLIC(for IDENTIFIERS)或ALL&#124;NONE(for STATEMENTS) |
| PLSQL_CCFLAGS | 用于语法兼容，无实际含义 | v:c {, v:c}<br>v为YashanDB的某个保留关键字<br>c为true&#124;false |
| PLSQL_CODE_TYPE | 用于语法兼容，无实际含义 | INTERPRETED&#124;NATIVE |
| PLSQL_OPTIMIZE_LEVEL | 用于语法兼容，无实际含义 | 0~3之间的一个整数 |
| PLSQL_WARNINGS | 用于语法兼容，无实际含义 | v:c {, v:c}<br/>v为ENABLE&#124;DISABLE&#124;ERROR<br>c为ALL&#124;SEVERE&#124;INFORMATIONAL&#124;PERFORMANCE<br>&#124;integer&#124;(integer,interger...) |
| NLS_LENGTH_SEMANTICS | 用于语法兼容，无实际含义 | NLS_LENGTH_SEMANTICS=BYTE&#124;CHAR |
| PERMIT_92_WRAP_FORMAT | 用于语法兼容，无实际含义 | true&#124;false |
| PLSQL_DEBUG | 用于语法兼容，无实际含义 | true&#124;false |

示例（单机、共享集群部署）

```sql
ALTER PACKAGE sales.calc_fee COMPILE PLSCOPE_SETTINGS='IDENTIFIERS:PLSQL,IDENTIFIERS:SQL,STATEMENTS:NONE';
```
