```ebnf
char_to_label = CHAR_TO_LABEL  "(" policy_name "," label_string ")" .
```

The CHAR_TO_LABEL function is used to retrieve the label value corresponding to the specified label content within an LBAC label policy.

If the specified label content already exists, the corresponding label value will be returned directly. If the specified label content does not exist, a new label will be automatically created first, and then the corresponding label value will be returned (the range of automatically created label values is [1,000,000,000, 4,000,000,000]).

This function is only applicable to HEAP tables.

Using this function may require calling built-in advanced packages such as [YLS_ENFORCEMENT](../../PL Reference Manual/Built-in Advanced PL Packages/YLS_ENFORCEMENT), [SA_SYSDBA](../../PL Reference Manual/Built-in Advanced PL Packages/SA_SYSDBA), [SA_COMPONENTS](../../PL Reference Manual/Built-in Advanced PL Packages/SA_COMPONENTS), and [SA_LABEL_ADMIN](../../PL Reference Manual/Built-in Advanced PL Packages/SA_LABEL_ADMIN). The related privilege requirements and usage constraints are also subject to the respective built-in advanced packages.

**policy\_name**

The name of the LBAC label policy. The policy name is case-insensitive and can be queried from the [DBA_SA_POLICIES](../../../Reference Manual/System Views/DBA Views/DBA_SA_POLICIES) view.

**label\_string**

The label content, which must meet the label format requirements and the corresponding components must exist. The label content is case-insensitive.

- Refer to [SA_LABEL_ADMIN](../../PL Reference Manual/Built-in Advanced PL Packages/SA_LABEL_ADMIN) for label format requirements.
- Refer to [DBA_SA_LEVELS](../../../Reference Manual/System Views/DBA Views/DBA_SA_LEVELS) for level names under this policy.
- Refer to [DBA_SA_COMPARTMENTS](../../../Reference Manual/System Views/DBA Views/DBA_SA_COMPARTMENTS) for compartment names under this policy.
- Refer to [DBA_SA_LABELS](../../../Reference Manual/System Views/DBA Views/DBA_SA_LABELS) for existing labels under this policy.

***Example*** for Heap tables

```sql
-- Enable LBAC
CALL YLS_ENFORCEMENT.ENABLE_YLS;
-- Create LBAC label policy
CALL SA_SYSDBA.CREATE_POLICY('lbac_policy', 'policy_column');
-- Create LBAC levels
CALL SA_COMPONENTS.CREATE_LEVEL( 'lbac_policy', 10, 'GENERAL', 'GENERAL');
CALL SA_COMPONENTS.CREATE_LEVEL ( 'lbac_policy', 20, 'SENS', 'SENS');
CALL SA_COMPONENTS.CREATE_LEVEL ( 'lbac_policy', 30, 'SECRET', 'SECRET');
-- Create LBAC compartments
CALL SA_COMPONENTS.CREATE_COMPARTMENT ( 'lbac_policy', 10, 'MNG', 'MNG');
CALL SA_COMPONENTS.CREATE_COMPARTMENT ( 'lbac_policy', 20, 'QA', 'QA');
CALL SA_COMPONENTS.CREATE_COMPARTMENT ( 'lbac_policy', 30, 'RD', 'RD');
CALL SA_COMPONENTS.CREATE_COMPARTMENT ( 'lbac_policy', 40, 'TEST', 'TEST');
-- Create label
CALL SA_LABEL_ADMIN.CREATE_LABEL ('lbac_policy', 1001, 'GENERAL:MNG');

SELECT CHAR_TO_LABEL('lbac_policy', 'general:mng') label_tag FROM DUAL;
            LABEL_TAG 
--------------------- 
                 1001

-- Drop policy
CALL SA_SYSDBA.DROP_POLICY('lbac_policy', TRUE);
-- Disable LBAC
CALL YLS_ENFORCEMENT.DISABLE_YLS;
```
