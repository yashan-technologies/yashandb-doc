通用描述
----

constraint用于对表中的数据进行指定规则的约束定义，只有符合约束定义的数据才能被生成，否则系统将会提示违反约束错误。

约束基于列字段定义，被应用于CREATE TABLE、ALTER TABLE等与列字段属性定义相关的语法中。

为提高外键约束的检查效率，定义外键时会自动在引用列上创建索引，若目标引用列已存在索引，创建外键时会直接使用已有的索引，不会重复创建新的索引。

语句定义
----

**constraint::=**

```ebnf
= inline_constraint|out_of_line_constraint.
```

**[inline\_constraint](#inlineconstraint)::=**

```ebnf
= (UNIQUE|[PRIMARY] KEY|references_clause|CHECK condition|([NOT] NULL)).
```

**[references\_clause](#referencesclause)::=**

```ebnf
= REFERENCES [schema "."] ref_table_name ( ["(" (column_name {"," column_name}) ")"] [MATCH FULL|MATCH PARTIAL|MATCH SIMPLE] [ON DELETE (RESTRICT|NO ACTION|CASCADE|SET NULL)] [ON UPDATE (RESTRICT|CASCADE|SET NULL)]).
```

**[using\_index\_clause](#usingindexclause)::=**

```ebnf
= USING INDEX [(["schema."] index_name | "(" create_index_clause ")" | index_attr_clause )].
```

**[out\_of\_line\_constraint](#outlineconstraint)::=**

```ebnf
= [CONSTRAINT constraint_name] ((UNIQUE "(" (column_name {"," column_name}) ")")|(PRIMARY KEY "(" (column_name {"," column_name}) ")")|(FOREIGN KEY "(" (column_name {"," column_name}) ")" references_clause)|CHECK condition) [constraint_state].
```

<span id="inlineconstraint" name="inlineconstraint"></span>

### inline_constraint



行内约束，即在定义列字段属性的同时为其定义约束项。

可通过CONSTRAINT constraint\_name指定约束项的名称，省略则由系统生成默认名称。

可以在行内定义的约束项包括UNIQUE、PRIMARY KEY（PRIMARY可省略）、FOREIGN KEY、CHECK、NOT NULL。



### out_of_line_constraint

行外约束，即不在某一个列字段的属性定义中，而是独立定义。

可以在行外定义的约束项包括UNIQUE、PRIMARY KEY、FOREIGN KEY、CHECK、NOT NULL。

<span id="referencesclause" name="referencesclause"></span>

#### references_clause

在定义外键约束时，REFERENCES指定了外键指向的父表，及父表中与子表外键列一一对应的列字段。若仅标识父表名而省略列名，则外键会自动引用父表的主键并且外键列与主键列需要一一对应。

<span id="usingindexclause" name="usingindexclause"></span>

#### using_index_clause

该语句只作为PRIMARY KEY或UNIQUE约束项的属性选项使用，用于指定约束项对应的索引或索引属性，对其他约束项使用本语句将报错。

<span id="outlineconstraint" name="outlineconstraint"></span>

示例（HEAP表）

```sql
create table major(
    id int,
    majorName varchar(20),
    constraint pk_id primary key(id)
);

alter table major add constraint alt_pk1 primary key(id);

alter table major drop primary key;

--外键约束
create table minor(
    id int,
    miniorName varchar(20)
);
alter table minor add constraint CONS_MINOR foreign key(id) references major(id);
--或者
CREATE TABLE minor(
    id INT ,
    miniorName varchar(20),
    key idx_minor(id),
    FOREIGN KEY idx_minor_new(id) references major(id)
);
--或者
CREATE TABLE minor(
    id INT ,
    miniorName varchar(20),
    constraint CONS_MINOR FOREIGN KEY(id) references major(id) MATCH PARTIAL ON update cascade ON DELETE set null
);
```
