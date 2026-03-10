By limiting the number of sessions a user can have simultaneously and setting an upper limit on idle session retention time, session hijacking or privilege escalation attacks can be effectively prevented, as well as malicious exploitation of long-idle sessions. Additionally, promptly releasing idle sessions can save database resources, helping the system maintain high availability and stability even under high load or concurrency scenarios.

YashanDB implements session resource control based on profiles, associating users with profiles to specify which corresponding limitations apply to specific users.

- Executing the [CREATE PROFILE](../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/CREATE PROFILE.html# res) and [ALTER PROFILE](../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/ALTER PROFILE.html# res) statements can configure session resource thresholds.

- Executing the [CREATE USER](../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/CREATE USER.html#profile) or [ALTER USER](../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/ALTER USER.html#profile) statements can associate users with profiles, i.e., configure the effective users for session resource threshold.

## Parallel Session Count SESSIONS\_PER\_USER

The parallel session count refers to the maximum number of parallel session connections that a user can create, configured via the SESSIONS_PER_USER parameter, with no default limit.

## Idle Session Retention Time IDLE\_TIME

Sessions that have been idle for a certain period (i.e., idle sessions) will be automatically disconnected. The upper limit for the retention time of idle sessions is configured through the IDLE_TIME parameter, measured in minutes, with no default limit.

A background thread performs checks every 10 seconds to disconnect long-idle sessions, thus the maximum continuous idle time for a user can exceed the corresponding threshold for that user by up to 10 seconds.