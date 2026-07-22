This view displays the primary/standby link configuration information in an ISC distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID        | INTEGER     | Group ID                                                                                      |
| GROUP_NODE_ID   | INTEGER     | Node ID within the group                                                                     |
| DEST_ID         | TINYINT     | Standby database ID, corresponding to the ARCHIVE_DEST_x parameter                          |
| DEST_NAME       | VARCHAR(16) | Parameter name                                                                                |
| SERVICE         | VARCHAR(256)| Remote service address                                                                        |
| NET_TIMEOUT      | BIGINT      | In Maximize availability mode, the timeout for waiting for the standby database response to transaction commit |
| AFFIRM          | VARCHAR(8)  | Whether to wait for the standby database to flush logs before returning ACK                  |
| VALID_NOW       | VARCHAR(8)  | Whether the link parameters are effective: <br/> * YES: The database role of this link is valid for the current database <br/> * NO: The database role specified by this link is not the role of the currently running database, and the link is ineffective |
| VALID_ROLE      | VARCHAR(16) | Effective database role for the link                                                         |
| DB_UNIQUE_NAME  | VARCHAR(31) | Unique database name                                                                          |
| NODE_ID         | VARCHAR(64) | Node identifier, formatted as service_id-group_id-node_id                                    |
| DISABLE_ELECTION | VARCHAR(8)  | Indicates whether the node pointed to by this link participates in primary/standby cluster leader election or voting. Defaults to FALSE; if specified as TRUE, it signifies that the node pointed to by this link only synchronizes the database and does not participate in leader election voting and selection. |