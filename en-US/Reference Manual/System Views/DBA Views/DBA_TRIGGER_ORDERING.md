This view shows information about the ordering of triggers that have FOLLOWS or PRECEDES relationships.

|Field |Type |Description |
| --- | --- | --- |
| TRIGGER_OWNER               | VARCHAR(64) | Username of the trigger owner           |
| TRIGGER_NAME                | VARCHAR(64) | Name of the trigger                     |
| REFERENCED_TRIGGER_OWNER    | VARCHAR(128)| Username of the referenced trigger owner |
| REFERENCED_TRIGGER_NAME      | VARCHAR(128)| Name of the referenced trigger          |
| ORDERING_TYPE               | VARCHAR(8)  | Ordering relationship between the trigger and the referenced trigger<br> * FOLLOWS |