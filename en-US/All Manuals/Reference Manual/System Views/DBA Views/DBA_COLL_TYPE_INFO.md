This view shows the internal details of composite types in all UDTs (User Defined Types).

|Field |Type |Description |
|------------------|-------------|----------|
| OWNER            | VARCHAR(64) | Username of the UDT |
| NAME             | VARCHAR(64) | Name of the UDT     |
| TOID             | BIGINT      | TOID of the UDT     |
| TYPECODE         | TINYINT     | Type of the UDT subclass |
| COLL_VERSION     | INTEGER     | Attribute version    |
| COLL_TOID        | BIGINT      | TOID of the attribute |
| COLL_UPPER_BOUND | INTEGER     | Upper bound of the attribute |
| COLL_LENGTH      | INTEGER     | Length of the attribute |
| COLL_PRECISION   | INTEGER     | Precision of the attribute |
| COLL_SCALE       | INTEGER     | Scale of the attribute |