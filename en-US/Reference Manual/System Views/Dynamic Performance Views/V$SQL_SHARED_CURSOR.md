This view records the reasons why SQL child cursors cannot be shared. When a SQL statement fails to share child cursors due to certain causes, the system creates new child cursors. This view is used to diagnose the specific causes of unshared child cursors. 

|Field |Type |Description |
| ----- | ----------- | ------------ |
| SQL_ID | VARCHAR(13) | SQL identifier |
| ADDRESS | RAW(8) | Address of the parent cursor |
| CHILD_ADDRESS | RAW(8) | Address of the child cursor |
| CHILD_NUMBER | INTEGER | Child cursor number |
| OPTIMIZER_MISMATCH | VARCHAR(1) | Whether this factor causes SQL child cursor unshareable:<br/>* Y: caused by this factor, optimizer environment information mismatch<br/>* N: not caused by this factor |
| STATS_ROW_MISMATCH | VARCHAR(1) | Whether this factor causes SQL child cursor unshareable:<br/>* Y: caused by this factor, statistics information mismatch<br/>* N: not caused by this factor |
| BIND_MISMATCH | VARCHAR(1) | Whether this factor causes SQL child cursor unshareable:<br/>* Y: caused by this factor, binding metadata mismatch with existing child cursor<br/>* N: not caused by this factor |
| TRANSLATION_MISMATCH | VARCHAR(1) | Whether this factor causes SQL child cursor unshareable:<br/>* Y: caused by this factor, base object mismatch of existing child cursor<br/>* N: not caused by this factor |
| BIND_EQUIV_FAILURE | VARCHAR(1) | Whether this factor causes SQL child cursor unshareable:<br/>* Y: caused by this factor, selectivity mismatch of bind values<br/>* N: not caused by this factor |
| PX_MISMATCH | VARCHAR(1) | Whether this factor causes SQL child cursor unshareable:<br/>* Y: caused by this factor, parameters affecting parallelization mismatch<br/>* N: not caused by this factor |
| INST_MISMATCH | VARCHAR(1) | Whether this factor causes SQL child cursor unshareable:<br/>* Y: caused by this factor, cluster instance mapping mismatch<br/>* N: not caused by this factor |