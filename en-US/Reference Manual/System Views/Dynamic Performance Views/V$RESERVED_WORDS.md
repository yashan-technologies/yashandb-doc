This view displays information about all keywords for both Standalone Deployment and distributed systems.

|Field |Type |Description |
|-----------| ----------- | ------------------------------------------------------------ |
| KEYWORD    | VARCHAR(64) | Keyword name                                                |
| LENGTH     | INTEGER     | Keyword length                                              |
| RESERVED   | VARCHAR(1)  | Whether the keyword is a reserved word, Y for yes, N for no |
| RES_TYPE   | VARCHAR(1)  | Whether the keyword is a reserved type name, Y for yes, N for no |
| RES_ATTR   | VARCHAR(1)  | Whether the keyword is a reserved attribute name, Y for yes, N for no |
| RES_SEMI   | VARCHAR(1)  | Whether the keyword is reserved in specific contexts, such as in DML or PL, Y for yes, N for no |
| DUPLICATE  | VARCHAR(1)  | Whether the keyword is a duplicate or copy of another keyword, Y for yes, N for no |