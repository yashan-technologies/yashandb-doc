通用描述
----

ALTER TYPE语句用于更改一个已存在的自定义类型（UDT）的相关属性。

自定义类型含义及限制请参考[自定义类型](../../PL参考手册/PL对象/自定义类型)章节。

语句定义
----

**alter_type::=**

```ebnf+diagram
syntax::=  ALTER TYPE  [ schema "." ]  type_name 
( EDITIONABLE | NONEDITIONABLE ) 
| type_compile_clause
```

**[type_compile_clause](#typecompileclause)::=**

```ebnf+diagram
syntax::=  COMPILE [DEBUG] [(SPECIFICATION | BODY)] [(compiler_parameters_clause) {(compiler_parameters_clause)}] [REUSE SETTINGS]
```

### 1. schema

包含UDT的模式名称，省略则默认为当前登录用户的模式。

### 2. type\_name

要更改的UDT的名称。

### 3. EDITIONABLE | NONEDITIONABLE

用于语法兼容，无实际含义。

<span id="typecompileclause" name="typecompileclause" class="yaslink"></span>

### 4. type\_compile\_clause

指定重编译选项，详见[compile_clause](../通用SQL语法/compile_clause)描述。

示例（单机、共享集群部署）

```sql
-- 显式重编译udt_object类型，如果存在对应类型主体，会同时重编译。
ALTER TYPE udt_object COMPILE;

-- 显式重编译udt_object类型。（不会重编译类型主体）
ALTER TYPE udt_object COMPILE SPECIFICATION;

-- 显式重编译udt_object类型的类型主体。（不会重编译类型）
ALTER TYPE udt_object COMPILE BODY;

-- 显式重编译udt_varray类型。
ALTER TYPE udt_varray COMPILE;
```
