本视图显示所有LBAC标签策略信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| POLICY_NAME       | VARCHAR(64)   | LBAC标签策略的名称                                           |
| COLUMN_NAME | VARCHAR(64) | 标签列的名称<br />当该策略应用于目标表时，系统将自动在目标表中新增一列（列名为该参数值），用于逐行记录每行数据的标签 |
| STATUS | VARCHAR(8) | LBAC标签策略的状态，目前恒为ENABLE  |
| POLICY_OPTIONS | VARCHAR(256) | LBAC标签策略的强制控制选项 |
| POLICY_SUBSCRIBED | VARCHAR(5) | 保留参数 |
