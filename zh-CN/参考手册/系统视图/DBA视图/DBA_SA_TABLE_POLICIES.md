本视图显示所有已配置行访问控制的表及其对应的策略信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| POLICY_NAME    | VARCHAR(64)   | LBAC标签策略的名称                         |
| SCHEMA_NAME | VARCHAR(64) | 表的所属用户名 |
| TABLE_NAME | VARCHAR(64) | 表名 |
| STATUS | VARCHAR(8) | LBAC标签策略的状态，目前恒为ENABLE  |
| TABLE_OPTIONS | VARCHAR(4000) | 该策略应用于该表的强制控制选项 |
| FUNCTION | VARCHAR(1024) | 保留参数 |
| PREDICATE | VARCHAR(256) | 保留参数 |
