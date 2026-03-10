General Description
----

The DROP AUDIT POLICY is used to delete an audit policy.

Only users with the AUDIT_ADMIN audit administrator role or those with the AUDIT SYSTEM system privilege can delete an audit policy.

For enabled audit policies, direct deletion is not allowed. The NOAUDIT POLICY should be executed first before performing the deletion operation.

Statement Definition
----

**drop\_audit\_policy::=**

```ebnf+diagram
syntax::= DROP AUDIT POLICY policy_name
```

### 1. policy\_name

The name of the audit policy to be deleted.

***Example***

```sql
DROP AUDIT POLICY up1;
DROP AUDIT POLICY up2;
DROP AUDIT POLICY up3;
DROP AUDIT POLICY up4;
DROP AUDIT POLICY audit_role_a;
```
