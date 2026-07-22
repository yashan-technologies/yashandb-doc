High availability of cluster databases means that when any server in the cluster fails, the system has the ability to perform failover and recovery. Users connected to the failed server will be automatically redirected to other active servers without perceiving this change.

Potential failure scenarios in a YAC include:

- YFS itself failing, such as disk failures or data corruption.
- YCS itself failing, such as network interruptions or process abnormalities.
- The aforementioned failures, as well as failures of the database itself, will cause database instance failures and user connections to be dropped.

YashanDB has established a failure mode library to detect various types of failures and automatically handle them when they occur:

- [High Availability of YFS](High Availability of YFS) implements automatic handling and recovery capabilities for YFS failures.
- [High Availability of YCS](High Availability of YCS) implements server leader election and switch capabilities.
- [Online Recovery from Instance Failures](Online Recovery from Instance Failures) implements data recovery capabilities for failed instances.