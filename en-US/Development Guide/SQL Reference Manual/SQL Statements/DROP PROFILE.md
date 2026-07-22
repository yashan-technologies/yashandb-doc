General Description
----

DROP PROFILE is used to delete a profile.

Please note the following when executing this statement:

- The user must have the DROP PROFILE privilege in order to delete a profile.
- The system default profile (named DEFAULT) cannot be deleted.
- For profiles already associated with users, the CASCADE keyword must be used to delete them, and the corresponding users will be automatically associated with the default PROFILE.
- In a CDB (with configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE), deleting global profiles must be executed by connecting to the CDB root. 

Statement Definition
----

**drop profile::=**

```ebnf
= DROP PROFILE profile_name [CASCADE].
```

### profile_name

The name of an existing profile.

### CASCADE

Specifying CASCADE means that the profile will be deleted along with its associations with users.

***Example***

```sql
DROP PROFILE prof_pswd1;

DROP PROFILE prof_pswd2 CASCADE;
```

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
DROP PROFILE prof_res;

DROP PROFILE prof_ip CASCADE;
```
