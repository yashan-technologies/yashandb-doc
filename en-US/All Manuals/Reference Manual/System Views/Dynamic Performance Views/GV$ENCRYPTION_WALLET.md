This view displays information about the wallet status and the location of Transparent Data Encryption (TDE) wallets.

In ISC Distributed Cluster Deployment, this view is always empty.

|Field |Type |Description |
|-------|------|------|
| GROUP_ID       | NUMBER  | Group ID                                                                                                                                                 |
| GROUP_NODE_ID  | NUMBER  | Node ID within the group                                                                                                                               |
| INST_ID        | NUMBER  | Instance ID                                                                                                                                             |
| WRL_TYPE       | INTEGER | Type of the wallet resource locator:<br/>* 0: Indicates the wallet file location is not yet determined, which may occur when the wallet is not opened <br/>* 1: Indicates that the wallet uses encrypted file storage         |
| WRL_PARAMETER   | VARCHAR(4000) | Parameters of the wallet resource locator:<br/>If WRL_TYPE=1, displays the absolute directory location of the wallet file <br/>Otherwise, displays NULL                         |
| STATUS         | INTEGER | Indicates the status of the wallet:<br/>* 0: Indicates CLOSED, the wallet is closed.<br/>* 1: Indicates OPEN_NO_MASTER_KEY, the wallet is opened but the master key is not set.<br/>* 2: Indicates OPEN, the wallet is opened and the master key has been set.                |
| WALLET_TYPE    | INTEGER | Indicates the type of the keystore being used: <br/>* 0: Indicates the wallet type is not yet determined, which may occur when the wallet is not opened.<br/>* 1: Indicates that the wallet is opened using a password                                          |
| WALLET_ORDER   | INTEGER | Indicates the order in which the wallet is searched:<br/>* 0: Indicates that only a single wallet is configured.<br/>Displays as NULL when the wallet is closed.         |
| KEYSTORE_MODE  | INTEGER | Indicates the keystore mode:<br/>* 0: Indicates that the keystore mode is not being used.                                                              |
| FULLY_BACKED_UP| INTEGER | Indicates whether all keys in the keystore have been backed up<br/>* 0: Indicates not backed up<br/>* 1: Indicates backed up                      |