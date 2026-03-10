This view shows all SEQUENCE information in the database.

|Field |Type |Description |
| --- | --- | --- |
| SEQUENCE_OWNER | VARCHAR(64) | The ID of the user to whom the SEQUENCE belongs |
| SEQUENCE_NAME | VARCHAR(64) | The name of the SEQUENCE |
| MIN_VALUE | NUMBER | The minimum value of the SEQUENCE |
| MAX_VALUE | NUMBER | The maximum value of the SEQUENCE |
| INCREMENT_BY | NUMBER | The increment of the SEQUENCE |
| CYCLE_FLAG | VARCHAR(1) | Whether to cycle through SEQUENCE values after exceeding the maximum value<br>\*   0: NO<br>\*   1: YES |
| ORDER_FLAG | VARCHAR(1) | Indicates whether SEQUENCE values are generated in order |
| CACHE_SIZE | NUMBER | The length of SEQUENCE values buffered in memory each time |
| LAST_NUMBER | NUMBER | The last persisted SEQUENCE value. If a SEQUENCE buffer is used, then the persisted SEQUENCE will be greater than or equal to the last used SEQUENCE |
| DATABASE_MAINTAINED  | VARCHAR(1)    | Whether created by the database system <br>* Y: Yes <br>* N: No (created by the user) |