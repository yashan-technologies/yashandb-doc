本视图用于查看LICENSE的相关信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| LICENSE_ID  | VARCHAR(128) | LICENSE唯一ID，使用UUID描述 |
| LICENSE_TYPE | VARCHAR(16) | LICENSE版本类型<br>\*   TRIAL表示试用版<br>\*   ENTERPRISE表示企业版|
| LICENSE_VERSION | VARCHAR(16) | LICENSE版本信息，目前仅支持v1|
| DB_VERSION | VARCHAR(16) | 数据库的软件版本号 |
| EXPIRED_DAYS | INTEGER | LICENSE有效天数 |
| ACCREDIT_DATE | TIMESTAMP | LICENSE的授权时间 |
| DEADLINE | TIMESTAMP | LICENSE截止日期 |
| ACCREDIT_CLUSTER_TYPE | VARCHAR(64) | 授权的集群类型<br>\*   单机部署/共享集群/分布式集群部署（ALL）<br>\*   共享集群/分布式集群部署（CE）<br>\*   单机部署（SE）|
| CONCURRENCY_USER_NUMBER | BIGINT | 最大并发连接数|
| ACCREDIT_MAX_NODE | BIGINT | 最大节点数|
| ESN | VARCHAR(1024) | 设备信息|
| LICENSE_SIGN | VARCHAR(256) | LICENSE签名密文信息 |
