The password policy is used during the password authentication process, allowing the system to assess user password behavior and attributes according to the rules defined by the policy and take corresponding actions, including:

- When setting or modifying a password, the password complexity (strength) must be validated to meet certain requirements.
- To prevent malicious attackers from continuously attempting to guess passwords, a user will be locked for a certain period when consecutive login failures due to incorrect passwords reach a threshold.
- Long-term use of the same password can affect database security; therefore, the system imposes limits on the password's life length.
- When the system detects that a password has been used for more than the allowed number of days during user login, it will apply an additional grace period and print an alert log. After the grace period, the password will be completely expired, and the client will prompt the user to enter a new password. The user can immediately update the password.
- To prevent users from using the same password for a long time, users must also meet password reuse restrictions when setting a new password, which disallows reusing passwords that have been used in the recent time frame and those within the most recent change counts.

<span id="password_complexity" name="password_complexity" class="yaslink"></span>

## Password Strength Control

When setting or modifying a password using the *yaspwd* or *yasboot* tools, YashanDB enables password strength control by default, requiring the password to meet the following criteria:

- Password length must be between 8 and 64 characters.
- The password must contain numbers, letters, and special characters.
- The password must not contain the corresponding user name.

When setting or modifying a password using an SQL statement, the password strength control functionality must be enabled first (controlled by the hidden parameter _CHECK_PASSWORD_COMPLEXITY; please contact our technical support for assistance), after which the password must meet the following requirements:

- The default password length requirement is between 8 and 64 characters (the minimum password length can be adjusted using the hidden parameter _PASSWORD_MIN_LENGTH).
- The password must contain numbers, uppercase letters, lowercase letters, and special characters.

<span id="password_policy" name="password_policy" class="yaslink"></span>

## Password Usage Policy

### Policy Overview

The Profile PROFILE serves as the carrier for the password usage policy, defining specific requirements for password usage by configuring a series of parameters. By associating PROFILE with target users, the corresponding password policy can be applied. To adjust the password usage policy, simply update the PROFILE, and it will take effect immediately for all associated users.

The relevant parameters are shown in the following table:

|Parameter |Corresponding Policy |Default Value |
| --------------------- | ------------------------------------------------------------ | ------------------- |
| FAILED_LOGIN_ATTEMPTS   | Number of incorrect attempts allowed before the account is locked | 10                     |
| PASSWORD_LOCK_TIME      | Number of days the user is locked after exceeding the incorrect attempts | 1                      |
| PASSWORD_LIFE_TIME      | Number of days a password can be used (life length)         | UNLIMITED<br>i.e., no limit on days |
| PASSWORD_GRACE_TIME     | Additional grace period in days after password expiration to use the original password | UNLIMITED<br>i.e., no limit on grace period |
| PASSWORD_REUSE_TIME     | Number of days a password cannot be reused<br>If set to X, the new password cannot be one used in the last X days | UNLIMITED no reuse   |
| PASSWORD_REUSE_MAX      | Number of necessary changes before a password can be reused<br>If set to N, the new password cannot be one used in the last N changes | UNLIMITED<br>i.e., no limit on reuse   |
| FAILED_LOGIN_INTERVAL    | Interval to wait before the next login attempt after an incorrect password | UNLIMITED<br>i.e., no limit on wait interval |

The PASSWORD_REUSE_TIME and PASSWORD_REUSE_MAX parameters must be set in relation to each other, as follows:

- If both parameters are assigned specific values, the user can only use a repeated password after both the PASSWORD_REUSE_TIME days and the PASSWORD_REUSE_MAX number of changes have been satisfied.
- If only one of the parameters is assigned a specific value and the other is kept at the default UNLIMITED, the user will never be able to use a repeated password.

YashanDB has a default PROFILE pre-set (named DEFAULT), with parameters related to password usage policy set to default values. All newly created users are by default associated with the default PROFILE unless otherwise specified, being governed by the default password policy.

The DBA_PROFILES view will present information about all profiles in the current database.

### Creating a Password Usage Policy

#### Prerequisites

The user executing the PROFILE creation operation must possess the CREATE PROFILE privilege. Please refer to [Privilege Granting and Revocation](../../Data Access Control/Privilege and Role Management (yashan Mode)/Permission Granting and Revocation) for authorization details.

#### Steps

1. Log in to YashanDB using a user with the appropriate privilege.

2. Execute the [CREATE PROFILE](../../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/CREATE PROFILE) statement to create a PROFILE.

    ```sql
    CREATE PROFILE prof_pswd LIMIT FAILED_LOGIN_ATTEMPTS 5 PASSWORD_LIFE_TIME 180;
    ```

### Applying a Password Usage Policy

When [creating a user](../User/Managing Users (yashan Mode)) or [managing user attribute information](../User/Managing Users (yashan Mode)), a user can associate an existing PROFILE.
```sql
-- Create a user and associate a specified profile
CREATE USER sales2 IDENTIFIED BY PASSWORD PROFILE prof_pswd;

-- Modify the user's profile, specifying an existing profile 
ALTER USER sales1 PROFILE prof_pswd;
```

### Modifying a Password Usage Policy

#### Prerequisites

The user executing the PROFILE modification operation must possess the ALTER PROFILE privilege. Please refer to [Privilege Granting and Revocation](../../Data Access Control/Privilege and Role Management (yashan Mode)/Permission Granting and Revocation) for authorization details.

#### Steps

1. Log in to YashanDB using a user with the appropriate privilege.

2. Execute the [ALTER PROFILE](../../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/ALTER PROFILE) statement to modify the PROFILE.

    ```sql
    ALTER PROFILE prof_pswd LIMIT FAILED_LOGIN_ATTEMPTS 3 PASSWORD_LIFE_TIME 200;
    ```

    After modification, the new password policy configuration values take immediate effect for all associated users.

### Deleting a Password Usage Policy

If a custom-created PROFILE is no longer needed, it can be deleted; the built-in default PROFILE (named DEFAULT) cannot be deleted.

For PROFILES associated with users, the CASCADE keyword must be used to delete them, which will automatically associate the corresponding users with the default PROFILE.

#### Prerequisites

The user executing the PROFILE deletion operation must possess the DROP PROFILE privilege. Please refer to [Privilege Granting and Revocation](../../Data Access Control/Privilege and Role Management (yashan Mode)/Permission Granting and Revocation) for authorization details.

#### Steps

1. Log in to YashanDB using a user with the appropriate privilege.

2. Execute the [DROP PROFILE](../../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/DROP PROFILE) statement to delete any unnecessary PROFILE.

    ```sql
    DROP PROFILE prof_pswd CASCADE;
    ```
