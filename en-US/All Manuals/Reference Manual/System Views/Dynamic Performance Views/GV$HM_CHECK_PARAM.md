Fault Diagnosis View, showing the parameter information corresponding to the health inspection items.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID     | NUMBER  | Group ID                                             |
| GROUP_NODE_ID| NUMBER  | Node ID within the group                            |
| INST_ID      | NUMBER  | Instance ID                                         |
| ID           | INTEGER | Parameter ID                                        |
| NAME         | VARCHAR(64) | Parameter name                                   |
| CHECK_ID     | INTEGER | ID of the inspection item to which this parameter belongs |
| TYPE         | VARCHAR(32) | Data type of the input parameter <br>*   HM_PARAM_INTEGER: The type of the parameter is numeric<br>*   HM_PARAM_TEXT: The type of the parameter is string |
| DESCRIPTION  | VARCHAR(1024) | Description of the check functionality         |