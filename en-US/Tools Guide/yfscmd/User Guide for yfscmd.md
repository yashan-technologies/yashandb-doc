Command Parameters
-------

Parameter format:

```shell
yfscmd -H
yfscmd -V
yfscmd [-D yfs_home_path] [-p] [cmd [cmd_args]]
```

**-H**

Display help information.

**-V**

Display version information.

**-D**

Specify the Home directory of YFS, it must be an absolute path. This parameter is optional; by default, the environment variable `$YASCS_HOME` is used.

**-p**

Display the current path in the prompt of the yfscmd shell.

```bash
$ yfscmd -p
YFSCMD [+] >  cd +DG0
YFSCMD [+DG0] >  
```

**cmd**

The subcommand of the *yfscmd* tool. cmd_args are the parameters for the subcommand.

Running Modes
-------

**shell - Interactive Mode**

Called when cmd is not specified, similar to the interactive environment of Linux Shell. After entering, only *yfscmd* subcommands can be run, and you can continuously call *yfscmd* subcommands until you call the exit command to exit the *yfscmd* interactive mode.

***Example***

```shell
# Entering interactive mode without specifying cmd
$ yfscmd -D $YASCS_HOME

YFSCMD >
```

**command - Non-interactive Mode**

Called when cmd is specified, commonly used to directly call *yfscmd* subcommands in the Linux Shell. Only one subcommand can be executed, and after execution, it returns to the Linux Shell.

***Example***

```shell
# Entering non-interactive mode by specifying cmd, for example, the subcommand ls
$ yfscmd ls
DG0
DG1
$
```

## Subcommands

Subcommands include all [File Management Commands](File Management Commands) and [Disk Management Commands](Disk Management Commands) called with `exec`.

**exec**

The *yfscmd* uses the `exec` subcommand to execute disk management or modify system parameters. The command format is:

```text
exec ["] DG_DDL ["] [;]
```

Where:

- In interactive mode, the `exec` command supports multi-line input, ending with `;`.
- In non-interactive mode, DG_DDL must be enclosed in `""`, ensuring that the entire DDL statement is passed as one parameter to the `exec` subcommand, for example, `yfscmd -D $YASCS_HOME exec "create diskgroup DG_0 ..."`. 

## Aliases

*yfscmd* has built-in aliases for some commands.

- `ll` = `ls -l`, prints file information in Long format similar to Linux system `ll`.
- `q` = `exit`
- `quit` = `exit`
- `bye` = `exit`
- `resize` = `truncate -q`, silently adjusts file size and cancels safety prompts.

> **Note**: 
> 
> This functionality is a laboratory feature.

## bash

This command can be used in *yfscmd* to call shell commands as the current session user, but it does not support features like pipes and parameters with spaces. For complex shell commands, please exit *yfscmd*.

> **Note**: 
> 
> This command is a laboratory feature and relies on the bash commands of the Linux system.

Command format:

```bash
bash [command [args]]
```

***Example***
```bash
YFSCMD >  bash uname -r 
3.10.0-1160.el7.x86_64
```
