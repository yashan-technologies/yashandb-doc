This view shows the dependency of all objects.

|Field |Type |Description |
| --- | --- | --- |
| OWNER                   | VARCHAR(64) | Object owner, this object is the dependent      |
| NAME                    | VARCHAR(64) | Object name, this object is the dependent                    |
| TYPE                    | VARCHAR(17) | Object type, this object is the dependent                    |
| REFERENCED_OWNER        | VARCHAR(64) | Owner of the referenced object    |
| REFERENCED_NAME         | VARCHAR(64) | Name of the referenced object       |
| REFERENCED_TYPE         | VARCHAR(17) | Type of the referenced object        |
| REFERENCED_LINK_NAME    | VARCHAR(1)  | Reserved field                 |
| DEPENDENCY_TYPE         | VARCHAR(4)  | Reserved field, value is HARD, indicating strong dependency of the object |