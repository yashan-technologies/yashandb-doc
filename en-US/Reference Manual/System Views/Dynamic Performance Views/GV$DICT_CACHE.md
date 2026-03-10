This view displays state information about the dictionary buffer.

|Field |Type |Description |
|:----------------------|:------------| :----------------------------------------------------------- |
| GROUP_ID              | NUMBER      | Group ID                                                    |
| GROUP_NODE_ID         | NUMBER      | Node ID within the group                                    |
| INST_ID              | NUMBER      | Instance ID                                                 |
| USER_ID              | INTEGER     | User ID of the dictionary buffer object                    |
| OBJECT_ID            | BIGINT      | ID of the dictionary buffer object                          |
| NAME                  | VARCHAR(68) | Object name                                                |
| VERSION               | INTEGER     | Version information, used for internal database comparison of object ages |
| TYPE                  | INTEGER     | Object type of the dictionary buffer object [object type](../DBA Views/DBA_OBJECTS)             |  
| IN_RECYCLE_BIN      | INTEGER     | Indicates whether the object is pending recycling           |
| VALID                 | INTEGER     | Indicates whether the object is valid in the dictionary buffer<br/>\* 1: indicates the dictionary buffer object is valid<br/> \* 0: indicates the dictionary buffer object is invalid. When the dictionary buffer object is invalid, the corresponding buffer field may be recycled at any time. To re-access the object after recycling, the dictionary buffer object needs to be reloaded into the buffer.<br/>\* NULL: indicates the dictionary buffer object has not been loaded yet.<br/> When VALID is 0 or NULL, the fields REF_COUNT, MEMORY_CONTEXT_USED, and LOAD_SCN have no reference meaning. |
| PVT                   | INTEGER     | Indicates whether the object is a private temporary table object. When the object is a private temporary table object, the fields REF_COUNT, MEMORY_CONTEXT_USED, and LOAD_SCN have no reference meaning. |
| REF_COUNT            | INTEGER     | Reference count of the current dictionary buffer object      |
| MEMORY_CONTEXT_USED | BIGINT      | Current memory usage in bytes for the dictionary buffer object context |
| LOAD_SCN             | BIGINT      | SCN number when the current dictionary buffer object was loaded |
| NOLOGGING_INSTANCE_ID| TINYINT    | Instance ID of the current dictionary buffer object with nologging enabled; defaults to -1 if the object is not a nologging table |