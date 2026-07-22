This view displays relevant statistics for all temporary LOBs.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID      | NUMBER   | Group ID                                      |
| GROUP_NODE_ID | NUMBER   | Node ID within the group                      |
| INST_ID       | NUMBER   | Instance ID                                   |
| SID           | SMALLINT | Session ID                                    |
| CACHE_LOBS    | BIGINT   | Number of temporary LOBs cached on the session |
| NOCACHE_LOBS  | BIGINT   | Number of temporary LOBs created by calling DBMS_LOB.CREATETEMPORARY |
| ABSTRACT_LOBS | BIGINT   | Number of implicitly created temporary LOBs, not counted, value is 0 |