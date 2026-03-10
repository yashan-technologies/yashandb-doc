## Create Audit Policy

Up to 512 audit policies can be created. All audit policies can be viewed through the AUDIT_UNIFIED_POLICIES view.

A complete audit policy mainly includes:

- Audit items: the objects/behaviors being audited, which can be specified through privilege_audit_clause, action_audit_clause, or role_audit_clause.

- Audit trigger conditions: optional, which can be specified through when_clause or toplevel_clause.

## Prerequisites

- The user performing the audit policy creation must have the AUDIT_ADMIN audit administrator role or the AUDIT SYSTEM system privilege. For authorization operations, please refer to [privilege granting and revocation](../Data Access Control/Privilege and Role Management/Permission Granting and Revocation).

- To configure audit items related to object operational behavior, ensure that the target object already exists.

## Steps

1. Log in to YashanDB using a user with the appropriate privilege.

2. Execute the [CREATE AUDIT POLICY](../../Development Guide/SQL Reference Manual/SQL Statements/CREATE AUDIT POLICY) statement to create an audit policy.

    ```sql
    CREATE AUDIT POLICY audit01 ACTIONS 
    DROP TABLE,
    DELETE ON sales.area,
    INSERT ON sales.area,
    UPDATE ON sales.area,
    ALL ON sales.branches;
    -- Audit items include: DROP TABLE type statements, DELETE, INSERT, UPDATE operations on the area table, and all operations on the branches table.
    ```

    Even if the audit functionality is enabled, the audit policy will not take effect immediately after creation. It needs to be enabled to make the corresponding audit items effective.

<span id="audit" name="audit" class="yaslink"></span>

## Enable Audit Policy

The layered handling of creating and enabling audit policies allows users to manage audit policies online more clearly and conveniently. During the creation phase, users only need to consider which audit items and audit conditions to include (e.g., whether to audit statement/session/instance level, whether to audit recursive SQL statements, etc.). In the enabling phase, users can further set additional conditions for triggering such audit items, including who it applies to (e.g., only applicable to target users, applies to others except specified users), and when it becomes effective (e.g., audit only when the operation is successful to reduce invalid records).

Once audit functionality is enabled and audit policies are in effect, specific users executing actions matching the audit items and meeting the audit policy trigger conditions will trigger audits.

You can view all enabled audit policies through the AUDIT_UNIFIED_ENABLED_POLICIES view.

## Prerequisites

- The user performing the audit policy enabling operation must have the AUDIT_ADMIN audit administrator role or the AUDIT SYSTEM system privilege. For authorization operations, please refer to [privilege granting and revocation](../Data Access Control/Privilege and Role Management/Permission Granting and Revocation).

- You cannot enable the same audit policy multiple times.

## Steps

1. Log in to YashanDB using a user with the appropriate privilege.

2. Execute the [AUDIT POLICY](../../Development Guide/SQL Reference Manual/SQL Statements/AUDIT POLICY) statement to enable the audit policy.

    ```sql
    AUDIT POLICY audit01 BY sales WHENEVER SUCCESSFUL;
    -- Specific trigger conditions: the audit items apply only to user sales and audit only when the operation executes successfully.
    ```

## Modify Audit Policy

YashanDB supports online dynamic modification of audit policies. If the modification is successful for an audit policy in effect, the audit of database operations will change immediately.

Modifying an audit policy means that adjustments can be made to the audit items, audit conditions, etc., specified during the creation of the audit policy.

The enabled status of the audit policy and the additional conditions set during enabling cannot be adjusted through modification. To adjust the enabled status or additional conditions of an enabled policy, you can only choose to execute the [enable (AUDIT POLICY)](#audit) or [disable (NOAUDIT POLICY)](#noaudit) operation as needed.

## Prerequisites

- The user performing the audit policy modification operation must have the AUDIT_ADMIN audit administrator role or the AUDIT SYSTEM system privilege. For authorization operations, please refer to [privilege granting and revocation](../Data Access Control/Privilege and Role Management/Permission Granting and Revocation).

- To add audit items related to object operational behavior, ensure that the target object already exists.

## Steps

1. Log in to YashanDB using a user with the appropriate privilege.

2. Execute the [ALTER AUDIT POLICY](../../Development Guide/SQL Reference Manual/SQL Statements/ALTER AUDIT POLICY) statement to modify the audit policy.

    ```sql
    ALTER AUDIT POLICY audit01
    ADD PRIVILEGES DROP ANY TABLE 
    DROP ACTIONS ALL ON sales.branches;
    -- Add audit item: system privilege DROP ANY TABLE related operations; remove audit item: all operations on branches table.
    ```

<span id="noaudit" name="noaudit" class="yaslink"></span>

## Disable Audit Policy

Disabling the audit policy can cancel it for specific users, cancel it based on certain additional conditions (successful execution or not), or directly cancel the entire audit policy. Upon disabling, the corresponding audit will stop immediately.

## Prerequisites

The user performing the disable audit policy operation must have the AUDIT_ADMIN audit administrator role or the AUDIT SYSTEM system privilege. For authorization operations, please refer to [privilege granting and revocation](../Data Access Control/Privilege and Role Management/Permission Granting and Revocation).

## Steps

1. Log in to YashanDB using a user with the appropriate privilege.

2. Execute the [NOAUDIT POLICY](../../Development Guide/SQL Reference Manual/SQL Statements/NOAUDIT POLICY) statement to disable the audit policy.

    ```sql
    NOAUDIT POLICY audit01 BY sales;
    ```

## Delete Audit Policy

For audit policies that are no longer needed, they can be deleted after being disabled.

## Prerequisites

- The user performing the delete audit policy operation must have the AUDIT_ADMIN audit administrator role or the AUDIT SYSTEM system privilege. For authorization operations, please refer to [privilege granting and revocation](../Data Access Control/Privilege and Role Management/Permission Granting and Revocation).

- You cannot delete an audit policy that is currently active. You must first [disable it (NOAUDIT POLICY)](#noaudit).

## Steps

1. Log in to YashanDB using a user with the appropriate privilege.

2. Execute the [DROP AUDIT POLICY](../../Development Guide/SQL Reference Manual/SQL Statements/NOAUDIT POLICY) statement to delete the audit policy.

    ```sql
    DROP AUDIT POLICY audit01;
    ```
