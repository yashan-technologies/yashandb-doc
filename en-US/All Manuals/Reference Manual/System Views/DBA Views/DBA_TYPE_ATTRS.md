This view displays the attribute information of OBJECT type in all UDT (User Defined Types).

|Field |Type |Description |
| --- | --- | --- |
| OWNER               | VARCHAR(64)   | The username of the UDT owner                        |
| TYPE_NAME           | VARCHAR(64)   | The name of the UDT                                   |
| ATTR_NAME           | VARCHAR(68)   | The name of the attribute                             |
| ATTR_TYPE_MOD       | VARCHAR(7)    | Reserved field, value is NULL                        |
| ATTR_TYPE_OWNER     | VARCHAR(64)   | The username of the attribute owner. NULL for built-in types. |
| ATTR_TYPE_NAME      | VARCHAR(64)   | The type name of the attribute                        |
| LENGTH              | INTEGER       | The length of the attribute                           |
| PRECISION           | INTEGER       | The precision of the attribute                        |
| SCALE               | INTEGER       | The scale of the attribute                            |
| CHARACTER_SET_NAME  | TINYINT       | Reserved field, value is NULL                        |
| ATTR_NO             | INTEGER       | The serial number of the attribute, starting from 1 |
| INHERITED           | VARCHAR(3)    | Indicates whether the attribute is inherited         |