Generic Description
----

The constraint is used to define rules for data within a table. Only data that meets the constraint definition can be generated; otherwise, the system will prompt a constraint violation error.

The constraints are based on column field definitions and are applied in syntax related to column field attribute definitions, such as CREATE TABLE and ALTER TABLE.

To improve the efficiency of foreign key constraint checks, an index will automatically be created on the referenced columns when defining a foreign key. If the target referenced column already has an index, the existing index will be used directly without creating a new one.

Statement Definition
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



Inline constraints are defined simultaneously with the column field attributes.

The constraint item name can be specified using CONSTRAINT constraint_name; if omitted, the system will generate a default name.

Constraints that can be defined inline include UNIQUE, PRIMARY KEY (PRIMARY can be omitted), FOREIGN KEY, CHECK, and NOT NULL.



### out_of_line_constraint

Out-of-line constraints are defined independently, rather than within a specific column field attribute definition.

Constraints that can be defined out-of-line include UNIQUE, PRIMARY KEY, FOREIGN KEY, CHECK, and NOT NULL.

<span id="referencesclause" name="referencesclause"></span>

#### references_clause

When defining a foreign key constraint, REFERENCES specifies the parent table that the foreign key points to, as well as the corresponding column fields in the parent table that match the foreign key column in the child table. If only the parent table name is specified and the column name is omitted, the foreign key will automatically reference the primary key of the parent table, and the foreign key column must correspond one-to-one with the primary key column.

<span id="usingindexclause" name="usingindexclause"></span>

#### using_index_clause

This clause is used only as an attribute option for PRIMARY KEY or UNIQUE constraints, to specify the index or index attributes corresponding to the constraint item. Using this clause with other constraint types will result in an error.

<span id="outlineconstraint" name="outlineconstraint"></span>

***Example*** for Standalone Deployment Heap tables

```sql
create table major(
    id int,
    majorName varchar(20),
    constraint pk_id primary key(id)
);

alter table major add constraint alt_pk1 primary key(id);

alter table major drop primary key;

-- Foreign key constraint
create table minor(
    id int,
    miniorName varchar(20)
);
alter table minor add constraint CONS_MINOR foreign key(id) references major(id);
-- or
CREATE TABLE minor(
    id INT ,
    miniorName varchar(20),
    key idx_minor(id),
    FOREIGN KEY idx_minor_new(id) references major(id)
);
-- or
CREATE TABLE minor(
    id INT ,
    miniorName varchar(20),
    constraint CONS_MINOR FOREIGN KEY(id) references major(id) MATCH PARTIAL ON update cascade ON DELETE set null
);
```
