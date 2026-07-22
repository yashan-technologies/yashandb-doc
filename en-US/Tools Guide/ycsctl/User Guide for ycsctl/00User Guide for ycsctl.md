## Command Parameters

Parameter Format:

```shell
ycsctl -H
ycsctl cmd [cmd_args]
```

**-H**

Displays help information.

**cmd**

The subcommand of the *ycsctl* tool. cmd_args are the parameters for the subcommand.

## Subcommand Classification

The detailed subcommands are shown in the table below.

| Classification     | Subcommands           |
| ------------------ | ---------------------- |
| [Cluster Configuration Commands](./Cluster Configuration Commands.md)       | ycsctl create cluster  |
|                    | ycsctl set_ycr         |
|                    | ycsctl import          |
|                    | ycsctl export          |
|                    | ycsctl show config     |
|                    | ycsctl status       |
| [Node Management Commands](./Node Management Commands.md)       | ycsctl add node        |
|                    | ycsctl extend node     |
|                    | ycsctl modify node     |
|                    | ycsctl start ycs       |
|                    | ycsctl stop ycs        |
|                    | ycsctl start osw       |
|                    | ycsctl stop osw        |
|                    | ycsctl get             |
|                    | ycsctl set             |
|                    | ycsctl show parameter  |
| [Database Resource Management Commands](./Database Resource Management Commands.md) | ycsctl add database    |
|                    | ycsctl add instance    |
|                    | ycsctl start instance  |
|                    | ycsctl stop instance   |
|                    | ycsctl modify instance |
|                    | ycsctl add pdb         |
|                    | ycsctl start pdb       |
|                    | ycsctl stop pdb        |
|                    | ycsctl status pdb      |
|                    | ycsctl modify pdb      |
|                    | ycsctl remove pdb      |
| [Network Resource Management Commands](./Network Resource Management Commands.md)   | ycsctl add network     |
|                    | ycsctl modify network  |
|                    | ycsctl remove network  |
|                    | ycsctl add vip         |
|                    | ycsctl start vip       |
|                    | ycsctl stop vip        |
|                    | ycsctl relocate vip    |
|                    | ycsctl modify vip      |
|                    | ycsctl remove vip      |
|                    | ycsctl add scan        |
|                    | ycsctl start scan      |
|                    | ycsctl stop scan       |
|                    | ycsctl relocate scan   |
|                    | ycsctl modify scan     |
|                    | ycsctl remove scan     |
| [Other Commands](./Other Commands.md)           | ycsctl show fence         |
|                    | ycsctl query disk      |