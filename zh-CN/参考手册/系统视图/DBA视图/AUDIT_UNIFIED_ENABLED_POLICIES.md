本视图显示使能的审计策略信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| POLICY_NAME | VARCHAR(64) | 审计策略名称 |
| ENABLED_OPTION | VARCHAR(15) | 审计策略的启用选项<br>\*  BY USER：针对的用户 <br>\*  EXCEPT USER：排除的用户 |
| ENTITY_NAME | VARCHAR(64) | 审计策略针对的用户或角色，若针对所有用户则值为 "ALL USERS" |
| ENTITY_TYPE | VARCHAR(7) | 审计策略针对的类型<br>\*  USER |
| SUCCESS | VARCHAR(3) | "YES" 表示审计成功事件，"NO" 表示不审计成功事件 |
| FAILURE | VARCHAR(3) | "YES" 表示审计失败事件，"NO" 表示不审计失败事件 |
