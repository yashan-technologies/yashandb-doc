```ebnf+diagram
char_to_label::= CHAR_TO_LABEL  "(" policy_name "," label_string ")" 
```

The CHAR_TO_LABEL function returns the label value associated with the LBAC security policy policy_name and the label content label_string. The content of label_string must meet the label content requirements.

If the label content already exists, it directly returns the corresponding label value; if the label content does not exist, it will automatically create the label before returning the corresponding label value.

This function is only applicable to HEAP tables in Standalone Deployment.

Using this function may require calling built-in advanced packages such as [YLS_ENFORCEMENT](../../All Manuals/Development Guide/PL参考手册/内置高级包/YLS_ENFORCEMENT), [SA_SYSDBA](../../All Manuals/Development Guide/PL参考手册/内置高级包/SA_SYSDBA), [SA_COMPONENTS](../../All Manuals/Development Guide/PL参考手册/内置高级包/SA_COMPONENTS), and [SA_LABEL_ADMIN](../../All Manuals/Development Guide/PL参考手册/内置高级包/SA_LABEL_ADMIN). The related privilege requirements and usage constraints are also subject to the respective built-in advanced packages.

**policy_name**

The name of the LBAC security policy. The policy name is case-insensitive and can be queried from the [DBA_SA_POLICIES](../../All Manuals/Reference Manual/System Views/DBA Views/DBA_SA_POLICIES) view.

**label_string**

The label content, which must meet the label format requirements and the corresponding components must exist. The label content is case-insensitive.
- Refer to [SA_LABEL_ADMIN](../../All Manuals/Development Guide/PL参考手册/内置高级包/SA_LABEL_ADMIN) for label format requirements.
- Refer to [DBA_SA_LEVELS](../../All Manuals/参考手册/系统视图/DBA视图/DBA_SA_LEVELS) for level names under this policy.
- Refer to [DBA_SA_COMPARTMENTS](../../All Manuals/参考手册/系统视图/DBA视图/DBA_SA_COMPARTMENTS) for compartment names under this policy.
- Refer to [DBA_SA_LABELS](../../All Manuals/参考手册/系统视图/DBA视图/DBA_SA_LABELS) for existing labels under this policy.

***Example*** for Standalone Deployment Heap tables

```sql
-- Enable LBAC
CALL YLS_ENFORCEMENT.ENABLE_YLS;
-- Create LBAC security policy
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
