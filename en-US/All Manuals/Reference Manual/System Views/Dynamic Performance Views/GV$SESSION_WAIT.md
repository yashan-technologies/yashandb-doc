This view displays information about the current session wait events.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID     | NUMBER    | Group ID                                |
| GROUP_NODE_ID| NUMBER    | Node ID within the group                |
| INST_ID      | NUMBER    | Instance ID                             |
| SID          | SMALLINT  | Session ID                              |
| WAIT_EVENT   | VARCHAR(32)| Wait event name, the value of this field refers to the description in the [Wait Events](../../Wait Events) document |