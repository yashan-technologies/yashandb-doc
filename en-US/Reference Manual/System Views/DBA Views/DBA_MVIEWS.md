This view displays information about all materialized views.

|Field |Type |Description |
| --- | --- | --- |
| OWNER             | VARCHAR(64) | Username of the materialized view  |
| MVIEW_NAME        | VARCHAR(64) | Name of the materialized view      |
| QUERY             | CLOB        | Subquery statement used to create the materialized view |
| QUERY_LEN         | INTEGER     | Length of the subquery statement used to create the materialized view |
| REFRESH_MODE      | VARCHAR(9)  | Refresh mode of the materialized view |
| REFRESH_METHOD    | VARCHAR(8)  | Type of refresh for the materialized view |
| REFRESH_START_DATE| VARCHAR(4000)| First refresh time of the materialized view |
| REFRESH_NEXT_DATE | VARCHAR(4000)| Refresh interval time of the materialized view |
| DDL_TIME          | DATE        | DDL SCN of the materialized view   |
| REWRITE_ENABLED    | VARCHAR(1)  | Whether query rewriting is allowed<br>*   Y<br>*   N |
| BUILD_MODE        | VARCHAR(9)  | Type of creation for the materialized view |
| JOB               | BIGINT      | Job ID of the materialized view     |