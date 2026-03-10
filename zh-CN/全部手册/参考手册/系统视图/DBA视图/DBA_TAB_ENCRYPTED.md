本视图显示表的加密信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| OWNER | VARCHAR(64) | 加密表所属对象的用户名 |
| TABLE_NAME | VARCHAR(64) | 表名   |
| TABLE_TYPE | VARCHAR(8)  | 表的类型<br>\* HEAP<br>\* TAC<br>\* LSC  |
| ENC_ALG | VARCHAR(6) | 表加密算法 |
| ENC_KEYVER | INTEGER | 表加密密钥版本 |
