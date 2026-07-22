本视图显示所有访问受控的用户及其对应的策略、标签信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| USER_NAME | VARCHAR(64) | 用户的名称 |
| POLICY_NAME         | VARCHAR(64)   | LBAC标签策略的名称                |
| MAX_READ_LABEL | VARCHAR(4000) | 最大读标签内容 |
| MAX_WRITE_LABEL | VARCHAR(4000) | 最大写标签内容 |
| MIN_WRITE_LABEL | VARCHAR(4000) | 最小写标签内容 |
| DEFAULT_READ_LABEL | VARCHAR(4000) | 默认读标签内容 |
| DEFAULT_WRITE_LABEL | VARCHAR(4000) | 默认写标签内容 |
| DEFAULT_ROW_LABEL | VARCHAR(4000) | 默认行标签内容 |
