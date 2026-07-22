This view displays all UDT (User Defined Type) information.

|Field |Type |Description |
| --- | --- |-----------------------------------------------|
| OWNER               | VARCHAR(64) | The username of the owner of the UDT                |
| TYPE_NAME           | VARCHAR(64) | The name of the UDT                                 |
| TYPE_OID            | BIGINT      | The identifier of the UDT                           |
| TYPECODE            | VARCHAR(64) | The type of the UDT<br>  * OBJECT: Object <br>  * COLLECTION: Collection |
| ATTRIBUTES          | INTEGER     | The number of attributes in the OBJECT              |
| METHODS             | INTEGER     | The number of methods in the OBJECT                 |
| PREDEFINED          | VARCHAR(3)  | Whether the UDT is a predefined type                |
| INCOMPLETE          | VARCHAR(3)  | Whether the UDT is incomplete                        |
| FINAL               | VARCHAR(3)  | Whether the UDT is a final type                     |
| INSTANTIABLE        | VARCHAR(3)  | Whether the UDT is instantiable                     |
| PERSISTABLE         | VARCHAR(3)  | Whether the UDT is persistable                      |
| SUPERTYPE_OWNER     | VARCHAR(64) | The username of the supertype. NULL if not a subtype |
| SUPERTYPE_NAME      | VARCHAR(64) | The name of the supertype. NULL if not a subtype    |
| LOCAL_ATTRIBUTES    | INTEGER     | The number of non-inherited attributes in the OBJECT |
| LOCAL_METHODS       | INTEGER     | The number of non-inherited methods in the OBJECT    |
| TYPEID              | BIGINT      | The type ID of the UDT                              |