故障诊断视图，显示存算一体分布式集群中所有节点的相关健康检查成果。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | INTEGER | 组ID |
| GROUP_NODE_ID | INTEGER | 组内节点ID |
| FINDING_ID | INTEGER | 表示结果的唯一ID |
| RUN_ID | INTEGER | 创建此结果的运行的ID |
| NAME | VARCHAR(33) | 调查结果的名称 |
| TIME_DETECTED | DATE | 检测到此发现的时间 |
| DESCRIPTION | VARCHAR(1024) | 调查结果说明 |
| DAMAGE_DESCRIPTION | VARCHAR(512) | 调查结果的可能损坏描述 |
