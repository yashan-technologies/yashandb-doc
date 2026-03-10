Generic Description
----

CREATE PROFILE is used to create a profile, which is a collection of resource limits imposed by the system on users. In YashanDB, profiles can define limitations related to [password usage policies](../../../Product Security/Identity Identification and Authentication/Password Authentication/Password Policy (yashan Mode).html#password_policy), [session resources](../../../Product Security/Resource Control/Session Resource Control), and [connection request IPs](../../../Product Security/Connection Management/IP Blacklist and Whitelist Management).

When executing this statement, the following points should be noted:

- The user must have the CREATE PROFILE privilege.
- The profile itself does not contain user information. The relationship between the user and the profile must be established using the [CREATE USER](CREATE USER) or [ALTER USER](ALTER USER) statements.

All profiles and their contents can be queried through the DBA_PROFILES view.

YashanDB has a default profile (named DEFAULT) preconfigured. When creating a new user using the default syntax, that user will be associated with the default profile and will be subject to its specified configuration limits.

In ISC Distributed Cluster Deployment, only [resource limits related to password policies](#pswd) are supported.

> **Note**: 
>
> - Parameter values support implicit type conversions and expression operations, as long as the result satisfies the rules.
> - If all parameter values are empty strings or the expression result is NULL, they are treated as 0 value.

Statement Definition
----

**create profile::=**

```ebnf+diagram
syntax::= CREATE PROFILE profile_name LIMIT (password_parameters | resource_parameters | tcp_ip_parameters) {" " (password_parameters | resource_parameters | tcp_ip_parameters)}
```

**[password_parameters](#pswd)::=**

```ebnf+diagram
syntax::= (password_parameter_name password_parameter_value)
  {" " (password_parameter_name password_parameter_value)}
```

**[resource_parameters](#res)::=**

```ebnf+diagram
syntax::= (resource_parameter_name resource_parameter_value)
  {" " (resource_parameter_name resource_parameter_value)}
```

**[tcp_ip_parameters](#ip)::=**

```ebnf+diagram
syntax::= (tcp_ip_parameter_name tcp_ip_parameter_value)
  {" " (tcp_ip_parameter_name tcp_ip_parameter_value)}
```

### 1. profile\_name

The profile name is mandatory and must comply with YashanDB's [object naming specifications](../Basic SQL Elements/Identifiers).

<span id="pswd" name="pswd" class="yaslink"></span>

### 2. password\_parameters

Defines configurations related to [password usage policies](../../../Product Security/Identity Identification and Authentication/Password Authentication/Password Policy (yashan Mode).html#password_policy), including password policy parameters and their values, with multiple parameters defined separated by spaces.

If a certain password policy parameter is not specified in this statement, the system will generate it in the profile according to the DEFAULT value (see the DEFAULT description below).

#### 2.1. password\_parameter\_name

Names of the password usage policy parameters, with multiple parameters defined separated by spaces.

#### 2.2. password\_parameter\_value

Specifies the values corresponding to the password policy parameters.

All parameters can be set to DEFAULT, UNLIMITED, or specific parameter values.

**DEFAULT**

Specifying DEFAULT means adopting the relevant configuration from the preset default profile.

**UNLIMITED**

Specifying UNLIMITED means there is no limitation on the parameter.

**password_parameters values**

Parameter values support mathematical operations, as long as the result meets the parameter value rules.

|Policy Parameter |Parameter Description |Parameter Value Rules |
| --------------------- | --------------------- | --------------------------------------- |
| FAILED_LOGIN_ATTEMPTS     | Maximum consecutive failed login attempts | Values must be integers in the range of (0,2147483647)                               |
| FAILED_LOGIN_INTERVAL      | Time interval to wait before retrying after a failed password attempt, in seconds | Values must be integers in the range of [1,2147483646]       |
| PASSWORD_LOCK_TIME         | Time to lock the corresponding user after reaching the maximum consecutive failed login attempts, in days | Values must be in the range of (0,24855]  |
| PASSWORD_LIFE_TIME         | Password lifecycle, in days | Values must be in the range of (0,24855]                                 |
| PASSWORD_GRACE_TIME        | Grace period after password expiration, in days | Values must be in the range of [0,24855]                                |
| PASSWORD_REUSE_TIME        | Time interval during which the password cannot be reused, in days | Values must be in the range of (0,24855]                          |
| PASSWORD_REUSE_MAX         | Number of changes required before a password can be reused | Values must be integers in the range of (0,2147483647)                       |

- FAILED_LOGIN_ATTEMPTS, FAILED_LOGIN_INTERVAL, and PASSWORD_LOCK_TIME work together to achieve the effect of "waiting X seconds before retrying after a password error, locking the user for Z days after Y consecutive retries."
- PASSWORD_REUSE_TIME and PASSWORD_REUSE_MAX work together to achieve the effect of "the new password cannot be the same as a password used in the last N days and the last M times." If one parameter is set to a specific value while the other is set to UNLIMITED, the user can never use a repeated password.

***Example***

```sql
CREATE PROFILE prof_pswd1 LIMIT FAILED_LOGIN_ATTEMPTS 5 PASSWORD_LIFE_TIME 180;

CREATE PROFILE prof_pswd2 LIMIT 
FAILED_LOGIN_ATTEMPTS 15 
FAILED_LOGIN_INTERVAL 10
PASSWORD_LOCK_TIME 1 
PASSWORD_LIFE_TIME 30*2.5 
PASSWORD_GRACE_TIME 0
PASSWORD_REUSE_TIME UNLIMITED 
PASSWORD_REUSE_MAX UNLIMITED;
```

<span id="res" name="res" class="yaslink"></span>

### 3. resource\_parameters

Defines configurations related to [session resources](../../../Product Security/Resource Control/Session Resource Control), with multiple parameters defined separated by spaces.

If a certain resource policy parameter is not specified in this statement, the system will generate it in the profile according to the DEFAULT value (see the DEFAULT description below).

#### 3.1. resource\_parameter\_name

Currently includes SESSIONS_PER_USER and IDLE_TIME.

**SESSIONS_PER_USER**

The maximum number of concurrent session connections for the same user.

**IDLE_TIME**

The maximum idle time without user operation, in minutes. If a user is logged in but remains inactive for a prolonged time that exceeds the threshold, the system will disconnect the session. If an operation is attempted after disconnection, an error will occur.

The background thread checks every 10 seconds and disconnects long idle sessions, so the maximum continuous idle time for users may be up to 10 seconds more than the corresponding threshold.

#### 3.2. resource\_parameter\_value

Specifies the values corresponding to the session resource parameters.

All parameters can be set to DEFAULT, UNLIMITED, or specific parameter values.

**DEFAULT**

Specifying DEFAULT means adopting the relevant configuration from the preset default profile.

**UNLIMITED**

Specifying UNLIMITED means there is no limitation on the corresponding resource parameter.

**resource_parameters values**

Parameter values support expression operations, as long as the results satisfy the parameter value rules.

|Policy Parameter |Parameter Description |Parameter Value Rules |
| --------------------- | ------------------------- | ----------------------------------- |
| SESSIONS_PER_USER         | The maximum number of concurrent session connections for the same user | Values must be integers in the range of [1,2147483646].                                |
| IDLE_TIME                 | The maximum idle time without user operation, in minutes | Values must be integers in the range of [1,2147483646].              |

***Example*** for Standalone Deployment and YAC Deployment

```sql
CREATE PROFILE prof_res LIMIT SESSIONS_PER_USER 10 IDLE_TIME 10;
```

<span id="ip" name="ip" class="yaslink"></span>

### 4. tcp\_ip\_parameters

Defines configurations related to [connection request IPs](../../../Product Security/Connection Management/IP Blacklist and Whitelist Management), with multiple parameters defined separated by spaces.

If a certain connection request IP configuration parameter is not specified in this statement, the system will generate it in the profile according to the DEFAULT value (see the DEFAULT description below).

#### 4.1. tcp\_ip\_parameters\_name

Currently includes login IP whitelist INVITED_NODES and login IP blacklist EXCLUDED_NODES.

The blacklist has higher priority than the whitelist; if an IP exists in both, the blacklist takes effect.

**INVITED_NODES**

Login IP whitelist. Once the IP list is configured, only users associated with the current profile can log in using the IPs in the whitelist.

The number of unique IPs must not exceed 64.

**EXCLUDED_NODES**

Login IP blacklist. Once the IP list is configured, users associated with the current profile cannot log in using the IPs in the blacklist.

The number of unique IPs must not exceed 64.

#### 4.2. tcp\_ip\_parameters\_value

Specifies the values corresponding to the connection request IP parameters.

All parameters can be set to DEFAULT, UNLIMITED, or specific IP lists.

**DEFAULT**

Specifying DEFAULT means no restrictions, which is equivalent to UNLIMITED.

**UNLIMITED**

Specifying UNLIMITED means there is no limitation on the corresponding parameter.

**iplist**

IP list string, formatted as `'IP/mask,IP'`.

- IP: actual IP address, e.g., 192.168.4.11.
- MASK: indicates the number of bits to match the binary IP from left to right, in the range of [0,32]. If MASK is omitted, it defaults to full match.

***Example*** for Standalone Deployment and YAC Deployment

```sql
CREATE PROFILE prof_ip LIMIT EXCLUDED_NODES '192.168.4.11';
```
