本视图显示数据库所有用户信息。

|  字段| 类型| 说明|
| --- |---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| USERNAME | VARCHAR(64)   | 用户名                                                                                                                                                                                                         |
| USER_ID | INTEGER       | 用户ID                                                                                                                                                                                                        |
| PASSWORD | VARCHAR(4000) | 加密密文                                                                                                                                                                                                        |
| ACCOUNT_STATUS | VARCHAR(49)   | 用户状态<br>\* OPEN<br/>* EXPIRED<br/>* EXPIRED(GRACE)<br/>* LOCKED(TIMED)<br/>* LOCKED<br/>* EXPIRED & LOCKED(TIMED)<br/>* EXPIRED(GRACE) & LOCKED(TIMED)<br/>* EXPIRED & LOCKED<br/>* EXPIRED(GRACE) & LOCKED |
| LOCK_DATE | DATE          | 如果用户状态为锁定状态，则表示锁定用户的时间                                                                                                                                                                                      |
| EXPIRY_DATE | DATE          | 用户密码到期时间                                                                                                                                                                                                      |
| DEFAULT_TABLESPACE | VARCHAR(64)   | 默认表空间                                                                                                                                                                                                       |
| CREATED | DATE          | 用户创建时间                                                                                                                                                                                                      |
| AUTHENTICATION_TYPE | VARCHAR(8)    | 指示用户的身份验证机制<br>\* PASSWORD<br>\* EXTERNAL<br>\* GLOBAL                                                                                                                                                      |
| LAST_LOGIN | DATE          | 用户最后一次登录时间                                                                                                                                                                                                  |
| PASSWORD_CHANGE_DATE | DATE          | 用户最后一次密码更改时间                                                                                                                                                                                                |
| DATABASE_MAINTAINED | VARCHAR(1)    | 是否DATABASE MAINTAINED<br>\*   Y：数据库系统创建的USER<br>\*   N：用户创建的USER                                                                                                                                            |
| PROFILE | VARCHAR(128)  | 用户对应的PROFILE名称                                                                                                                                                                                              |
| TEMPORARY_TABLESPACE   | VARCHAR(64)   | 默认临时表空间                                                                                                                                                                                                  |
| LOCAL_TEMP_TABLESPACE  | VARCHAR(64)   | 默认本地临时表空间                                                                                                                      |
| DEFAULT_TABLESPACE_SET | VARCHAR(64)   | 默认表空间集（仅在存算一体分布式集群部署中有意义，其他部署形态中该值恒为NULL）                                                                                                                                              |
|TYPE| VARCHAR(7) | 用户类型 |
|INITIAL_RSRC_CONSUMER_GROUP| VARCHAR(22) | 用户初始所属资源组，SYS用户所属资源组为SYS_GROUP，其他用户为DEFAULT_CONSUMER_GROUP |
