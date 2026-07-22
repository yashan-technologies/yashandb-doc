This view displays information about all CDB resource plans.

This view is meaningful only when YashanDB is deployed as a CDB (configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE).


|Field |Type |Description |
|---------------|--------------|--------------------|
| PLAN_ID   | BIGINT        | ID of the CDB resource plan |
| PLAN      | VARCHAR(64)   | Name of the CDB resource plan |
| COMMENTS  | VARCHAR(2000) | Comment of the resource plan |
| STATUS    | VARCHAR(1)    | Compatibility only, and the current value is fixed to NULL. |
| MANDATORY | VARCHAR(3)    | Is Mandatory Existence or not (Cannot be deleted): YES or NO |