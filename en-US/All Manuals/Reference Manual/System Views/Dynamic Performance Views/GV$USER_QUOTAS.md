This view displays the tablespace quotas corresponding to all users and the usage of those quotas. When no quota has been set for a tablespace, the relevant information for that tablespace will not be displayed.

|Field |Type |Description |
| --- |-------------|-------------------------------------------------------------------------------------|
| GROUP_ID       | NUMBER        | Group ID                                                                           |
| GROUP_NODE_ID  | NUMBER        | Node ID within the group                                                           |
| INST_ID        | NUMBER        | Instance ID                                                                        |
| TABLESPACE_NAME| VARCHAR(64)   | Tablespace name                                                                    |
| USERNAME       | VARCHAR(64)   | Username                                                                           |
| BYTES          | BIGINT        | Number of bytes used in the corresponding tablespace                               |
| MAX_BYTES      | BIGINT        | Maximum available number of bytes                                                   |
| MIN_BYTES      | BIGINT        | Minimum available number of bytes, that is, the space that needs to be reserved for the user in the tablespace |
| BLOCKS         | BIGINT        | Number of used blocks in the corresponding tablespace                               |
| MAX_BLOCKS     | BIGINT        | Maximum number of blocks the user can use in the tablespace                        |
| MIN_BLOCKS     | BIGINT        | Minimum number of blocks the user can use in the tablespace                        |