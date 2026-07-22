This view displays information about all nested tables.

|Field |Type |Description |
|-----------------------------|----------------| --- |
| OWNER                       | VARCHAR(64)    | The user to whom the nested table belongs |
| TABLE\_NAME                 | VARCHAR(64)    | The name of the object that this column belongs to |
| TABLE\_TYPE\_OWNER          | VARCHAR(64)    | The username of the owner of the nested table type corresponding to the nested table |
| TABLE\_TYPE\_NAME           | VARCHAR(64)    | The name of the nested table type corresponding to the nested table |
| PARENT\_TABLE\_NAME         | VARCHAR(64)    | The name of the parent table of the nested table |
| PARENT\_TABLE\_COLUMN       | VARCHAR(4000)  | The column name of the parent table corresponding to the nested table |
| STORAGE\_SPEC               | VARCHAR(14)    | Whether the storage option for the nested table is user-specified (USER_SPECIFIED/DEFAULT) |
| RETURN\_TYPE                | VARCHAR(7)     | The return type of the nested table, for internal use |
| ELEMENT\_SUBSTITUTABLE      | VARCHAR(1)     | Whether the member type of the nested table is substitutable |