This view displays audit policy information.

|Field |Type |Description |
| --- | --- |-----------------------------------------------------------------------------------------|
| POLICY_NAME        | VARCHAR(64)  | Audit policy name                                                                                   |
| AUDIT_CONDITION    | VARCHAR(4000)| Conditions related to the audit policy                                                              |
| CONDITION_EVAL_OPT | VARCHAR(9)   | Frequency of executing condition judgment for the audit policy<br>\* STATEMENT: statement level<br>\* SESSION: session level<br>\* INSTANCE: instance level<br>\* NONE: not specified |
| AUDIT_OPTION       | VARCHAR(64)  | Audit item                                                                                          |
| AUDIT_OPTION_TYPE  | VARCHAR(16)  | Type of audit item<br>\* STANDARD ACTION: standard action audit<br>\* OBJECT ACTION: object action audit                       |
| OBJECT_SCHEMA      | VARCHAR(64)  | Object owner                                                                                         |
| OBJECT_NAME        | VARCHAR(64)  | Object name                                                                                         |
| OBJECT_TYPE        | VARCHAR(18)  | Object type                                                                                         |
| AUDIT_ONLY_TOPLEVEL| VARCHAR(3)   | "YES" means the audit policy is defined to only audit top-level SQL statements <br>"NO" means both top-level SQL and recursive SQL statements are audited                                   |