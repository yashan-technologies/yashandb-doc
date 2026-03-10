This view displays information about all keywords for both standalone and distributed systems.

|Field |Type |Description |
|-----------| ----------- | ------------------------------------------------------------ |
| GROUP_ID       | NUMBER       | Group ID                                                      |
| GROUP_NODE_ID  | NUMBER       | Node ID within the group                                      |
| INST_ID        | NUMBER       | Instance ID                                                   |
| KEYWORD        | VARCHAR(64)  | Keyword name                                                 |
| LENGTH         | INTEGER      | Keyword length                                               |
| RESERVED       | VARCHAR(1)   | Whether the keyword is a reserved word, Y is yes, N is no    |
| RES_TYPE       | VARCHAR(1)   | Whether the keyword is a reserved type name, Y is yes, N is no |
| RES_ATTR       | VARCHAR(1)   | Whether the keyword is a reserved attribute name, Y is yes, N is no |
| RES_SEMI       | VARCHAR(1)   | Whether the keyword is reserved in a specific context, such as DML or PL, Y is yes, N is no |
| DUPLICATE      | VARCHAR(1)   | Whether the keyword is a duplicate or copy of another keyword, Y is yes, N is no |