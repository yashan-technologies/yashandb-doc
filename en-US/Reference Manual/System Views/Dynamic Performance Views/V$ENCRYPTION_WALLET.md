This view displays information about the wallet status and the location of Transparent Data Encryption (TDE) wallets.

In ISC Distributed Cluster Deployment, this view is always empty.

|Field |Type |Description |
|-------|------|------|
| WRL_TYPE        | INTEGER  | The type of wallet resource locator:<br/>* 0: Indicates the wallet file location is still uncertain, which may occur when the wallet is not opened.<br/>* 1: Indicates that the wallet file exists in the local file system.<br/>* 2: Indicates that the wallet file exists in YFS. |
| WRL_PARAMETER   | VARCHAR(4000) | The parameters of the wallet resource locator:<br/>If WRL_TYPE=0, it shows NULL; otherwise, it shows the absolute directory location of the wallet or keystore. |
| STATUS          | INTEGER  | Indicates the wallet status:<br/>* 0: Indicates CLOSED, the wallet is closed.<br/>* 1: Indicates OPEN_NO_MASTER_KEY, the wallet is opened but the master key is not set.<br/>* 2: Indicates OPEN, the wallet is opened and the master key is set. |
| WALLET_TYPE     | INTEGER  | Displays the type of the keystore being used:<br/>* 0: Indicates UNKNOWN, the wallet type is still uncertain, which may occur when the wallet is not opened.<br/>* 1: Indicates the keystore file is stored inside the file. |
| WALLET_ORDER    | INTEGER  | Indicates the order of wallet lookup:<br/>* 0: Indicates only a single wallet is configured.<br/>This will show as NULL when the wallet is closed. |
| KEYSTORE_MODE   | INTEGER  | Displays the keystore mode:<br/>* 0: Indicates the keystore mode is not used. |
| FULLY_BACKED_UP | INTEGER  | Indicates whether all keys in the keystore have been backed up:<br/>* 0: Indicates not backed up.<br/>* 1: Indicates backed up. |