本视图显示所有的表上设置的行访问控制策略信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| POLICY_NAME | VARCHAR(64) | 策略名称 |
| SCHEMA_NAME | VARCHAR(64) | 用户名 |
| TABLE_NAME | VARCHAR(64) | 表名 |
| STATUS | VARCHAR(8) | 状态类型<br>\*   ENABLE<br>\*   DISABLE |
| TABLE_OPTIONS | VARCHAR(4000) | 应用到表上的策略控制项 |
| FUNCTION | VARCHAR(1024) | 保留参数 |
| PREDICATE | VARCHAR(256) | 保留参数 |
