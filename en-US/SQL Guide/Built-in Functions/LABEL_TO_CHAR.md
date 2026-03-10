```ebnf+diagram
label_string::= LABEL_TO_CHAR  "(" label ")" 
```

The LABEL_TO_CHAR function returns the label content associated with the LBAC label value specified by the label.

This function is only applicable to HEAP tables in Standalone Deployment.

Using this function may require invoking built-in advanced packages such as [YLS_ENFORCEMENT](../../All Manuals/Development Guide/PL Reference Manual/Built-in Advanced PL Packages/YLS_ENFORCEMENT), [SA_SYSDBA](../../All Manuals/Development Guide/PL Reference Manual/Built-in Advanced PL Packages/SA_SYSDBA), [SA_COMPONENTS](../../All Manuals/Development Guide/PL Reference Manual/Built-in Advanced PL Packages/SA_COMPONENTS), and [SA_LABEL_ADMIN](../../All Manuals/Development Guide/PL Reference Manual/Built-in Advanced PL Packages/SA_LABEL_ADMIN). The relevant privilege requirements and usage constraints will also be subject to the corresponding built-in advanced packages.

**label**

The LBAC label value can be obtained by querying the [DBA_SA_LABELS](../../All Manuals/Reference Manual/System Views/DBA Views/DBA_SA_LABELS) view. The parameter value is of type BIGINT. When the parameter is of type BINARY_FLOAT or BINARY_DOUBLE, it is rounded to the nearest whole number; when it is of other types, it is truncated.

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

SELECT LABEL_TO_CHAR(1001) label_string FROM DUAL;
LABEL_STRING                                                     
---------------------------------------------------------------- 
GENERAL:MNG

-- Drop policy
CALL SA_SYSDBA.DROP_POLICY('lbac_policy', TRUE);
-- Disable switch
CALL YLS_ENFORCEMENT.DISABLE_YLS;
```
