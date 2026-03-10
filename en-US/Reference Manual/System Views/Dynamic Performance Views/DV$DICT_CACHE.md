This view shows the status information of all nodes in the dictionary buffer within a distributed cluster.

|Field |Type |Description |
| --------------------- | ----------- | ------------------ |
| GROUP_ID             | INTEGER     | Group ID                        |
| GROUP_NODE_ID       | INTEGER     | Node ID within the group        |
| USER_ID              | INTEGER     | User ID of the dictionary buffer object |
| OBJECT_ID            | BIGINT      | ID of the dictionary buffer object |
| NAME                  | VARCHAR(68) | Object name                     |
| VERSION               | INTEGER     | Version information, used for internal database evaluation of the object's age |
| TYPE                  | INTEGER     | [Object type](../DBA Views/DBA_OBJECTS) of the dictionary buffer object |
| IN_RECYCLE_BIN      | INTEGER     | Whether the object is pending recycling |
| VALID                 | INTEGER     | Indicates whether the object is valid in the dictionary buffer <br/> \* 1: indicates the dictionary buffer object is valid; <br/> \* 0: indicates the dictionary buffer object is invalid. When the dictionary buffer object is invalid, the corresponding buffer field may be reclaimed at any time. If re-accessing the object after recycling is needed, the dictionary buffer object must be reloaded into the buffer. <br/> \* NULL: indicates the dictionary buffer object has not yet been loaded. <br/> When VALID is 0 or NULL, the fields REF_COUNT, MEMORY_CONTEXT_USED, and LOAD_SCN have no referential significance. |
| PVT                   | INTEGER     | Indicates whether the object is a private temporary table object; when the object is a private temporary table object, the fields REF_COUNT, MEMORY_CONTEXT_USED, and LOAD_SCN have no referential significance. |
| REF_COUNT            | INTEGER     | Current reference count of the dictionary buffer object |
| MEMORY_CONTEXT_USED | BIGINT      | Current memory usage in bytes of the dictionary buffer object context |
| LOAD_SCN             | BIGINT      | SCN number when the current dictionary buffer object was loaded |