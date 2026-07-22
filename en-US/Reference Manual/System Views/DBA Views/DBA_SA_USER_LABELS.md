This view displays all users who have access to controlled data, along with their corresponding policies and label information.

|Field |Type |Description |
| --- | --- | --- |
| USER_NAME          | VARCHAR(64)   | The name of the user    |
| POLICY_NAME         | VARCHAR(64)   | The name of the LBAC label policy |
| MAX_READ_LABEL     | VARCHAR(4000) | Maximum read label content     |
| MAX_WRITE_LABEL    | VARCHAR(4000) | Maximum write label content    |
| MIN_WRITE_LABEL    | VARCHAR(4000) | Minimum write label content    |
| DEFAULT_READ_LABEL | VARCHAR(4000) | Default read label content     |
| DEFAULT_WRITE_LABEL| VARCHAR(4000) | Default write label content    |
| DEFAULT_ROW_LABEL  | VARCHAR(4000) | Default row label content      |