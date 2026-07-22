This view shows all SEQUENCE information in the database.

|Field |Type |Description |
| --- | --- | --- |
| SEQUENCE\_OWNER | VARCHAR(64) | The ID of the user to whom the SEQUENCE belongs |
| SEQUENCE\_NAME | VARCHAR(64) | The name of the SEQUENCE |
| MIN\_VALUE | NUMBER | The minimum value of the SEQUENCE |
| MAX\_VALUE | NUMBER | The maximum value of the SEQUENCE |
| INCREMENT\_BY | NUMBER | The increment of the SEQUENCE |
| CYCLE\_FLAG | VARCHAR(1) | Whether to cycle through SEQUENCE values after exceeding the maximum value<br>\*   0: NO<br>\*   1: YES |
| ORDER\_FLAG | VARCHAR(1) | Indicates whether SEQUENCE values are generated in order |
| CACHE\_SIZE | NUMBER | The length of SEQUENCE values buffered in memory each time |
| LAST\_NUMBER | NUMBER | The last persisted SEQUENCE value. If a SEQUENCE buffer is used, then the persisted SEQUENCE will be greater than or equal to the last used SEQUENCE |
| DATABASE\_MAINTAINED | VARCHAR(1) | Whether it is DATABASE MAINTAINED<br>\*   Y: SEQUENCE created by the database system<br>\*   N: SEQUENCE created by the user |