The DBMS_AWR_EXTRA package provides a built-in stored procedure to assist in the implementation of functionality for the [DBMS_AWR](DBMS_AWR) advanced package.

> **Caution**:
>
> - The DBMS_AWR_EXTRA advanced package can only be called through the CREATE_SNAPSHOT() subroutine of the [DBMS_AWR](DBMS_AWR) advanced package, and users are not allowed to call this advanced package directly.
>
> - Other usage constraints for the DBMS_AWR_EXTRA advanced package are the same as those for the DBMS_AWR advanced package.

## UPDATE\_SQLSTAT\_LAST\_SNAP

```plsql
DBMS_AWR_EXTRA.UPDATE_SQLSTAT_LAST_SNAP();
```

The UPDATE_SQLSTAT_LAST_SNAP subroutine is a stored procedure with no parameters, used to update the last snap value of each SQL's statistics in the plan cache to the current value of the corresponding statistics during snapshot creation.