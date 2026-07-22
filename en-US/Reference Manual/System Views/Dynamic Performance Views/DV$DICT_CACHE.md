This view shows the status information of all nodes in the dictionary cache within an ISC distributed cluster.

|Field |Type |Description |
| --------------------- | ----------- | ------------------ |
| GROUP\_ID             | INTEGER     | Group ID                        |
| GROUP\_NODE\_ID       | INTEGER     | Node ID within the group        |
| USER\_ID              | INTEGER     | User ID of the dictionary cache object |
| OBJECT\_ID            | BIGINT      | ID of the dictionary cache object |
| NAME                  | VARCHAR(68) | Object name                     |
| VERSION               | INTEGER     | Version information, used for internal database evaluation of the object's age |
| TYPE                  | INTEGER     | [Object type](../DBA Views/DBA_OBJECTS) of the dictionary cache object |
| IN\_RECYCLE\_BIN      | INTEGER     | Whether the object is pending recycling |
| VALID                 | INTEGER     | Indicates whether the object is valid in the dictionary cache <br/> \* 1: indicates the dictionary cache object is valid; <br/> \* 0: indicates the dictionary cache object is invalid. When the dictionary cache object is invalid, the corresponding buffer field may be reclaimed at any time. If re-accessing the object after recycling is needed, the dictionary cache object must be reloaded into the buffer. <br/> \* NULL: indicates the dictionary cache object has not yet been loaded. <br/> When VALID is 0 or NULL, the fields REF\_COUNT, MEMORY\_CONTEXT\_USED, and LOAD\_SCN have no referential significance. |
| PVT                   | INTEGER     | Indicates whether the object is a private temporary table object; when the object is a private temporary table object, the fields REF\_COUNT, MEMORY\_CONTEXT\_USED, and LOAD\_SCN have no referential significance. |
| REF\_COUNT            | INTEGER     | Current reference count of the dictionary cache object |
| MEMORY\_CONTEXT\_USED | BIGINT      | Current memory usage in bytes of the dictionary cache object context |
| LOAD\_SCN             | BIGINT      | SCN number when the current dictionary cache object was loaded |