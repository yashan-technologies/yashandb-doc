The SA_LABEL_ADMIN package provides a set of built-in procedures for creating, modifying, and deleting labels.

- This advanced package is not suitable for ISC Distributed Cluster Deployment.

- Users executing this advanced package must have the LBAC_DBA role.

## CREATE\_LABEL

```plsql
SA_LABEL_ADMIN.CREATE_LABEL (
    policy_name IN VARCHAR,
    label_tag IN BINARY_INTEGER,
    label_value IN VARCHAR,
    data_label IN BOOLEAN DEFAULT TRUE);
```
This stored procedure is used to create security labels for LBAC security policies. The created labels occupy a certain amount of memory. If a large number of labels are created, it is recommended to modify the corresponding POOL configuration parameters to preallocate more memory space.

|Parameter |Description |
| :--- |:-----------------------------|
| policy_name | The name of the LBAC security policy to which the label belongs |
| label_tag   | Label value <br/> * For manual creation, the value range is [1,99999999] <br/> * For automatic creation, the value range is [1000000000,4000000000] |
| label_value | The content of the label                             |
| data_label  | Indicates whether it is a data label, default value TRUE |

Label format:

```txt
Level Name + ':' + [Range Name, ...]
```

***Example*** for Standalone Deployment and YAC Deployment

```plsql
BEGIN
    SA_LABEL_ADMIN.CREATE_LABEL ('lbac_policy', 1001, 'GENERAL:MNG');
END;
/

```

## DROP\_LABEL

```plsql
SA_LABEL_ADMIN.DROP_LABEL (
    policy_name IN VARCHAR,
    label_tag IN BINARY_INTEGER);
```
This stored procedure is used to delete labels from LBAC security policies.

|Parameter |Description |
| :--- | :---- |
| policy_name | The name of the LBAC security policy            |
| label_tag   | Label value                                     |

***Example*** for Standalone Deployment and YAC Deployment

```plsql
BEGIN
  SA_LABEL_ADMIN.DROP_LABEL ('lbac_policy', 1001);
END;
/

```
