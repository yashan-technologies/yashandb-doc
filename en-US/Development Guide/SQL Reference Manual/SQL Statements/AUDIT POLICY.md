General Description
----

The AUDIT POLICY enables the created audit policy. Only enabled audit policies will trigger audits when database operations match the audit conditions.

Only users with the AUDIT_ADMIN audit administrator role or users with the AUDIT SYSTEM system privilege can execute the enable operation for a specific audit policy.

After enabling an audit policy according to the AUDIT POLICY definition rules, the system will audit database operations when the configuration parameter UNIFIED_AUDITING is set to true. Users can view the corresponding audit records through the UNIFIED_AUDIT_TRAIL view.

Statement Definition
----

**audit\_policy::=**

```ebnf
= AUDIT POLICY policy_name [((BY | EXCEPT) user_name {',' user_name})] [WHENEVER [NOT] SUCCESSFUL].
```

### policy\_name

The name of the audit policy to be enabled.

### BY|EXCEPT

Specifies the operation users to be audited or excludes the operation users that do not need to be audited. Multiple users can be specified or excluded using `,` as a separator. This clause can be omitted, in which case all operation users will be audited by default.

### WHENEVER [NOT] SUCCESSFUL

Specifies that audit records will be made only when the database operation is successful or failed. If this clause is not used, it means that data operations meeting the audit conditions will be audited regardless of their success or failure.

***Example***

```sql
-- Enable audit policy, auditing user sales when the corresponding audit item executes successfully
AUDIT POLICY up1 BY sales WHENEVER SUCCESSFUL;

-- Enable audit policy, excluding user sales, auditing regardless of success or failure for corresponding audit items
AUDIT POLICY up2 EXCEPT sales;
```
