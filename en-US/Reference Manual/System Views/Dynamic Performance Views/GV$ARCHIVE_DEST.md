This view displays the primary/standby link configuration information.

|Field |Type |Description |
| ------------------ | ------------ | ------------------------------------------------------------ |
| GROUP_ID           | NUMBER       | Group ID                                                     |
| GROUP_NODE_ID      | NUMBER       | Node ID within the group                                     |
| INST_ID            | NUMBER       | Instance ID                                                 |
| DEST_ID            | TINYINT      | Standby database ID, corresponding to the ARCHIVE_DEST_x parameter |
| DEST_NAME          | VARCHAR(16)  | Parameter name                                              |
| SERVICE            | VARCHAR(256) | Remote service address                                      |
| NET_TIMEOUT        | BIGINT       | In Maximize availability mode, the timeout for waiting for a response from the standby database when submitting a transaction |
| AFFIRM             | VARCHAR(8)   | Whether to wait for the standby database to flush logs before returning ACK |
| VALID_NOW          | VARCHAR(8)   | Whether the link parameters are effective: <br/>* YES: The database role of this link is effective for the current database <br/>* NO: The database role specified by this link is not the role of the currently running database, and the link is not effective |
| VALID_ROLE         | VARCHAR(16)  | Database role that takes effect for the link                |
| DB_UNIQUE_NAME     | VARCHAR(31)  | Name of the standby database                                  |
| NODE_ID            | VARCHAR(64)  | Node identifier, in the format service_id-group_id-node_id  |
| DISABLE_ELECTION   | VARCHAR(8)   | Indicates whether the node pointed to by this link participates in the leader election or voting of the primary/standby cluster. Default is FALSE. If set to TRUE, it indicates that the node pointed to by this link only synchronizes the database and does not participate in leader election voting and selection. |