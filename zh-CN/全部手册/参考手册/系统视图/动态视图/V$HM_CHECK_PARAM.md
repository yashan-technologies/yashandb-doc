故障诊断视图，显示健康巡检项目对应的参数信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| ID | INTEGER | 参数ID |
| NAME | VARCHAR(64) | 参数名称 |
| CHECK_ID | INTEGER | 此参数所属的检查项的ID |
| TYPE | VARCHAR(32) | 输入参数的数据类型 <br>\*   HM_PARAM_INTEGER：参数的类型是数值<br>\*   HM_PARAM_TEXT：参数的类型是字符串 |
| DESCRIPTION | VARCHAR(1024) | 检查功能的说明 |
