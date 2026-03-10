The YLS_ENFORCEMENT package provides a set of built-in subprograms to set the status of label-based row access control switches.

- This advanced package is not applicable to ISC Distributed Cluster Deployment.

- Users executing this advanced package must have the LBAC_DBA role.

## ENABLE\_YLS

```plsql
YLS_ENFORCEMENT.ENABLE_YLS();
```
This stored procedure is used to turn on the row access control switch.

***Example*** for Standalone Deployment and YAC Deployment

```plsql
BEGIN
  YLS_ENFORCEMENT.ENABLE_YLS();
END;
/

```

## DISABLE\_YLS

```plsql
YLS_ENFORCEMENT.DISABLE_YLS();
```
This stored procedure is used to turn off the row access control switch.

***Example*** for Standalone Deployment and YAC Deployment

```plsql
BEGIN
  YLS_ENFORCEMENT.DISABLE_YLS();
END;
/

```
