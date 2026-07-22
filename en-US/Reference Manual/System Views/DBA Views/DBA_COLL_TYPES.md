This view displays the details of composite types in all UDTs (User-Defined Types).

|Field |Type |Description |
| --- |-------------|-------------------------------------------------------|
| OWNER          | VARCHAR(64)   | The username of the UDT owner                           |
| TYPE_NAME      | VARCHAR(64)   | The name of the UDT                                     |
| COLL_TYPE      | VARCHAR(13)   | The type of composite type<br> * TABLE: nested table <br> * VARYING ARRAY: variable-length array<br> |
| UPPER_BOUND    | INTEGER       | The maximum number of tuples for array types            |
| ELEM_TYPE_MOD  | VARCHAR(7)    | Reserved                                                |
| ELEM_TYPE_OWNER| VARCHAR(64)   | The username of the element owner. NULL for built-in types.|
| ELEM_TYPE_NAME | VARCHAR(64)   | The type name of the element                            |
| LENGTH         | INTEGER       | The length of the element                               |
| PRECISION      | INTEGER       | The precision of the element                            |
| SCALE          | INTEGER       | The scale of the element                                |
| CHARACTER_SET_NAME | TINYINT   | Reserved                                                |
| ELEM_STORAGE   | VARCHAR(1)    | Reserved                                                |
| NULLS_STORED   | VARCHAR(3)    | Whether NULL values can be stored                       |