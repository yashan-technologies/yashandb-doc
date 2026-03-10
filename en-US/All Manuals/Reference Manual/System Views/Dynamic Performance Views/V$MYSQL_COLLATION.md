This view shows the collation information supported by the database.

|Field |Type |Description |
|-----------|-------------|----------|
| COLLATION    | VARCHAR(32)  | Collation name     |
| CHARSET      | VARCHAR(32)  | Corresponding charset name |
| ID           | BIGINT       | Collation ID      |
| DEFAULT      | VARCHAR(3)   | Whether it is the default collation |
| COMPILED     | VARCHAR(3)   | Whether it is loaded |
| SORTLEN      | BIGINT       | Byte length required for sorting |
| YAS_COLLATION_ID | INTEGER  | YashanDB collation ID corresponding to MySQL collation |