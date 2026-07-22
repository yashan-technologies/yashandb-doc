In this product documentation, when describing the definition of each SQL statement or PL statement, we generate syntax diagrams that visually represent the grammar based on its EBNF (Extended Backus-Naur Form). This article will explain the basic components of these syntax diagrams.

**Simplest Syntax Diagram**

***Example***: Defining the SQL keyword for deletion.

```ebnf
drop_table = DROP TABLE table_name.
```

**|**: Defines multiple branches, and one branch must be chosen.

***Example***: Defining operations that can be selected during alteration, such as adding a column or dropping a column.

```ebnf
alter_table = ALTER TABLE table_name (add_column_clause | drop_column_clause).
```

**""**: When the operation to be displayed contains keywords that overlap with EBNF, or when multiple operations need to be shown within one box, use double quotes to enclose them.

***Example***: In the specific syntax for adding a column, the ADD keyword must be followed by "(", without double quotes it would be parsed according to EBNF meaning, while with double quotes it is parsed as its literal meaning.

```ebnf
alter_table = ALTER TABLE table_name (ADD "(" column_name dataType ")" | drop_column_clause).
```

**[]**: Operations within [] are optional and can be omitted.

***Example***: In the specific syntax for adding a column, defining a default value for the column is an optional item.

```ebnf
alter_table = ALTER TABLE table_name (ADD "(" column_name dataType [default_expr] ")" | drop_column_clause).
```

**{}**: Defines a loop of operations specified within {}.

***Example***: During alteration, multiple columns can be added simultaneously, separated by commas (`,`), and the syntax for each column definition is identical; this can be defined as a loop of the same operation.

```ebnf
alter_table = ALTER TABLE table_name (ADD "(" column_name dataType [default_expr] { "," column_name dataType [default_expr]} ")" | drop_column_clause).
```
