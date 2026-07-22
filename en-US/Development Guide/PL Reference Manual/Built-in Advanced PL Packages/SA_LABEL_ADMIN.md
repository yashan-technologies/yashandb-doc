The SA_LABEL_ADMIN package provides a set of built-in procedures for creating, modifying, and deleting labels.

- This advanced package is not suitable for ISC Distributed Cluster Deployment.

- Users executing this advanced package must have the LBAC_DBA role.

## CREATE_LABEL

```plsql
SA_LABEL_ADMIN.CREATE_LABEL (
    policy_name IN VARCHAR,
    label_tag IN BINARY_INTEGER,
    label_value IN VARCHAR,
    data_label IN BOOLEAN DEFAULT TRUE);
```
This stored procedure is used to create a label for an existing LBAC label policy.

> **Note**:
>
>Labels will occupy a certain amount of [Session Stack Memory and Heap Memory](../../../Product Concepts/Instance Architecture/Database Memory). If a large number of labels need to be created, it is recommended to reasonably configure the values of the corresponding memory parameters WORK_AREA_POOL_SIZE, WORK_AREA_STACK_SIZE, and WORK_AREA_HEAP_SIZE to pre - allocate more memory space.

|Parameter |Description |
| :--- |:-----------------------------|
| policy_name        | The name of the LBAC label policy to be operated on, which can be obtained via the `POLICY_NAME` column in the [DBA_SA_POLICIES](../../../Reference Manual/System Views/DBA Views/DBA_SA_POLICIES) view |
| label_tag   | The tag number of the label, must be globally unique and in the range [1, 99999999]. <br />If labels are automatically created through the [CHAR_TO_LABEL](../../SQL Reference Manual/Built-in Functions/CHAR_TO_LABEL) function, the label identification value range is [1000000000, 4000000000]. |
| label_value | The content of the label, composed of the short names of levels and compartments <br />Multiple short names of the same component are separated by commas (`,`), while short names of different components  are separated by colons (`:`). The format is: `level1_shortname,…,levelN_shortname:compartment1_shortname,…,compartmentm_shortname` |
| data_label  | Indicates whether it is a data label, default value TRUE |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
BEGIN
    SA_LABEL_ADMIN.CREATE_LABEL ('lbac_policy', 1001, 'GENERAL:MNG');
END;
/

PL/SQL Succeed.
```

## DROP_LABEL

```plsql
SA_LABEL_ADMIN.DROP_LABEL (
    policy_name IN VARCHAR,
    label_tag IN BINARY_INTEGER);
```
This stored procedure is used to delete a label from an existing LBAC label policy.

> **Caution**:
>
> Before deleting a label, ensure that the target label is not used by any data rows (you can view which tables the target policy has been applied to through the [DBA_SA_TABLE_POLICIES](../../../Reference Manual/System Views/DBA Views/DBA_SA_TABLE_POLICIES) view, and then check whether the target label value exists in the policy column values of such tables). Otherwise, after the label is deleted, the corresponding data rows will only allow read/write operations by the sys user.

|Parameter |Description |
| :--- | :---- |
| policy_name        | The name of the LBAC label policy to be operated on, which can be obtained via the `POLICY_NAME` column in the [DBA_SA_POLICIES](../../../Reference Manual/System Views/DBA Views/DBA_SA_POLICIES) view |
| label_tag          | The tag number of the label to be deleted within the target policy, which can be obtained via the `LABEL_TAG` column in the [DBA_SA_LABELS](../../../Reference Manual/System Views/DBA Views/DBA_SA_LABELS) view |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
BEGIN
  SA_LABEL_ADMIN.DROP_LABEL ('lbac_policy', 1001);
END;
/

PL/SQL Succeed.
```
