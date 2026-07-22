YashanDB YAC is a multi-instance cluster with a single database:

- Each instance runs on a different server, and each server connects to the shared storage via fiber or network.

- A database has only one persistent file stored in the shared storage, which can be read and written by all instances. All instances form a peer-to-peer cluster, and any instance can read and write all data.

YashanDB YAC provides cluster-level high availability, ensuring uninterrupted service, and can "self-heal" in case of failures without user awareness.

For the server side, when any server in the cluster experiences an anomaly (e.g., server crash, network failure, etc.), the system will automatically handle the fault and recover without the need for operational intervention. For the client side, TAF technology allows automatic switching of connections to surviving cluster instances when a failure occurs.
