This view displays the tablespace quotas for all users and the usage of those quotas. When no quota is set for a tablespace, the related information for that tablespace is not displayed.

|Field |Type |Description |
| --- |-------------|-------------------------------------------------------------------------------------|
| TABLESPACE_NAME | VARCHAR(64)  | Tablespace name                                                                          |
| USERNAME        | VARCHAR(64)  | Username                                                                                 |
| BYTES           | BIGINT       | The number of bytes used in the corresponding tablespace                                 |
| MAX_BYTES       | BIGINT       | The maximum available number of bytes                                                    |
| MIN_BYTES       | BIGINT       | The minimum available number of bytes, which is the space the tablespace needs to reserve for the user |
| BLOCKS          | BIGINT       | The number of blocks used in the corresponding tablespace                                 |
| MAX_BLOCKS      | BIGINT       | The maximum number of blocks a user can use in this tablespace                          |
| MIN_BLOCKS      | BIGINT       | The minimum number of blocks a user can use in this tablespace                          |