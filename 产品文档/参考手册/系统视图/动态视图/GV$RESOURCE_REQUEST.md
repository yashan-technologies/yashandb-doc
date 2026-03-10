本视图显示资源当前等待处理的消息 。

| 字段             | 类型           | 描述                                                        |
|----------------|--------------|-----------------------------------------------------------|
| GROUP_ID       | NUMBER       | 组ID                                                       |
| GROUP_NODE_ID  | NUMBER       | 组内节点ID                                                    |
| INST_ID       | NUMBER       | 实例ID                                                      |
| RESOURCE_NAME | VARCHAR(128) | 资源名称，block资源\[space\]\[file\]\[id\]，lock资源\[id\]\[type\]，GTID资源\[len\]\[string\] |
| TYPE           | INTEGER      | 请求类型                                                      |
| INSTANCE_ID   | INTEGER      | 发出请求消息的节点ID                                               |
| SESSION_ID    | INTEGER      | 发出请求消息的会话ID                                               |
| SERIAL_NO     | INTEGER      | 请求消息的序列号                                                  |
| IN_PROCESS    | BOOLEAN      | 当前请求是否正在处理                                                |
