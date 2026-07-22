General Description
----

CREATE VIEW is used to create a view object. The created view can be selected and can serve as an object for INSERT/UPDATE/DELETE under certain conditions.

If this statement includes a DML operation on a certain table, the corresponding DML privileges on that table are required to successfully create the view, and all constraints of the corresponding DML must be followed.

Statement Definition
----

**create view::=**

```ebnf
= CREATE [OR REPLACE] [FORCE|NO FORCE] [EDITIONABLE|NONEDITIONABLE] VIEW [schema "."] view_name ["(" alias {","alias} ")"] AS subquery [subquery_restriction_clause].
```

**subquery_restriction_clause::=**

```ebnf
= WITH (READ ONLY | CHECK OPTION) [CONSTRAINT constraint].
```

### OR REPLACE

This clause is used to specify that if the view to be created already exists, it will be rebuilt.

### FORCE|NO FORCE

Specifying FORCE indicates a forced creation of the view. The default is NO FORCE, which means an error will be returned if the conditions are not met, rather than forcing the creation of the view.

When the objects referenced by the subquery do not exist, or the view owner does not have access privileges to the objects referenced in the subquery, specifying FORCE can forcibly create the view, but using this view may result in errors.

The advantage of using FORCE is that once the dependent objects are created and the view owner has the necessary access privileges, the view can be successfully queried without needing to be recreated.

***Example***

```sql
CREATE FORCE VIEW v_table_not_exsits AS SELECT * FROM table_not_exsits;
```

### EDITIONABLE | NONEDITIONABLE

Used for syntax compatibility; it has no actual meaning.

### view\_name

This clause is used to specify the name of the view to be created. It is mandatory and must comply with YashanDB's [object naming conventions](../Basic SQL Elements/Identifiers).

### alias

This clause is used to specify aliases for the view's columns. It is optional and must comply with YashanDB's [object naming conventions](../Basic SQL Elements/Identifiers). It cannot be used to change the data type of the columns.

### AS subquery

This clause is used to specify the subquery statement for creating the view. The rules for the subquery are as described in the [SELECT](SELECT) statement. The columns in the subquery cannot be specified as a sequence number.

If the subquery includes a SELECT * operation on a certain table:

* The created view will have all columns of that table as its own columns.
* If the table adds new columns, this view will not reflect that change and will only include the original column items.

***Example***

```sql
CREATE VIEW v_area AS SELECT * FROM area;
```

**View Invalidity**

When the dependent sub-objects of a view undergo certain changes, the view becomes invalid, and its status changes to invalid.

* If a column in a base table is deleted, the view object becomes invalid. When querying the view:
    * If a non-dependent column is deleted, querying the view will trigger the view to become valid again.
    * If a dependent column is deleted, querying the view will still result in the view being invalid, and the view's column information will become unknown. At this point, the deleted column must be re-added and the view queried again.
* If a column in a base table is renamed, the view object becomes invalid. When querying the view:
    * If a non-dependent column is renamed, querying the view will trigger the view to become valid again.
    * If a dependent column is renamed, querying the view will still result in the view being invalid, and the view's column information will become unknown. At this point, the column must be renamed back to its original name and queried again.
* If a column in a base table is modified (data type or attributes), the view object becomes invalid. When querying the view:
    * If the modification does not affect the view, the view will successfully recompile and querying it will trigger the view to become valid again.
    * If the modification affects the view, causing compilation to fail, the view will still be invalid, and the view's column information will become unknown. The column must be restored and the view queried again.
* Deleting or renaming objects that the view depends on will also cause the view to become invalid.

**View Metadata**

When the objects that the view relies on change (as mentioned above), it triggers a refresh of the view's metadata.

When the objects that the view relies on are synonyms or DBLink, changes to the connecting objects cannot invalidate the view, nor refresh the view's metadata.

After upgrading from an old version to the current version, the view is valid, but the view's metadata may be incorrect (if the base table has undergone structural changes). In this case, the view's metadata cannot be refreshed. The view needs to be rebuilt, or a DDL operation needs to be executed on the base table to trigger a refresh of the view's metadata.

If the view is invalid, querying it on the standby database will result in an error. The view needs to be recompiled on the primary database to refresh its status, allowing the standby database to query it.

### WITH READ ONLY

Specifying this clause indicates that the created view is read-only, meaning that the data in the view cannot be modified. YashanDB defaults to this mode.

***Example***

```sql
CREATE VIEW v_area_readonly AS SELECT * FROM area WITH READ ONLY;
```

### WITH CHECK OPTION

Specifying this clause indicates that the created view will perform additional checks during insert and update operations. If the data being inserted or updated does not satisfy the original filtering conditions of the base table from which the view was created (i.e., if the update or insert were successful, the data would not appear in the view), it will result in an error. This check only takes effect when there is exactly one base table for the view. If there are multiple base tables, its effect is equivalent to the READ ONLY clause.

***Example*** for Heap tables

```sql
CREATE VIEW v_area_check_option AS SELECT * FROM area WHERE area_no > 5 WITH CHECK OPTION;

INSERT INTO v_area_check_option VALUES('02', 'WestChina', 'Chengdu');

YAS-02755 view WITH CHECK OPTION where-clause violation
```

### CONSTRAINT constraint

This clause is used for syntax compatibility.