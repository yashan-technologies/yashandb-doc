This view displays all hint information corresponding to OUTLINE.

|Field |Type |Description |
| --- | --- | --- |
| NAME      | VARCHAR(64) | Object name                  |
| OWNER     | VARCHAR(64) | Object owner's username      |
| NODE      | SMALLINT    | ID of the query or subquery where the hint is applied, the top-level query is identified as 1 |
| STAGE     | SMALLINT    | Stage of the hint application |
| JOIN_POS  | SMALLINT    | Position of the table in the join order |
| HINT      | CLOB        | Hint content                 |