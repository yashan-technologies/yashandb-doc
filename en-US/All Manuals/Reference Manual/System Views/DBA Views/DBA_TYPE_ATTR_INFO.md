This view displays the internal attribute information of OBJECT subtypes in all UDTs (User Defined Types).

|Field |Type |Description |
|------------|-------------|----------|
| OWNER       | VARCHAR(64)  | The username of the UDT owner |
| TYPE_NAME   | VARCHAR(64)  | The name of the UDT         |
| TOID        | BIGINT       | The TOID of the UDT        |
| TYPECODE    | TINYINT      | The type of UDT subclass     |
| ATTR_NAME   | VARCHAR(68)  | The name of the attribute    |
| ATTR_NO     | INTEGER      | The number of the attribute   |
| ATTR_TOID   | BIGINT       | The TOID of the attribute     |
| ATTR_TYPE   | VARCHAR(64)  | The type of the attribute     |
| ATTR_OWNER  | VARCHAR(64)  | The username of the attribute owner |