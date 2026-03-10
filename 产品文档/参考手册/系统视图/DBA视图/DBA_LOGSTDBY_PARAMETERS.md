本视图显示逻辑备库SQL回放的参数信息。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
|SERVER_ID    |INTEGER    |YSTREAM的服务编号（逻辑备库使用固定的编号32）|
|SERVER_NAME  |CHAR(15)|YSTREAM的服务名（逻辑备库使用固定的服务名：LOGICAL_STANDBY）|
|PARAM_NAME   |VARCHAR(64)|参数名|
|PARAM_VALUE  |VARCHAR(64)|参数值|
|PARAM_DEFAULT|VARCHAR(64)|默认值|
