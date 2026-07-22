|Field |Type |Description |
| ------------------ | ------------ | ------------------------------------------------------------ |
| DEST\_ID           | TINYINT      | Standby database ID, corresponding to ARCHIVE\_DEST\_x parameter |
| DEST\_NAME         | VARCHAR(16)  | Parameter name                                             |
| SERVICE            | VARCHAR(256) | Remote service address                                     |
| NET\_TIMEOUT        | BIGINT       | In Maximize availability mode, the timeout for waiting for standby database response during transaction commit |
| AFFIRM             | VARCHAR(8)   | Whether to wait for the standby database to flush logs before returning ACK |
| VALID\_NOW          | VARCHAR(8)   | Whether the link parameter is effective: <br/>* YES: The database role of this link is valid for the current database <br/>* NO: The database role specified by this link is not the role of the currently running database, and this link is not effective |
| VALID\_ROLE         | VARCHAR(16)  | Database role that is effective for the link              |
| DB\_UNIQUE\_NAME     | VARCHAR(31)  | Name of the Standby database                                |
| NODE\_ID            | VARCHAR(64)  | Node ID, format service\_id-group\_id-node\_id           |
| DISABLE\_ELECTION   | VARCHAR(8)   | Marks whether the node pointed to by this link participates in the primary/standby cluster's leader election or voting. Default is FALSE. If set to TRUE, it indicates that the node pointed to by this link only synchronizes the database and does not participate in leader election voting and election |