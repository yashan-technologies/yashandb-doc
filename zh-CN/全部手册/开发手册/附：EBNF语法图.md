本产品文档中，在描述每一个SQL语句或PL语句定义时，依据其EBNF巴科斯范式，同时生成了可直观展现的语法图。本文将对这些语法图的基本构成元素进行解释。

**最简单的语法图**

示例：定义删除表的SQL关键字。

```ebnf+diagram
drop_table::= DROP TABLE table_name
```

**|：定义多个分支，且必须选择一个分支**

示例：定义更改表时可选择的操作，添加列字段或者删除列字段。

```ebnf+diagram
alter_table::= ALTER TABLE table_name (add_column_clause | drop_column_clause)
```

**""：当要显示的操作里含有与EBNF重叠的关键字，或者多个操作要显示在一个框内时，用双引号进行包围**

示例：在添加列字段的具体语法中，ADD关键字后需要跟"("，此时没有双引号的话会按照EBNF的含义来解析，用双引号则解析成其字面含义。

```ebnf+diagram
alter_table::= ALTER TABLE table_name (ADD "(" column_name dataType ")" | drop_column_clause)
```

**[]：[]内的操作是可选的，可以略过**

示例：在添加列字段的具体语法中，为列字段定义缺省值是一个可选项。

```ebnf+diagram
alter_table::= ALTER TABLE table_name (ADD "(" column_name dataType [default_expr] ")" | drop_column_clause)
```

**{}：循环{}内定义的操作**

示例：更改表时可以同时添加多个列字段，语法中用逗号（`,`）分隔，且每个列字段的定义语法完全相同，此时可以定义成相同操作的循环。

```ebnf+diagram
alter_table::= ALTER TABLE table_name (ADD "(" column_name dataType [default_expr] { "," column_name dataType [default_expr]} ")" | drop_column_clause)
```
