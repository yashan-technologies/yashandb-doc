A YAC database consists of a database file that is persisted on shared storage and multiple peer, read-write member servers. When certain member servers encounter exceptions (such as private network isolation, storage network I/O exceptions, etc.), it is necessary to prevent the already evicted illegal member servers from continuing to modify the data file before updating the cluster member relationships based on the results of the voting arbitration. This process is called I/O Fencing.

YCS provides various I/O Fencing methods, each fence type corresponds to an integer type code. If *yasboot* is used to deploy YAC, *yasboot* will automatically select the optimal fence type for the user. If needed, users can cautiously modify the RTO requirements according to the hardware configuration of the deployment environment and business scenarios after the cluster is deployed. Before making modifications, please read the documentation of the relevant fence types carefully and execute the required hardware capability testing scripts to confirm that the hardware capabilities meet the requirements.

- [In-Flight I/O Protection Algorithm](IO Protection Algorithm): Does not rely on hardware capabilities, has strong compatibility, but slightly lower safety and RTO. Type code: 0.

- [SCSI Persistent Reservation-Based I/O Fencing](SCSI IO Fencing): Requires the shared storage device to support SCSI persistent reservation commands, providing optimal safety and RTO. Type code: 1.

## Configuration Method

The fence type of YAC can be modified using the ycsctl set_ycr command.

- Command format: ycsctl set_ycr FENCE_TYPE <Type Code>

- Notes: This command can only be executed statically and requires stopping all cluster servers and restarting to take effect; if the type code is not equal to 0, please ensure that the YCSRA process is started on each server. Otherwise, refer to the [ycsrootagent tool](../../../Tools Guide/ycsrootagent) to manually start it with sudo.

## Status Query

The fence status of YAC can be queried using the ycsctl show fence command.

- Command format: ycsctl show fence

- Notes: This command requires YCSRA to be online to provide query services.

## Common Issues

Common issues that may be encountered during configuration and usage are recorded in the corresponding subpages according to the fence type.