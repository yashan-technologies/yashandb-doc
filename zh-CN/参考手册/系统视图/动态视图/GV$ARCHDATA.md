本视图显示全局归档切片文件清理信息，用于在集群环境下查看所有实例的归档切片文件清理状态和各实例的归档清理LFN信息。

| 字段         | 类型    | 说明               |
|--------------|---------|--------------------|
| GROUP_ID     | NUMBER  | 组ID               |
| GROUP_NODE_ID| NUMBER  | 组内节点ID         |
| INST_ID      | NUMBER  | 实例ID             |
| ARCH_INST_ID | TINYINT | 归档实例ID         |
| CLEAN_LFN    | BIGINT  | 已清理的日志文件号 |
| ARCH_MIN_LFN | BIGINT  | 归档最小日志文件号 |