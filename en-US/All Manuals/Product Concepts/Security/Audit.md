Database auditing is a process of monitoring and recording database activities. Through database auditing, access, modification, and operational behaviors of the database can be tracked and recorded for traceability, troubleshooting, and compliance checks.

## Audit Administrator

The audit administrator (AUDIT_ADMIN role) can create and manage audit policies and view audit logs. To ensure segregation of duties, an additional AUDIT_VIEWER role has been established to allow viewing of audit logs.

Audit records are stored in physical tables. Users with AUDIT_ADMIN or AUDIT_VIEWER role privileges can view audit log information through the audit view UNIFIED_AUDIT_TRAIL.

## Audit Scope

YashanDB provides audit management in the following areas for users:

- Privilege Audit

    Privilege audit refers to the auditing of all system privileges in YashanDB. When an audit policy is enabled for a specific system privilege, any usage of that system privilege in SQL statements or other operations will be audited.

- Behavior Audit

    Behavior audit includes auditing of system operation behaviors and object operation behaviors:

    - System Operation Behaviors: Includes all system operation activities such as object creation/deletion, database creation/closing, commit/rollback, etc. The AUDITABLE_SYSTEM_ACTIONS view displays all current system operation behavior audit items. The term ALL indicates that all system operation behaviors are audited.
    - Object Operation Behaviors: Refers to operation behaviors on specific objects, such as SELECT/INSERT/UPDATE/DELETE operations on a table. The AUDITABLE_OBJECT_ACTIONS view displays all current object operation behavior audit items. The term ALL indicates that all operation behaviors on a particular object are audited.

- Role Audit

    YashanDB offers role audit functionality. When an audit policy is enabled for a specific role, all system privileges directly assigned to that role can be audited.

## Audit Switch

YashanDB controls the audit switch through the configuration parameter UNIFIED_AUDITING. When the audit switch is turned on, the system will execute the created and enabled audit policies.

## Audit Policies

Only users with the AUDIT_ADMIN audit administrator role or users with the AUDIT SYSTEM system privilege can execute the CREATE AUDIT POLICY statement to create audit policies.

Once an audit policy is created, it does not take effect until it is enabled using the AUDIT POLICY statement. After enabling, operations performed by users that fall under the audit item will trigger auditing.

After an audit policy is successfully created, its definition information can be viewed through the AUDIT_UNIFIED_POLICIES view.

## Audit Logs

Users can view audit logs through the audit view UNIFIED_AUDIT_TRAIL, retrieving and sorting detailed operation records by date, audit item type, object name, operation user, and more.

To avoid excessive disk space usage, it is recommended to clean up backed-up or retention-compliant audit data. The cleanup methods include:

- Setting Cleanup Time Points: Setting cleanup time points allows for more flexible specification of the audit data cleanup range.

- Manual Cleanup: Performing manual deletion of audit logs when necessary.

- Automatic Cleanup: Establishing automatic cleanup tasks for the system to periodically delete audit data.

## Asynchronous Auditing

Asynchronous auditing first writes audit data information into the audit data queue. When the audit data queue reaches a threshold (data refresh time interval, queue full), the data is then batch inserted into the audit records table.

The asynchronous audit switch is controlled by the AUDIT_QUEUE_WRITE parameter and is enabled by default.

Asynchronous auditing can reduce the impact on database performance. However, in certain abnormal situations (e.g., unexpected database crashes), it may lead to the loss of some audit data.