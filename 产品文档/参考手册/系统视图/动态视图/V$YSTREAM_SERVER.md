本视图显示YStream服务的信息。

| 字段  | 类型  | 说明 |
|-------|------|------|
|SERVER_ID       |INTEGER      |服务编号|
|SERVER_NAME     |VARCHAR(64)  |服务名|
|STATUS          |VARCHAR(16)  |服务状态<br/>* CREATED：服务已创建，还未启动过<br/>* STARTED：服务已启动，没有客户端通过当前实例连接服务<br/>* RUNNING：服务已启动，客户端通过当前实例连接服务<br/>* STOPPED：服务已停止|
|NODE_ID         |VARCHAR(16)  |启动服务的节点ID|
|CREATE_TIME     |TIMESTAMP    |创建日期|
|START_SCN       |BIGINT       |起始解析的SCN|
|START_POINT     |VARCHAR(4096)  |起始解析的日志点|
|RESTART_POINT   |VARCHAR(4096)  |重启解析的日志点|
|RESTART_POSITION|VARCHAR(4096)  |重启解析的position|
|CAPTURE_POINT   |VARCHAR(4096)  |已解析的日志点|
|CAPTURE_POSITION|VARCHAR(4096)  |已解析的position|
|APPLIED_POSITION|VARCHAR(4096)  |客户端应用的position|
|ERROR           |VARCHAR(1024)|服务在当前实例上最后一条错误信息，如该服务未在当前实例运行过或报错，该字段为空，共享集群中各实例错误信息可能不同。请在最后运行该服务的实例上，查询服务的最新错误信息|
|TYPE            |VARCHAR(16)  |服务的类型<br/>* API：表示用户通过高级包创建的server<br/>* LOGICAL STANDBY：内置server，逻辑备库启动回放时生成|