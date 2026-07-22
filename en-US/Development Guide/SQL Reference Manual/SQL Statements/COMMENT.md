General Description
----

COMMENT is used to add comments to a table|view or the column fields of a table|view. The added comment information can be queried in system views such as USER_TAB_COMMENTS/USER_COL_COMMENTS.

Comments that have been created cannot be deleted or modified. Each execution of COMMENT will overwrite the previous comment information. To delete the comment information, specify the CONTENT of COMMENT as ''.

Statement Definition
----

**comment::=**

```ebnf
= COMMENT ON (TABLE [schema "."] (table|view)|COLUMN [schema "."] (table|view) "." column) IS string.
```

### TABLE table|view

This statement is used to specify the name of the table|view to which comment information will be added.

***Example***

```sql
COMMENT ON TABLE area IS 'Area Table';
SELECT table_name,table_type,comments FROM USER_TAB_COMMENTS WHERE table_name='AREA';
TABLE_NAME        TABLE_TYPE COMMENTS    
----------------- ---------- -------------
AREA              TABLE      Area Table  
  
-- Clear comment
COMMENT ON TABLE area IS '';
SELECT table_name,table_type,comments FROM USER_TAB_COMMENTS WHERE table_name='AREA';
TABLE_NAME        TABLE_TYPE COMMENTS    
----------------- ---------- -------------
AREA              TABLE
```

### COLUMN table|view

This statement is used to specify the name of the column field to which comment information will be added.

***Example***

```sql
COMMENT ON COLUMN area.DHQ IS 'Regional Headquarters';
SELECT table_name,column_name,comments FROM USER_COL_COMMENTS WHERE table_name='AREA' AND column_name='DHQ';
TABLE_NAME        COLUMN_NAME       COMMENTS     
----------------- ----------------- --------------
AREA              DHQ               Regional Headquarters     
  
-- Clear comment
COMMENT ON COLUMN area.DHQ IS '';
SELECT table_name,column_name,comments FROM USER_COL_COMMENTS WHERE table_name='AREA' AND column_name='DHQ';             
TABLE_NAME        COLUMN_NAME       COMMENTS     
----------------- ----------------- --------------
AREA              DHQ
```
