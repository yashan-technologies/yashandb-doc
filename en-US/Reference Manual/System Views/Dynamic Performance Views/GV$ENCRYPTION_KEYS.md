This view displays the description attributes of the master key.

In ISC Distributed Cluster Deployment, this view is always empty.

|Field |Type |Description |
|--------------------------|------------ |-------|
| GROUP_ID                  | NUMBER       | Group ID                                |
| GROUP_NODE_ID             | NUMBER       | Node ID within the group                |
| INST_ID                   | NUMBER       | Instance ID                             |
| KEY_ID                    | VARCHAR(78)  | Master key identifier                   |
| TAG                       | VARCHAR(4000)| User-defined information related to the master key |
| CREATION_TIME             | TIMESTAMP    | Master key creation time                |
| ACTIVATION_TIME           | TIMESTAMP    | Time when the master key was put into use |
| CREATOR                   | VARCHAR(68)  | User who created the master key         |
| CREATOR_ID                | INTEGER      | User ID of the creator of the master key |
| USER#                     | VARCHAR(68)  | User who activated the master key; displays as null if not activated |
| USER_ID                   | INTEGER      | User ID who activated the master key; displays as null if not activated |
| KEY_USE                   | VARCHAR(64)  | Indicates the scenario for which the master key is used, e.g., 'TDE' |
| KEYSTORE_TYPE             | INTEGER      | Indicates the location of the master key:<br/> * 0: Master key location is unknown.<br/> * 1: Master key is managed by software. |
| ORIGIN                    | INTEGER      | Indicates the origin of the master key:<br/> * 0: Master key was created locally in this database.<br/> * 1: Master key was imported from another database. |
| BACKED_UP                 | INTEGER      | Indicates whether the key has been backed up |
| CREATOR_DBNAME            | VARCHAR(68)  | Database that created the key; empty string if created in Nomount state |
| CREATOR_DBID              | INTEGER      | Database ID that created the key; -1 if in Nomount state |
| CREATOR_INSTANCE_NAME     | VARCHAR(256) | Instance name of the instance that created the key, as the hostname |
| CREATOR_INSTANCE_NUMBER   | INTEGER      | Instance number of the instance that created the key; always 0 for standalone or distributed deployment, instance ID in cluster mode |
| CREATOR_INSTANCE_SERIAL   | INTEGER      | Sequence number of the instance that created the key; currently has no actual meaning and is always -1. |
| ACTIVATING_DBNAME         | VARCHAR(68)  | Database that activated the key; displays as null if not activated |
| ACTIVATING_DBID           | INTEGER      | Database ID that activated the key; displays as null if not activated |
| ACTIVATING_INSTANCE_NAME  | VARCHAR(256) | Instance name of the instance that activated the key; displays as null if not activated |
| ACTIVATING_INSTANCE_NUMBER | INTEGER      | Instance number of the instance that activated the key; displays as null if not activated |
| ACTIVATING_INSTANCE_SERIAL | INTEGER      | Sequence number of the instance that activated the key; displays as null if not activated |