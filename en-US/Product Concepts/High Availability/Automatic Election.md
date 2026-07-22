## Primary/Standby Leader Election

In a standalone one-primary/multi-standby deployment or an ISC distributed high availability deployment (the nodes within the DN group are one-primary/multi-standby), the primary/standby leader election functionality can be enabled to achieve primary database election and automatic failover. The primary/standby leader election adopts the Raft algorithm and supports setting Quorum.

### Heartbeat

Raft uses a heartbeat mechanism to detect database status. The database in the leader role periodically sends heartbeat messages to all followers to maintain its position. A follower will remain in its follower state as long as it receives valid heartbeat or voting messages from either the leader or a candidate. If a follower does not receive any messages within a certain period of time (i.e., election timeout), it will conclude that there is no available leader in the system and initiate an election to select a new leader.

### Term

Raft divides time into arbitrary lengths of terms, labeled with consecutive integers. Each term begins with an election, during which one or more candidates attempt to initiate voting to become the leader. If a candidate wins the election, it serves as the leader in the subsequent term. In some cases, votes during a single election may be split among multiple databases, resulting in the term ending without a leader and starting a new term (along with a new election). Raft ensures that at most one leader exists within a term.

### Node Priority

Node priority is determined by values, with higher values indicating higher priority for the node. During the election process, Raft ensures that, while maintaining data consistency, high-priority nodes in the cluster are preferred to become the leader.

### Election State

Once the primary/standby leader election is enabled, the database has the following election states at runtime:

- Startup

  After the leader election starts, it is in the startup state. If there are no other databases, the current database directly becomes the leader; if there are other databases, it switches to the pre-candidate state.

- Follower

  Under normal circumstances, the follower continuously receives heartbeat messages from the leader. Every time a heartbeat message is received, it resets the election timeout and the time of the last received heartbeat (used for the voting process), and will update the term and leader based on the heartbeat message. If a heartbeat message is not received within an election timeout period, it will switch to the pre-candidate state.

- PreCandidate & Candidate

  When a pre-candidate finds that it can obtain votes from the majority of databases, it will switch to the candidate state. The handling logic for pre-candidate and candidate states is fundamentally the same, but the pre-candidate does not increase the term when initiating voting, while the candidate will increment the term. The pre-candidate avoids the issue of frequent term increases due to instability in the database. When a candidate receives votes from the majority of databases for the same term, it wins the election and becomes the leader. Each database will cast at most one vote for a term (based on first-come-first-served), and only one candidate can win that election. Once a candidate wins the election, it becomes the leader for that term, then it sends heartbeat messages to other databases to secure its position and prevent the emergence of a new leader.

- Leader

  After obtaining the majority of votes from other databases, a candidate switches to the leader state. The leader periodically sends heartbeat messages to other databases.

- Shutdown

  The primary/standby leader election is in a stopped state when not enabled.

## yasomElection

In a standalone one-primary/one-standby deployment or an ISC distributed high availability deployment (the nodes within the DN group are one-primary/one-standby), based on yasom arbitration, master election can be enabled. When yasom detects a failure in the primary database, it triggers arbitration switch to promote the standby database, continuing to provide services. The former primary is demoted to standby by yasom.

The election method based on yasom has a normal mode and a zero loss mode:

- Normal Mode
  
  It prioritizes availability; as long as there is a surviving standby database, a switch can occur in the event of primary database failure, but it does not guarantee no data loss.

- Zero Loss Mode

  Prioritizing primary/standby maximizes protection, ensuring that when the primary database fails, the standby database switch to leader does not result in data loss. If the standby database fails, yasom will demote the primary database to maximize availability mode to ensure business remains uninterrupted. After the standby database recovers, if the primary database fails before data synchronization, yasom will not perform arbitration switch since the standby database data is incomplete. Once the data between the standby database and primary database is synchronized again, yasom will promote the primary database to maximize protection mode.

## YAC Leader Election

In a YAC Deployment context, YCS conducts voting arbitration upon detecting system failures, determining the new master instance and identifying the survivors that can remain in the cluster. It then notifies all servers to take necessary reorganization actions to continue providing services.

### Heartbeat

YAC service uses heartbeat signals to detect system failures, automatically triggering master election. The following heartbeat methods are available:

- Network Heartbeat

  Instances send heartbeat detection messages to each other over the network and determine if other instances are abnormal based on the timely receipt of responses.

- Disk Heartbeat

  Each instance periodically updates the heartbeat sequence number in the private area of the current instance in shared storage. If other instances notice that the corresponding instance's heartbeat sequence number has not changed, they conclude that the corresponding instance is abnormal.

### Term

YAC service uses terms to represent the period from the creation of a new master instance until the failure of that master instance (either being actively taken offline or the next detection of failure). YAC service manages the periods of providing available services based on terms.

### Election State

YAC service completes the master election process based on shared storage. Instances have the following election states during operation:

- Normal

  During normal operation, instances are in the normal state. Instances enter the normal state after the startup phase and completing a round of voting arbitration.

- Candidate

  When an instance discovers that there is no normally available master instance in the cluster service, it proactively switches itself to candidate state and refreshes its voting term in shared storage.

- Follower

  When an instance detects that other instances have entered a new voting term, it switches to follower state, awaiting the final master election result. In concurrent scenarios, multiple instances may exist in the candidate state within the cluster, but after several rounds of competition, only one candidate instance will remain, with the others switching to follower state.

- Teller

  The sole candidate instance in the cluster will automatically switch to teller state and select the most suitable instance (usually the one with the smallest ID) as the master instance for the corresponding term, informing the other instances accordingly.