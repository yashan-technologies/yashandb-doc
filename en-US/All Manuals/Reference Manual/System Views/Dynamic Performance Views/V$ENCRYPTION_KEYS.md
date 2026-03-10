This view displays the attributes of the master key description.

In an ISC Distributed Cluster Deployment, this view is always empty.

|Field |Type |Description |
|--------------------------|------------ |-------|
| KEY_ID                    | VARCHAR(78)  | Master key identifier                    |
| TAG                       | VARCHAR(4000)| User-defined information associated with the master key |
| CREATION_TIME             | TIMESTAMP    | Master key creation time                 |
| ACTIVATION_TIME           | TIMESTAMP    | Time when the master key was put into use |
| CREATOR                   | VARCHAR(68)  | User who created the master key         |
| CREATOR_ID                | INTEGER      | User ID of the creator of the master key |
| USER#                     | VARCHAR(68)  | User who activated the master key; displays as null if not activated |
| USER_ID                   | INTEGER      | User ID of the user who activated the master key; displays as null if not activated |
| KEY_USE                   | VARCHAR(64)  | Indicates the scenario for which the master key is used, e.g., 'TDE' |
| KEYSTORE_TYPE             | INTEGER      | Indicates the location of the master key:<br/> * 0: Location of the master key is unknown.<br/> * 1: Master key is managed by software. |
| ORIGIN                    | INTEGER      | Indicates the source information of the master key:<br/> * 0: The master key was created locally in this database.<br/> * 1: The master key was imported from another database. |
| BACKED_UP                 | INTEGER      | Indicates whether the key has been backed up |
| CREATOR_DBNAME            | VARCHAR(68)  | Database that created the key; displays as an empty string if created in Nomount state |
| CREATOR_DBID              | INTEGER      | Database ID that created the key; displays as -1 if in Nomount state. |
| CREATOR_INSTANCE_NAME     | VARCHAR(256) | Instance name of the instance that created the key, corresponding to the host hostname |
| CREATOR_INSTANCE_NUMBER   | INTEGER      | Instance number of the instance that created the key, always 0 in standalone or distributed setups, instance ID in clustered mode. |
| CREATOR_INSTANCE_SERIAL   | INTEGER      | Sequence number of the instance that created the key; currently has no actual meaning, always -1. |
| ACTIVATING_DBNAME         | VARCHAR(68)  | Database that activated the key; displays as null if not activated |
| ACTIVATING_DBID           | INTEGER      | Database ID that activated the key; displays as null if not activated |
| ACTIVATING_INSTANCE_NAME  | VARCHAR(256) | Instance name of the instance that activated the key; displays as null if not activated |
| ACTIVATING_INSTANCE_NUMBER | INTEGER      | Instance number of the instance that activated the key; displays as null if not activated |
| ACTIVATING_INSTANCE_SERIAL | INTEGER      | Sequence number of the instance that activated the key; displays as null if not activated |