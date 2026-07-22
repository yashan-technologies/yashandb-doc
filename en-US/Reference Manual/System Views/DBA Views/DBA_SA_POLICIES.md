This view displays the information of all LBAC label policies.

|Field |Type |Description |
| --- | --- | --- |
| POLICY_NAME       | VARCHAR(64)   | The name of the LBAC label policy                            |
| COLUMN_NAME     | VARCHAR(64)  | The name of the label column<br />When this policy is applied to the target table, the system will automatically add a column (named using this parameter value) to the target table to record the label for each row |
| STATUS          | VARCHAR(8)   | The status of the LBAC label policy, which is currently always ENABLE |
| POLICY_OPTIONS   | VARCHAR(256) | The mandatory control option(s) of the LBAC label policy |
| POLICY_SUBSCRIBED | VARCHAR(5)  | Reserved parameter                                   |