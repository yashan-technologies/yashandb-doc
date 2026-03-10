The DBA views are the views provided by the system that start with DBA_ (excluding certain specific functionality views, see specific view descriptions). They are used to present a global view of the entire database and contain information about objects from all users, generally intended for use by DBAs.

Users querying the DBA views must have DBA privilege, or must be granted object privilege by the sys user before they can query.

In ISC Distributed Cluster Deployment, the DBA views only count relevant information for the current node.