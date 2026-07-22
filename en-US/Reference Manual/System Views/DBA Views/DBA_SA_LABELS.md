This view displays all label information of all LBAC label policies.

|Field |Type |Description |
| --- | --- |---------------------------------------------------------------------------------------------|
| POLICY_NAME | VARCHAR(64)  | The name of the LBAC label policy                                                    |
| LABEL       | VARCHAR(4000)| The content of the label                                                            |
| LABEL_TAG   | BIGINT       | The tag number of the label <br />* When the value is within [1,99999999], it indicates that the label was manually created by calling [SA_LABEL_ADMIN.CREATE_LABEL](../../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/SA_LABEL_ADMIN) <br />* When the value is within [1000000000,4000000000], it indicates that the label was automatically created by executing the [CHAR_TO_LABEL](../../../Development Guide/SQL Reference Manual/Built-in Functions/CHAR_TO_LABEL) function   |
| LABEL_TYPE  | VARCHAR(15)  | The type of the label<br>\*   USER LABEL: User label, data with this label has a user sensitivity level<br>\*   USER/DATA LABEL: User/data label, data with this label has a row sensitivity level |