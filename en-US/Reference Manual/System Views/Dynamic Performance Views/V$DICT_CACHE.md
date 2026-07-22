This view displays the status information of the dictionary cache.

|Field |Type |Description |
| :-------------------- | :---------- | :-------------------- |
| USER\_ID              | INTEGER     | User ID of the dictionary cache object   |
| OBJECT\_ID            | BIGINT      | ID of the dictionary cache object         |
| NAME                  | VARCHAR(68) | Name of the object                       |
| VERSION               | INTEGER     | Version information, used for internal database comparison of the object's state |
| TYPE                  | INTEGER     | [Object type](../DBA Views/DBA_OBJECTS) of the dictionary cache object      |
| IN\_RECYCLE\_BIN      | INTEGER     | Whether the object is pending for recycling |
| VALID                 | INTEGER     | Indicates whether the object is valid in the dictionary cache<br/> * 1: Indicates the dictionary cache object is valid<br/> * 0: Indicates the dictionary cache object is invalid. When the dictionary cache object is invalid, the corresponding buffer field may be recycled at any time. If you need to access the object again after recycling, you need to reload the dictionary cache object into the buffer. <br/> * NULL: Indicates the dictionary cache object has not been loaded yet<br/> When VALID is 0 or NULL, the fields REF\_COUNT, MEMORY\_CONTEXT\_USED, and LOAD\_SCN have no reference significance |
| PVT                   | INTEGER     | Indicates whether the object is a private temporary table. When the object is a private temporary table, the fields REF\_COUNT, MEMORY\_CONTEXT\_USED, and LOAD\_SCN have no reference significance |
| REF\_COUNT            | INTEGER     | Reference count of the current dictionary cache object |
| MEMORY\_CONTEXT\_USED | BIGINT      | Current memory usage in bytes of the dictionary cache object's context  |
| LOAD\_SCN             | BIGINT      | SCN number when the current dictionary cache object was loaded                |
| NOLOGGING\_INSTANCE\_ID| TINYINT    | ID of the instance that opened nologging for the current dictionary cache object; defaults to -1 if the object is not a nologging table |