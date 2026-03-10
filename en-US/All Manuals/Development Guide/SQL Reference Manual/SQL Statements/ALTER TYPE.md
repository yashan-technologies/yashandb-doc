Generic Description
----

The ALTER TYPE statement is used to change the properties of an existing UDT (User-Defined Type).

For the meaning and limitations of UDT, please refer to the [UDT](../../PL Reference Manual/PL Objects/User-Defined Types) section.

Statement Definition
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

The name of the schema that contains the UDT. If omitted, it defaults to the current logged-in user's schema.

### 2. type\_name

The name of the UDT to be changed.

### 3. EDITIONABLE | NONEDITIONABLE

Used for syntax compatibility and has no actual meaning.

<span id="typecompileclause" name="typecompileclause" class="yaslink"></span>

### 4. type\_compile\_clause

Specifies the recompilation options. For details, refer to the [compile_clause](../General SQL Syntax/compile_clause) description.

***Example*** for Standalone Deployment and YAC Deployment

```sql
-- Explicitly recompiles the udt_object type; if the corresponding type body exists, it will also be recompiled.
ALTER TYPE udt_object COMPILE;

-- Explicitly recompiles the udt_object type. (The type body will not be recompiled.)
ALTER TYPE udt_object COMPILE SPECIFICATION;

-- Explicitly recompiles the body of the udt_object type. (The type will not be recompiled.)
ALTER TYPE udt_object COMPILE BODY;

-- Explicitly recompiles the udt_varray type.
ALTER TYPE udt_varray COMPILE;
```
