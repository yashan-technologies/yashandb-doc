本视图显示有关钱包状态和透明数据加密（TDE）的钱包位置的信息。

分布式部署中，本视图恒为空。

| 字段  | 类型  | 说明 |
|-------|------|------|
|GROUP_ID      | NUMBER | 组ID |
|GROUP_NODE_ID | NUMBER | 组内节点ID |
|INST_ID       | NUMBER | 实例ID|
|WRL_TYPE|INTEGER|钱包资源定位器的类型：<br/>* 0：表示还不确定钱包文件位置，该情况可能发生在钱包未打开的时候。 <br/> * 1：表示表示钱包文件存在本地文件系统。 <br/> * 2：表示钱包文件存在YFS。|
|WRL_PARAMETER|VARCHAR(4000)|钱包资源定位器的参数：<br/>如果WRL_TYPE=0，则显示NULL; <br/>其它情况则显示钱包或密钥库的绝对目录位置。|
|STATUS|INTEGER|表示钱包的状态<br/>* 0：表示CLOSED，钱包已关闭。 <br/> * 1：表示OPEN_NO_MASTER_KEY，钱包已打开，但未设置主密钥。<br/> * 2： 表示OPEN，钱包已打开并且已经设置主密钥。|
|WALLET_TYPE|INTEGER|显示正在使用的密钥库的类型。<br/>* 0：表示UNKNOWN，还不确定钱包的类型，该情况可能发生在钱包未打开的时候。 <br/> * 1：表示密钥库文件存储在文件里面。 <br/>|
|WALLET_ORDER|INTEGER|表示钱包查找的顺序：<br/>* 0：表示仅配置单个钱包。<br/>当钱包关闭时显示为NULL。
|
|KEYSTORE_MODE|INTEGER|显示密钥库模式：<br/>* 0：表示未使用密钥库模式。|
|FULLY_BACKED_UP|INTEGER|指示密钥库中的所有密钥是否都已备份。<br/>* 0：表示未备份。<br/>* 1：表示已备份。|
