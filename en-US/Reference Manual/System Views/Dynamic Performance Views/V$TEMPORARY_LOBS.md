This view displays relevant statistics for all temporary LOBs.

|Field |Type |Description |
| --- | --- | --- |
| SID          | SMALLINT | Session ID                                                   |
| CACHE_LOBS    | BIGINT   | Number of temporary LOBs cached on the session |
| NOCACHE_LOBS | BIGINT   | Number of temporary LOBs created by calling DBMS_LOB.CREATETEMPORARY |
| ABSTRACT_LOBS| BIGINT   | Number of implicitly created temporary LOBs, not currently counted, value is 0 |