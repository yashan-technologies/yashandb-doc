This view is used to view information related to LICENSE.

|Field |Type |Description |
| --- | --- | --- |
| LICENSE_ID            | VARCHAR(128)| Unique ID for LICENSE, described using UUID                  |
| LICENSE_TYPE          | VARCHAR(16) | LICENSE version type<br>\*   TRIAL indicates trial version<br>\*   ENTERPRISE indicates enterprise version |
| LICENSE_VERSION       | VARCHAR(16) | LICENSE version information, currently only supports v1      |
| DB_VERSION            | VARCHAR(16) | Database software version number                             |
| EXPIRED_DAYS          | INTEGER     | Number of valid days for LICENSE                             |
| ACCREDIT_DATE         | TIMESTAMP(6)   | Authorization time for LICENSE                               |
| DEADLINE              | TIMESTAMP(6)   | LICENSE expiration date                                      |
| ACCREDIT_CLUSTER_TYPE  | VARCHAR(64) | Authorized cluster type<br>\*   Standalone/YAC/Distributed Cluster Deployment (ALL)<br>\*   YAC/Distributed Cluster Deployment (CE)<br>\*   Standalone Deployment (SE) |
| CONCURRENCY_USER_NUMBER| BIGINT     | Maximum number of concurrent connections                      |
| ACCREDIT_MAX_NODE     | BIGINT     | Maximum number of nodes                                      |
| ESN                   | VARCHAR(1024)| Device information                                           |
| LICENSE_SIGN          | VARCHAR(256)| Encrypted information for LICENSE signature                   |