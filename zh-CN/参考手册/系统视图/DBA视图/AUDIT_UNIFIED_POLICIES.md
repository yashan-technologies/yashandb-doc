本视图显示审计策略信息。

|  字段| 类型| 说明|
| --- | --- |-----------------------------------------------------------------------------------------|
| POLICY_NAME | VARCHAR(64) | 审计策略名称                                                                                  |
| AUDIT_CONDITION | VARCHAR(4000) | 审计策略相关的条件                                                                               |
| CONDITION_EVAL_OPT | VARCHAR(9) | 审计策略条件判断执行频率<br>\* STATEMENT：语句级别<br>\* SESSION：会话级别<br>\* INSTANCE：实例级别<br>\* NONE：未指定 |
| AUDIT_OPTION | VARCHAR(64) | 审计项                                                                                     |
| AUDIT_OPTION_TYPE | VARCHAR(16) | 审计项类型<br>\*   STANDARD ACTION：标准行为审计<br>\*   OBJECT ACTION：对象行为审计                       |
| OBJECT_SCHEMA | VARCHAR(64) | 对象拥有者                                                                                   |
| OBJECT_NAME | VARCHAR(64) | 对象名                                                                                     |
| OBJECT_TYPE | VARCHAR(18) | 对象类型                                                                                    |
| AUDIT_ONLY_TOPLEVEL | VARCHAR(3) | "YES" 表示将审核策略定义为仅审核顶级SQL语句<br>"NO" 表示同时审核顶级SQL和递归SQL语句                                  |
