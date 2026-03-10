This view displays the status information of the dictionary buffer.

|Field |Type |Description |
| :-------------------- | :---------- | :-------------------- |
| USER_ID              | INTEGER     | User ID of the dictionary buffer object   |
| OBJECT_ID            | BIGINT      | ID of the dictionary buffer object         |
| NAME                  | VARCHAR(68) | Name of the object                       |
| VERSION               | INTEGER     | Version information, used for internal database comparison of the object's state |
| TYPE                  | INTEGER     | [Object type](../DBA Views/DBA_OBJECTS) of the dictionary buffer object      |
| IN_RECYCLE_BIN      | INTEGER     | Whether the object is pending for recycling |
| VALID                 | INTEGER     | Indicates whether the object is valid in the dictionary buffer<br/> * 1: Indicates the dictionary buffer object is valid<br/> * 0: Indicates the dictionary buffer object is invalid. When the dictionary buffer object is invalid, the corresponding buffer field may be recycled at any time. If you need to access the object again after recycling, you need to reload the dictionary buffer object into the buffer. <br/> * NULL: Indicates the dictionary buffer object has not been loaded yet<br/> When VALID is 0 or NULL, the fields REF_COUNT, MEMORY_CONTEXT_USED, and LOAD_SCN have no reference significance |
| PVT                   | INTEGER     | Indicates whether the object is a private temporary table. When the object is a private temporary table, the fields REF_COUNT, MEMORY_CONTEXT_USED, and LOAD_SCN have no reference significance |
| REF_COUNT            | INTEGER     | Reference count of the current dictionary buffer object |
| MEMORY_CONTEXT_USED | BIGINT      | Current memory usage in bytes of the dictionary buffer object's context  |
| LOAD_SCN             | BIGINT      | SCN number when the current dictionary buffer object was loaded                |
| NOLOGGING_INSTANCE_ID| TINYINT    | ID of the instance that opened nologging for the current dictionary buffer object; defaults to -1 if the object is not a nologging table |