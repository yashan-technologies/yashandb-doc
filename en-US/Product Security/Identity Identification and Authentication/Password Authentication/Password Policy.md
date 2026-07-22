The password policy is used during the password authentication process, allowing the system to assess user password behavior and attributes according to the rules defined by the policy and take corresponding actions, including:

- When setting or modifying a password, the password complexity (strength) must be validated to meet certain requirements.

- To prevent malicious attackers from continuously attempting to guess passwords, a user will be locked for a certain period when consecutive login failures due to incorrect passwords reach a threshold.

- Long-term use of the same password can affect database security; therefore, the system imposes limits on the password's life length.

- When the system detects that a password has been used for more than the allowed number of days during user login, it will apply an additional grace period and print an alert log. After the grace period, the password will be completely expired, and the client will prompt the user to enter a new password. The user can immediately update the password.

- To prevent users from using the same password for a long time, users must also meet password reuse restrictions when setting a new password, which disallows reusing passwords that have been used in the recent time frame and those within the most recent change counts.

<span id="password_complexity" name="password_complexity"></span>

## Password Strength Control

When setting or modifying a password using the *yaspwd* or *yasboot* tools, YashanDB enables password strength control by default, requiring the password to meet the following criteria:

- Password length must be between 8 and 64 characters.

- The password must contain numbers, letters, and special characters.

- The password must not contain the corresponding user name.

When setting or modifying a password using an SQL statement, the password strength control functionality must be enabled first (controlled by the hidden parameter _CHECK_PASSWORD_COMPLEXITY; please contact our technical support for assistance), after which the password must meet the following requirements:

- The default password length requirement is between 8 and 64 characters (the minimum password length can be adjusted using the hidden parameter _PASSWORD_MIN_LENGTH).

- The password must contain numbers, uppercase letters, lowercase letters, and special characters.

<span id="password_policy" name="password_policy"></span>

## Password Usage Policy

### Policy Overview

The Profile serves as the carrier for the password usage policy, defining specific requirements for password usage by configuring a series of parameters. By associating PROFILE with target users, the corresponding password policy can be applied. To adjust the password usage policy, simply update the PROFILE, and it will take effect immediately for all associated users.



The relevant parameters are shown in the following table:

::: tabs
== When separation of duties is disabled

|Parameter |Corresponding Policy |Value Range | Default Value |
| --------------------- | ----------------------------|-------------------------|------- |
| FAILED_LOGIN_ATTEMPTS   | Maximum consecutive failed login attempts | An integer in the range (0, 2147483647) |  10  |
| FAILED_LOGIN_INTERVAL    | Password retry interval (unit: seconds), i.e., the waiting time required before retrying after a failed password attempt | UNLIMITED or an integer in the range [1,2147483646] |UNLIMITED<br>i.e., no limit on wait interval |
| PASSWORD_LOCK_TIME      | Number of days the user is locked after exceeding the incorrect attempts | (0,24855] | 1    |
| PASSWORD_LIFE_TIME      | Password lifecycle, i.e., the number of days before a password needs to be updated        | UNLIMITED or (0,24855]  | UNLIMITED, .e., no limit on days |
| PASSWORD_GRACE_TIME     | Password grace period, i.e., the number of days a password can still be used after expiration | UNLIMITED or [0,24855]  | UNLIMITED<br>i.e., no limit on grace period |
| PASSWORD_REUSE_TIME     | Number of days a password cannot be reused<br>If set to X, the new password cannot be one used in the last X days | UNLIMITED or (0,24855]      | UNLIMITED<br>i.e., no limit on reuse   |
| PASSWORD_REUSE_MAX      | Number of necessary changes before a password can be reused<br>If set to N, the new password cannot be one used in the last N changes |  UNLIMITED or an integer in the range [1,2147483646] | UNLIMITED<br>i.e., no limit on reuse   |

== When separation of duties is enabled

|Parameter |Corresponding Policy |Value Range | Default Value |
| --------------------- | ----------------------------|-------------------------|------- |
| FAILED_LOGIN_ATTEMPTS   | Maximum consecutive failed login attempts | An integer in the range (0,5] |  5  |
| FAILED_LOGIN_INTERVAL    | Password retry interval (unit: seconds), i.e., the waiting time required before retrying after a failed password attempt | UNLIMITED or an integer in the range [1,2147483646] |UNLIMITED<br>i.e., no limit on wait interval |
| PASSWORD_LOCK_TIME      | Number of days the user is locked after exceeding the incorrect attempts | (0,24855] | 1    |
| PASSWORD_LIFE_TIME      | Password lifecycle, i.e., the number of days before a password needs to be updated        | (0,7] | 7 |
| PASSWORD_GRACE_TIME     | Password grace period, i.e., the number of days a password can still be used after expiration | 0  | 0 |
| PASSWORD_REUSE_TIME     | Number of days a password cannot be reused<br>If set to X, the new password cannot be one used in the last X days | UNLIMITED or (0,24855]      | UNLIMITED<br>i.e., no limit on reuse   |
| PASSWORD_REUSE_MAX      | Number of necessary changes before a password can be reused<br>If set to N, the new password cannot be one used in the last N changes |  UNLIMITED or an integer in the range [1,2147483646] | UNLIMITED<br>i.e., no limit on reuse   |

> **Note**:
>
> After enabling separation of duties,  the custom profiles created previously are not affected. However, some values of the default profile will be automatically adjusted to comply with separation of duties requirements. When separation of duties is later disabled, these PROFILE values will not be reset to their original state.

:::

- FAILED_LOGIN_ATTEMPTS, FAILED_LOGIN_INTERVAL, and PASSWORD_LOCK_TIME work together to achieve the effect of "waiting X seconds before retrying after a password error, locking the user for Z days after Y consecutive retries (unless the user is manually [unlocked](../Managing Users.md#alteruser))."

- PASSWORD_REUSE_TIME and PASSWORD_REUSE_MAX work together to achieve the effect of "the new password cannot be the same as a password used in the last N days and the last M times." If one parameter is set to a specific value while the other is set to UNLIMITED, the user can never use a repeated password.



YashanDB has a default profile pre-set (named DEFAULT), with parameters related to password usage policy set to default values. All newly created users are by default associated with the default PROFILE unless otherwise specified, being governed by the default password policy.

The DBA_PROFILES view will present information about all profiles in the current database.

### Creating a Password Usage Policy

#### Prerequisites

The user executing the PROFILE creation operation must possess the CREATE PROFILE privilege. Please refer to [Privilege Granting and Revocation](../../Data Access Control/Privilege and Role Management/Permission Granting and Revocation) for authorization details.

#### Operation Steps

1. Log in to the database using a user with the corresponding privileges.

    In a CDB, creating common profiles requires connecting to the CDB root, while creating local profiles requires connecting to the target PDB.

2. Execute the [CREATE PROFILE](../../../Development Guide/SQL Reference Manual/SQL Statements/CREATE PROFILE) statement to create a PROFILE.

    ```sql
    CREATE PROFILE prof_pswd LIMIT FAILED_LOGIN_ATTEMPTS 5 PASSWORD_LIFE_TIME 180;
    ```

### Applying a Password Usage Policy

When [creating a user](../Managing Users) or [managing user attribute information](../Managing Users), a user can associate an existing PROFILE.
```sql
-- Create a user and associate a specified profile
CREATE USER sales2 IDENTIFIED BY password PROFILE prof_pswd;

-- Modify the user's profile, specifying an existing profile 
ALTER USER sales1 PROFILE prof_pswd;
```

### Modifying a Password Usage Policy

#### Prerequisites

The user executing the PROFILE modification operation must possess the ALTER PROFILE privilege. Please refer to [Privilege Granting and Revocation](../../Data Access Control/Privilege and Role Management/Permission Granting and Revocation) for authorization details.

#### Operation Steps

1. Log in to the database using a user with the appropriate privilege.

2. Execute the [ALTER PROFILE](../../../Development Guide/SQL Reference Manual/SQL Statements/ALTER PROFILE) statement to modify the PROFILE.

    ```sql
    ALTER PROFILE prof_pswd LIMIT FAILED_LOGIN_ATTEMPTS 3 PASSWORD_LIFE_TIME 200;
    ```

    After modification, the new password policy configuration values take immediate effect for all associated users.

### Deleting a Password Usage Policy

If a custom-created PROFILE is no longer needed, it can be deleted; the built-in default PROFILE (named DEFAULT) cannot be deleted.

For PROFILES associated with users, the CASCADE keyword must be used to delete them, which will automatically associate the corresponding users with the default PROFILE.

#### Prerequisites

The user executing the PROFILE deletion operation must possess the DROP PROFILE privilege. Please refer to [Privilege Granting and Revocation](../../Data Access Control/Privilege and Role Management/Permission Granting and Revocation) for authorization details.

#### Operation Steps

1. Log in to YashanDB using a user with the appropriate privilege.

2. Execute the [DROP PROFILE](../../../Development Guide/SQL Reference Manual/SQL Statements/DROP PROFILE) statement to delete any unnecessary PROFILE.

    ```sql
    DROP PROFILE prof_pswd CASCADE;
    ```
