Fault Diagnosis View, showing the parameter information corresponding to the health inspection items.

|Field |Type |Description |
| --- | --- | --- |
| ID | INTEGER | Parameter ID |
| NAME | VARCHAR(64) | Parameter Name |
| CHECK\_ID | INTEGER | ID of the inspection item to which this parameter belongs |
| TYPE | VARCHAR(32) | Data type of the input parameter <br>\*   HM_PARAM_INTEGER: The type of the parameter is numeric<br>\*   HM_PARAM_TEXT: The type of the parameter is string |
| DESCRIPTION | VARCHAR(1024) | Description of the functionality being checked |