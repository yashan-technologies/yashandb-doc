本视图显示主密钥描述属性。

存算一体分布式集群部署中，本视图恒为空。

|  字段| 类型| 说明|
|--------------------------|------------ |-------|
|GROUP_ID                  |NUMBER       |组ID |
|GROUP_NODE_ID             |NUMBER       |组内节点ID |
|INST_ID                   |NUMBER       |实例ID|
|KEY_ID                    |VARCHAR(78)  |主密钥标识符|
|TAG                       |VARCHAR(4000)|与主密钥关联的用户定义信息|
|CREATION_TIME             |TIMESTAMP(6)    |主密钥创建时间|
|ACTIVATION_TIME           |TIMESTAMP(6)    |主密钥投入使用的时间|
|CREATOR                   |VARCHAR(68)  |创建主密钥的用户|
|CREATOR_ID                |INTEGER      |创建主密钥的用户ID|
|USER#                     |VARCHAR(68)  |激活主密钥的用户，如果未激活，则显示为null。|
|USER_ID                   |INTEGER      |激活主密钥的用户 ID，如果未激活，则显示为null。|
|KEY_USE                   |VARCHAR(64)  |表示主密钥是用于何种场景，如：'TDE'|
|KEYSTORE_TYPE             |INTEGER      |表示主密钥位置：<br/> * 0：表示主密钥位置未知。<br/> * 1：表示主密钥用软件管理。|
|ORIGIN                    |INTEGER      |表示主密钥来源的信息：<br/> * 0：表示主密钥是在此数据库中本地创建的。<br/> * 1：表示主密钥是从另一个数据库导入的。|
|BACKED_UP                 |INTEGER      |表示密钥是否已备份|
|CREATOR_DBNAME            |VARCHAR(68)  |创建密钥的数据库，如果是Nomount状态创建的则为空字符串|
|CREATOR_DBID              |INTEGER      |创建密钥的数据库ID，如果是Nomount状态则为-1。|
|CREATOR_INSTANCE_NAME     |VARCHAR(256) |创建密钥的实例的实例名称，为主机hostname|
|CREATOR_INSTANCE_NUMBER   |INTEGER      |创建密钥的实例的实例编号，单机或分布式下恒为0，集群模式下为实例ID。|
|CREATOR_INSTANCE_SERIAL   |INTEGER      |创建密钥的实例的序列号，暂无实际含义，恒为-1。|
|ACTIVATING_DBNAME         |VARCHAR(68)  |激活密钥的数据库， 如果未激活，则显示为null|
|ACTIVATING_DBID           |INTEGER      |激活密钥的数据库ID， 如果未激活，则显示为null|
|ACTIVATING_INSTANCE_NAME  |VARCHAR(256) |激活密钥的实例的实例名称， 如果未激活，则显示为null|
|ACTIVATING_INSTANCE_NUMBER|INTEGER      |激活密钥的实例的实例编号， 如果未激活，则显示为null|
|ACTIVATING_INSTANCE_SERIAL|INTEGER      |激活密钥的实例的序列号， 如果未激活，则显示为null|
