This view shows the mapping relationship between users and resource consumption groups in the database.

|Field |Type |Description |
| --- | --- | --- |
| ATTRIBUTE       | VARCHAR(64)| Attribute, currently only supports `USER`  |
| VALUE           | VARCHAR(64)| Attribute value, records the username when the attribute is `USER` |
| CONSUMER_GROUP  | VARCHAR(64)| The name of the resource consumption group mapped to the user |
| STATUS          | VARCHAR(1) | Used for compatibility only, currently fixed to `NULL` |