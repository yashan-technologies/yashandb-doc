本视图显示所有的行访问控制策略信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| POLICY_NAME | VARCHAR(64) | 策略名称 |
| COLUMN_NAME | VARCHAR(64) | 标签列名称，受策略保护的表会增加此名称的列 |
| STATUS | VARCHAR(8) | 状态类型<br>\*   ENABLE<br>\*   DISABLE |
| POLICY_OPTIONS | VARCHAR(256) | 策略控制项 |
| POLICY_SUBSCRIBED | VARCHAR(5) | 保留参数 |
