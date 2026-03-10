In YAC Deployment, once a database instance on a server fails, all connections logged into that failed instance will be disconnected, and any uncommitted transactions on that instance will be rolled back. At this point, session requests on other active instances can continue to execute, but session requests involving global resource access will be blocked until the global resource is restored; session requests that do not involve global resource access will run normally without blocking. In either case, session requests on all active instances in the cluster will ultimately complete successfully, ensuring that business operations are not affected.

The online recovery for instance failures in YashanDB handles global resource recovery during the occurrence of an instance failure. This task is carried out by the current primary instance in the cluster, and the main processing logic is sequenced as follows:

1. Collect information on the failed instance that needs online recovery.
2. Suspend access to global resource business.
3. Rebuild global resource information based on the remaining active instances.
4. Analyze the redo logs of the failed instance and lock the global resources that need recovery.
5. Restore business access to global resources.
6. Apply the redo logs; once the relevant global resources are restored, lift access to those global resources.
7. End the online recovery process.

Online recovery is automatically triggered when an instance failure occurs, requiring no manual initiation or intervention; however, if a user executes table space DDL operations during this period, they will not succeed.