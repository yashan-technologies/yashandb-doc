Command Parameters
-------

Parameter Format:

```shell
ycsctl -H
ycsctl cmd [cmd_args]
```

**-H**

Displays help information.

**cmd**

The subcommand of the *ycsctl* tool. cmd_args are the parameters for the subcommand.

Subcommands include [Cluster Management Commands](Cluster Management Commands) and [Node Management Commands](Node Management Commands).

Among them, Cluster Management Commands provide a series of cluster configuration commands, and this configuration information will be stored in the YCR, which can be viewed using `ycsctl show config`.