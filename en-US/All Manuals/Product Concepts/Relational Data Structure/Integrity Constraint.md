Data integrity primarily refers to the correctness, consistency, and validity of data within a database. It encompasses various aspects, such as ensuring that data meets business definitions and criteria requirements: for instance, an ID number must be unique, the salary range must relate to the job title, and an employee's residence must exist.

In practical applications, ensuring data integrity is crucial and can typically be achieved through various methods, including executing business rules in database applications or using database triggers to invoke stored procedures.

YashanDB provides integrity constraint functionality to more conveniently ensure data integrity from the moment of data entry.

Integrity constraints are rules defined on columns or objects that limit the values in the database to comply with specific rules (sets). Database administrators and business application developers clearly define data integrity constraints to ensure data integrity and normal business operations.

The advantages of using integrity constraints are as follows:

- User-friendly: Integrity constraints can be defined using SQL statements without any additional programming. SQL statements are easy to write, reducing the possibility of programming errors.

- Centralized and unified rules: Integrity constraints are defined on tables and stored in the data dictionary. All business application operations on table data must comply with the unified integrity constraints, irrespective of changes to the constraint rules.

- Flexible: Integrity constraints can be temporarily disabled when loading large amounts of data (to be re-enabled after data loading), reducing or avoiding unnecessary performance overhead.

## Types of Integrity Constraints

YashanDB supports the application of constraints at both column and table levels. When creating a table, constraints can be specified directly after the column description or added at the end of the entire create table statement.

Integrity constraints can be categorized as follows:

|Constraint Type |Description |
| ------------------- | -------------------- |
| NOT NULL Constraint         | Specifies whether a column can be NULL; it can only be specified after the column description in the create table statement. |
| Unique Constraint           | Specifies whether different rows are allowed to have duplicate values in the same column or a combination of multiple columns (NULL values are allowed). |
| Primary Key Constraint      | Satisfies both NOT NULL and Unique constraints.                             |
| Foreign Key Constraint      | Specifies a column as a foreign key and establishes a relationship between the foreign key and a primary or unique key. The foreign key values must exist within the primary or unique key. |
| Check Constraint            | Requires that the corresponding column satisfies specified conditions.      |

### NOT NULL Constraint

By default, all columns in a table allow NULL values. Using the NOT NULL constraint specifies that a column cannot be NULL.

The NOT NULL constraint is mainly used for columns that cannot have missing values, such as the name column in an employee information table. If the name column specifies a NOT NULL constraint, an error will be prompted when inserting employee information without a name:

```sql
INSERT INTO employees(employee_id) VALUES(1);

YAS-04006 cannot insert NULL value to column employee_name
```

If the table already contains data, a column with a NOT NULL constraint cannot be added unless the corresponding column also has a DEFAULT value defined.

### Unique Constraint

The unique constraint stipulates that each value in the specified column or column set is unique. In a table, multiple rows are not allowed to have duplicate values in columns that have a unique constraint (unique key) or a combination of columns (composite unique key).

A unique key constraint is suitable for any column that does not allow duplicate values. However, a unique constraint typically only requires the values to be unique and does not necessarily have practical significance.

Columns with unique constraints can all be NULL and still satisfy the unique key constraint.

### Primary Key Constraint

In a primary key constraint, the values in the column or set of columns can uniquely identify a row. Each table can only have one primary key, which serves to identify rows and ensures no duplicate rows exist.

A primary key can be a meaningful attribute column in the table (e.g., ID number) or an arbitrary column (e.g., a user-defined sequence ID column).

YashanDB's primary key constraint guarantees:

- No two rows in the specified primary key column have duplicate values.

- The primary key column does not allow NULL values.

For example, in an employee information table, each employee must have a unique ID (employee number); the information for each employee must be recorded by only one row in the employee table:

```sql
INSERT INTO employees(employee_id) VALUES (1);

YAS-02030 unique constraint violated

INSERT INTO employees(name) VALUES('Ming');

YAS-04006 cannot insert NULL value to column employee_id
```

YashanDB implements primary key constraints using indexes. Typically, creating a primary key constraint on a column implicitly creates a unique index and a NOT NULL constraint. However, if there is an existing index available when creating the primary key constraint, that index will be reused instead of implicitly creating a new index.

### Foreign Key Constraint

When two tables share one or more common columns, YashanDB can establish a relationship between the two tables by creating a foreign key constraint.

Usually, foreign key constraints require that each value in the constraint column or column set must match the values in the specified column of another table, such as ensuring that classes taught by instructors must exist in the class information table (a foreign key constraint from the instructor information table to the class information table).

|Term |Definition |
| -------------- | ------------------------------------------------------------ |
| Foreign Key/Composite Foreign Key | The columns included in the constraint definition are called foreign keys; when a foreign key consists of multiple columns, it is called a composite foreign key. A foreign key references the primary key or unique key of another table, while a composite foreign key must reference a composite primary key or composite unique key with the same number and data types of columns. The value of a foreign key must either match the value of the referenced primary key or unique key or be NULL. If a composite foreign key includes a NULL column, the non-NULL columns of that key are no longer required to match the corresponding columns in the parent. |
| Referenced Key   | The unique key or primary key in the table being referenced by the foreign key. |
| Dependent Table   | Also known as the "child table," the table in which the foreign key constraint exists. |
| Referenced Table | Also known as the "parent table," the table referenced by the child table's foreign key. The referenced values in this table determine whether specific inserts or updates in the child table are allowed. |

#### Relationship between Parent Table Modifications and Foreign Keys

Deleting or updating data in the parent table may violate foreign key constraints. When modifying the parent key, referential integrity constraints can define actions to be taken on the related rows in the child table to satisfy the foreign key constraint. These actions include:

- NO ACTION

  Under normal circumstances, if the modification would violate the foreign key constraint, the user cannot make the change.

- DELETE CASCADE

  Cascade Delete means that when the parent table is deleted, all corresponding rows with the referenced key values in the child table will also be deleted.

- DELETE SET NULL

  Delete Set Null means that when the parent table is deleted, the foreign key in all rows with the referenced key values in the child table will be set to NULL.

- UPDATE CASCADE

  Cascade Update means that when the parent table is updated, all corresponding rows with the referenced key values in the child table will also be updated with the new values.

- UPDATE SET NULL

  Update Set Null means that when the parent table is updated, the foreign key in all rows with the referenced key values in the child table will be set to NULL.

#### Indexes and Foreign Keys

YashanDB recommends creating indexes on foreign key columns, with advantages including:

- Preventing modification of the parent table from triggering exclusive locks on the child table.

- Preventing modification of the parent table from triggering full table scans on the child table.

### Check Constraint

Check constraints have the ability to enforce specific integrity rules. Creating check constraints on specified columns or sets of columns ensures that the data in the table meets specified conditions. If a DML statement violates the conditions of a check constraint, an error will occur.

For example, setting a check constraint on the age column to not exceed 60, the insertion would look as follows:

```sql
INSERT INTO employees(age) VALUES(61);

YAS-02254 check constraint (MAX_EMP_AGE) violated
```

Multiple check constraints can be specified for the same column, but they should ensure all check constraints are logically consistent; otherwise, they may render the table unusable. For example, if a column is simultaneously specified with "value must be greater than 5000" and "value must be less than 4999," the two check constraints are mutually exclusive and set unreasonably.

## Manifestation of Integrity Constraints

YashanDB can specify whether a constraint is enabled (for new data) and whether it is validated (for existing data), determining the specific manifestation of the constraint.

If specified as enabled (ENABLE), inserting/updating data will check whether the new data conforms to the constraints; data that violates the constraints cannot be inserted/updated in the table.

If specified as validated (VALIDATE), it will check whether existing data complies with the constraints. If existing data violates the constraints, specifying validation (VALIDATE) will fail and return an error. Unique constraints and primary key constraints are guaranteed by unique indexes, which will validate even if specified as not validated (NOVALIDATE); if existing data violates the constraints, an error will still be returned.

With checks on both enabling and validating, the specific manifestation of constraints is as follows:

|Is Enabled |Is Validated |Constraint Manifestation |
| ------- | ---------- | ------------------------------------ |
| ENABLE     | VALIDATE     | Existing data in the table and future new data must comply with the constraint. |
| ENABLE     | NOVALIDATE   | Existing data in the table may not comply with the constraint, but future new data must comply with the constraint. |
| DISABLE    | VALIDATE     | Existing data in the table must comply with the constraint, but the current constraint is inactive (indexes on the constraints are deleted), and future new data need not comply with the constraint. |
| DISABLE    | NOVALIDATE   | Existing data in the table may not comply with the constraint, and the current constraint is inactive, with future new data needing not comply with the constraint. |