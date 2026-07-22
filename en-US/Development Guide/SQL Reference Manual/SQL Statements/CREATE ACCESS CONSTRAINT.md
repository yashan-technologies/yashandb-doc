General Description
----
CREATE ACCESS CONSTRAINT is used to create an [AC object](../Basic SQL Elements/Schema Objects) on a specified table. This statement can only create AC objects based on LSC tables, and the LSC table cannot be a composite partition table or an INTERVAL type partition table.

Once the AC is created, the system will simultaneously generate AC data in the background, which can be used to:

- Speed up queries on the source table by using AC for bounded calculations when AC conditions are met during the query execution.
- Allow users to query AC directly like a regular table, with the query results reflecting the AC data that has been generated in the background.

When the source table is a partition table, the created AC will also be partitioned, and its partition boundary values, quantities, partition tablespaces, storage properties, etc., will be consistent with the source table.

Statement Definition
----

**create access constraint::=**

```ebnf
= CREATE ACCESS CONSTRAINT [schema "."] ac_name FROM [schema "."] table_name ac_model_clause.
```

**[ac_model_clause](#acmodelclause)::=**

```ebnf
= ON xy_clause [n_clause] [where_clause] [aggr_clause][order_clause][ac_attr_clause].
```

**[xy_clause](#xyclause)::=**

```ebnf
= ( column_expr { "," column_expr } TO | ONLY ) column_expr  { "," column_expr }.
```

**[column_expr](#columnexpr)::=**

```ebnf
= (column_name|column_oper_expr) [[AS] alias] .
```

**[column_oper_expr](#columnoperexpr)::=**

```ebnf
= (column_name|literal) ("+"|"-"|"*"|"/"|"%") (column_name|literal).
```

**[n_clause](#nclause)::=**

```ebnf
= BOUND literal.
```

**[where_clause](#whereclause)::=**

```ebnf
= WHERE filter_clause [(AND|OR) filter_clause].
```

**[filter_clause](#flterclause)::=**

```ebnf
= ((column_name|literal) ("<"|"<="|">"|">="|"="|"!=") (column_name|literal))
| (column_name IS [NOT] NULL)
| (column_name IN "(" literal {"," literal} ")").
```

**[aggr_clause](#aggrclause)::=**

```ebnf
= INCLUDE aggr_func [[AS] alias] { "," aggr_func [[AS] alias]}.
```

**[order_clause](#orderclause)::=**

```ebnf
= [NO] ORDER.
```

**[ac_attr_clause](#acattrclause)::=**

```ebnf
= TABLESPACE (tablespace_name|DEFAULT).
```

### ac\_name

This statement is used to specify the name of the created AC, which is mandatory and must comply with YashanDB's [object naming convention](../Basic SQL Elements/Identifiers).

### table\_name

This statement is used to specify the source table for which the AC is to be created. An exclusive lock will be applied to this table during the operation of creating the AC.

<span id="acmodelclause" name="acmodelclause"></span>

### ac_model_clause

This clause is used to establish the AC data model.

<span id="xyclause" name="xyclause"></span>

#### xy_clause

This statement is used to specify the x and y values in the AC. The model can be established using the TO keyword for an x to y model, or using the ONLY keyword for a model that contains only y values. An AC object can only be created once for the same combination of x and y values unless the object is deleted.

Both x and y values consist of one or more expressions based on column fields, separated by `,`.

<span id="columnexpr" name="columnexpr"></span>

##### column_expr

This statement is used to specify expressions based on column fields for x/y values. The expressions can be:

- Column fields of the table for which the AC is being created.
<span id="columnoperexpr" name="columnoperexpr"></span>
- Arithmetic expressions involving a column field and a constant, or between column fields.

At least one column field must be included in the expression, and none of the column fields included in the expression can be of LOB (Large Object) type.

An alias can be specified for each expression; if not specified, a default name will be used. An error will be reported if the alias duplicates other aliases or default aliases.

***Example*** for LSC tables

```sql
CREATE ACCESS CONSTRAINT ac_area FROM area
ON area_no TO area_name,dhq;
```

<span id="nclause" name="nclause"></span>

#### n_clause

This statement is used to specify the boundary size of the AC. BOUND is a reserved keyword with no actual meaning and can be omitted, in which case it defaults to the maximum value of int64.

If this statement is specified, the following constraint rules must be met:

- The value of BOUND must be a positive integer, with an upper limit of the maximum value of int64, which is 9223372036854775807.
- It can only be specified as a single constant.

<span id="whereclause" name="whereclause"></span>

#### where_clause

This statement is used to set the filtering conditions for the AC data. It can be omitted, indicating that all data will be retrieved.

<span id="flterclause" name="flterclause"></span>

##### filter_clause

Conditions that can be used to filter AC model data include:

- =, !=, >, >=, <, <=: These conditions must involve either column fields with column fields or column fields with constants.
- IS NULL, IS NOT NULL: These conditions must assess column fields.
- IN: This condition must assess column fields, and the set following IN may only contain constants.

***Example*** for LSC tables

```sql
DROP ACCESS CONSTRAINT ac_area;
CREATE ACCESS CONSTRAINT ac_area FROM area
ON area_name TO area_no,dhq
WHERE dhq IN ('Shanghai','Chengdu')
AND area_name IS NOT NULL;
```

<span id="aggrclause" name="aggrclause"></span>

#### aggr_clause

This statement is used to specify aggregation information for the AC columns. It can be omitted, indicating that no aggregation will be performed.

Multiple aggregation information can be specified, separated by `,`, but each aggregation information can only apply to one column field. The aggregate functions that can be executed include:

- SUM
- MAX
- MIN
- COUNT

The above aggregate functions cannot be nested, and the same column cannot have the same aggregation operation in a single AC.

***Example*** for LSC tables

```sql
DROP ACCESS CONSTRAINT ac_area;
CREATE ACCESS CONSTRAINT ac_area FROM area
ON area_name TO area_no,dhq
WHERE dhq IN ('Shanghai','Chengdu')
AND area_name IS NOT NULL
INCLUDE COUNT(dhq),MAX(area_no);
```

<span id="orderclause" name="orderclause"></span>

#### order_clause

This statement is used to specify whether the data of the AC is sorted. If omitted, it defaults to ORDER.

<span id="acattrclause" name="acattrclause"></span>

#### ac_attr_clause

This statement is used to specify the tablespace for the AC. If omitted, it defaults to the tablespace of the source table. In an ISC Distributed Cluster Deployment, users cannot specify a tablespace for an AC created on a sharded table; the tablespace of the table will be used as the tablespace for the AC.

*   tablespace_name: Specifies an existing tablespace.
*   DEFAULT: Specifies the default tablespace, which is the tablespace of the source table.

For AC objects created on LSC tables, the tablespace must be specified as a databucket tablespace.

***Example*** for Standalone Deployment LSC tables

```sql
DROP ACCESS CONSTRAINT ac_area;
CREATE ACCESS CONSTRAINT ac_area FROM area
ON area_name TO area_no,dhq
NO ORDER
TABLESPACE DEFAULT;
```
