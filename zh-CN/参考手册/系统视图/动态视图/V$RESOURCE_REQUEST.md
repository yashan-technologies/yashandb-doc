本视图显示资源当前等待处理的消息 。

|  字段| 类型| 描述|
| --- | --- | --- |
| RESOURCE\_NAME | VARCHAR(128) | 资源名称，block资源\[space\]\[file\]\[id\]，lock资源\[id\]\[type\]，gtid资源\[len\]\[string\]  |
| TYPE | INTEGER | 请求类型 |
| INSTANCE\_ID | INTEGER | 发出请求消息的节点ID |
| SESSION\_ID | INTEGER | 发出请求消息的会话ID |
| SERIAL\_NO | INTEGER | 请求消息的序列号 |
| IN\_PROCESS | BOOLEAN | 当前请求是否正在处理 |
