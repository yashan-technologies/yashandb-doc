## Tablespace Set Management Guidelines

A tablespace set is a logical storage unit in a ISC distributed database, used in ISC Distributed Cluster Deployment to store sharded tables, indexes built on sharded tables, and ac built on sharded tables. 

When a user creates a sharded table, they can specify a TABLESPACE SET for it. If not specified, the default TABLESPACE SET is used.

## YashanDB Default Tablespace Sets

YashanDB has the following built-in tablespace sets, whose properties can be set to default or specified during database creation:

- USERS tablespace set
- USERS_AIM tablespace set