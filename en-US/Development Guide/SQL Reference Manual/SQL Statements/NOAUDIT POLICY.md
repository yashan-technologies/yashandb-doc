General Description  
----

The NOAUDIT POLICY disables the audit policy and cancels the audit of the audit items corresponding to the audit policy.  

Only users with the AUDIT_ADMIN audit administrator role or users with the AUDIT SYSTEM privilege can disable an audit policy.  

Statement Definition  
----

**noaudit\_policy::=**

```ebnf
= NOAUDIT POLICY policy_name [(BY user_name {',' user_name})] [WHENEVER [ NOT] SUCCESSFUL].
```

### policy\_name

The name of the audit policy to be disabled.  

### BY

Specifies the user whose operations are to be disabled; this user must be in the list of users specified when the audit policy was enabled.  

If this clause is not specified, the audit policy can only be disabled if it applies to all users or if the policy is an exception.  

### WHENEVER [NOT] SUCCESSFUL

Cancels the audit records of the audit policy when the database operation is executed successfully or fails.  

If this clause is not specified, it indicates that the audit policy is canceled for both successful and failed database operation audits.  

***Example***  

```sql
-- Disable the audit for the audit policy up1 corresponding to user sales  
NOAUDIT POLICY up1 BY sales;

-- Disable the audit for the audit policy up2 corresponding to user sales on failed database operations  
NOAUDIT POLICY up2 BY sales WHENEVER NOT SUCCESSFUL;
```
