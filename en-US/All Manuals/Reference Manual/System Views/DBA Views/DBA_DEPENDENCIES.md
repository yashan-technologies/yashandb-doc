This view shows the dependency of all objects.

|Field |Type |Description |
| --- | --- | --- |
| OWNER                   | VARCHAR(64) | Object owner's username        |
| NAME                    | VARCHAR(64) | Object name                    |
| TYPE                    | VARCHAR(17) | Object type                    |
| REFERENCED_OWNER        | VARCHAR(64) | Referenced object's username    |
| REFERENCED_NAME         | VARCHAR(64) | Referenced object's name       |
| REFERENCED_TYPE         | VARCHAR(17) | Referenced object's type       |
| REFERENCED_LINK_NAME    | VARCHAR(1)  | Reserved field                 |
| DEPENDENCY_TYPE         | VARCHAR(4)  | Reserved field, value is HARD, indicating strong dependency of the object |