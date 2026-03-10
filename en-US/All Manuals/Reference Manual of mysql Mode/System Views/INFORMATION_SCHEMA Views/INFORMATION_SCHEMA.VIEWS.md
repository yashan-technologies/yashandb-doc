This view displays information about views.

|Field |Type |Description |
| --- | --- | --- |
| TABLE_CATALOG        | VARCHAR(3)  | The name of the catalog where the view is located |
| TABLE_SCHEMA         | VARCHAR(64) | The name of the schema where the view is located  |
| TABLE_NAME           | VARCHAR(64) | The name of the view               |
| VIEW_DEFINITION      | LONGTEXT    | The definition of the view         |
| CHECK_OPTION         | VARCHAR(4)  | The check option of the view<br>Syntax compatible only, no actual functional meaning.       |
| IS_UPDATABLE         | VARCHAR(2)  | Indicator of whether the view is updatable. <br>Reserved field, defaults to `NO`, has no practical meaning |
| DEFINER              | VARCHAR(64) | The creator of the view            |
| SECURITY_TYPE        | VARCHAR(7)  | The security type of the view      |
| CHARACTER_SET_CLIENT | VARCHAR(32) | The character set used when connecting from the client |
| COLLATION_CONNECTION  | VARCHAR(32) | The collation used when connecting from the client |