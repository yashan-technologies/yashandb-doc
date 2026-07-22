This view displays information about all roles in the database.

|Field |Type |Description |
| :-- | --- | --- |
| ROLE       | VARCHAR(64)| Role name                                                                                             |
| ROLE_ID    | INTEGER    | Role ID                                                                                               |
| TYPE       | VARCHAR(11)| Role Type<br/>*   NORMAL ROLE: Normal role<br/>*   SYSTEM ROLE: System-level privilege role<br/>*   OBJECT ROLE: Object-level privilege role |
| SYS_MAINTAINED | VARCHAR(3)| Is SYS MAINTAINED<br/>*   YES: Built-in database role<br/>*   NO: User-created role                   |