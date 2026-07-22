This view displays the tablespace quotas for all users and the usage status of those quotas.

|Field |Type |Description |
| --- |-------------|-------------------------------------------------------------------------------------|
| TABLESPACE_NAME | VARCHAR(64) | Name of the tablespace                                                             |
| USERNAME      | VARCHAR(64) | Username                                                                         |
| BYTES         | NUMBER      | Number of bytes used in the corresponding tablespace                                |
| MAX_BYTES     | BIGINT      | Maximum available number of bytes                                                  |
| MIN_BYTES     | BIGINT      | Minimum available number of bytes, which is the space that needs to be reserved for this user |
| BLOCKS        | NUMBER      | Number of blocks used in the corresponding tablespace                               |
| MAX_BLOCKS    | BIGINT      | Maximum number of blocks that the user can use in this tablespace                  |
| MIN_BLOCKS    | BIGINT      | Minimum number of blocks that the user can use in this tablespace                  |