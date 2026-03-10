This view displays parameter information for SQL apply in a logical standby database.

|Field |Type |Description |
| --- | --- | --- |
| SERVER_ID    | INTEGER    | Service number of YSTREAM (logical standby database uses a fixed number 32) |
| SERVER_NAME  | CHAR(15)   | Service name of YSTREAM (logical standby database uses a fixed service name: LOGICAL_STANDBY) |
| PARAM_NAME   | VARCHAR(64)| Parameter name                                      |
| PARAM_VALUE  | VARCHAR(64)| Parameter value                                     |
| PARAM_DEFAULT| VARCHAR(64)| Default value                                       |