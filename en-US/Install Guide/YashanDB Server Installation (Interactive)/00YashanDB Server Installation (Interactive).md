YashanDB supports guiding users to input various parameter options required for database deployment in an interactive manner after users enter the initial command `yasboot init`, thereby completing the installation within a single command session. 

During the interactive deployment process, after entering the corresponding configuration, you must press **Enter** to submit the information (this will not be repeated in the steps). For configurations with default values (shown as `(default: xxx)`) that do not require custom adjustments, you can directly press **Enter** to proceed to the next step.

To exit the program during the process, press **Ctrl** + **C**.

Currently, interactive deployment has limitations on deployment topologies and scenarios. If your required deployment topology and scenario are not listed in the table below, please use command-line or graphical deployment methods. 

|Supported Deployment Forms                  |Supported Deployment Scenarios    |
| ------------------------------------------------------------ | -------------------------------------------------- |
| Standalone Deployment (Single database or Non-cascaded non-dual replication group primary-standby) | Non-CDB<br>yashan mode<br>Single network segment   |
| YAC Deployment (Single cluster or primary-standby cluster)   | Non-CDB<br/>yashan mode<br/>Single network segment |