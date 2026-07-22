This view displays state information about the dictionary cache.

|Field |Type |Description |
|:----------------------|:------------| :----------------------------------------------------------- |
| GROUP_ID              | NUMBER      | Group ID                                                    |
| GROUP_NODE_ID         | NUMBER      | Node ID within the group                                    |
| INST\_ID              | NUMBER      | Instance ID                                                 |
| USER\_ID              | INTEGER     | User ID of the dictionary cache object                    |
| OBJECT\_ID            | BIGINT      | ID of the dictionary cache object                          |
| NAME                  | VARCHAR(68) | Object name                                                |
| VERSION               | INTEGER     | Version information, used for internal database comparison of object ages |
| TYPE                  | INTEGER     | Object type of the dictionary cache object [object type](../DBA Views/DBA_OBJECTS)             |  
| IN\_RECYCLE\_BIN      | INTEGER     | Indicates whether the object is pending recycling           |
| VALID                 | INTEGER     | Indicates whether the object is valid in the dictionary cache<br/>\* 1: indicates the dictionary cache object is valid<br/> \* 0: indicates the dictionary cache object is invalid. When the dictionary cache object is invalid, the corresponding buffer field may be recycled at any time. To re-access the object after recycling, the dictionary cache object needs to be reloaded into the buffer.<br/>\* NULL: indicates the dictionary cache object has not been loaded yet.<br/> When VALID is 0 or NULL, the fields REF\_COUNT, MEMORY\_CONTEXT\_USED, and LOAD\_SCN have no reference meaning. |
| PVT                   | INTEGER     | Indicates whether the object is a private temporary table object. When the object is a private temporary table object, the fields REF\_COUNT, MEMORY\_CONTEXT\_USED, and LOAD\_SCN have no reference meaning. |
| REF\_COUNT            | INTEGER     | Reference count of the current dictionary cache object      |
| MEMORY\_CONTEXT\_USED | BIGINT      | Current memory usage in bytes for the dictionary cache object context |
| LOAD\_SCN             | BIGINT      | SCN number when the current dictionary cache object was loaded |
| NOLOGGING\_INSTANCE\_ID| TINYINT    | Instance ID of the current dictionary cache object with nologging enabled; defaults to -1 if the object is not a nologging table |