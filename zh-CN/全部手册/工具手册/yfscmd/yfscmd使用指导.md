命令参数
-------

参数格式：

```shell
yfscmd -H
yfscmd -V
yfscmd [-D yfs_home_path] [-p] [cmd [cmd_args]]
```

**-H**

显示帮助信息。

**-V**

显示版本信息。

**-D**

指定YFS的Home目录，必须为绝对路径。可省略，默认采用环境变量`$YASCS_HOME`。

**-p**

在yfscmd shell的promot中显示当前路径。

```bash
$ yfscmd -p
YFSCMD [+] >  cd +DG0
YFSCMD [+DG0] >  
```

**cmd**

yfscmd工具的子命令。cmd_args为子命令的参数。

运行模式
-------

**shell-交互模式**

未指定cmd时调用， 类似Linux Shell的交互环境，进入后只能运行yfscmd子命令，可以不断调用yfscmd子命令，直到调用exit命令退出yfscmd交互模式。

示例

```shell
# 不指定cmd进入交互模式
$ yfscmd -D $YASCS_HOME

YFSCMD >
```

**command-非交互模式**

指定cmd时调用，常用于在Linux Shell中直接调用yfscmd子命令，只能执行1条子命令，执行完毕即回到Linux Shell。

示例

```shell
# 指定cmd进入非交互模式，例如子命令ls
$ yfscmd ls
DG0
DG1
$
```

## 子命令

子命令包括所有的[文件管理命令](./文件管理命令)，和以`exec`调用的[磁盘管理命令](./磁盘管理命令)。

**exec**

yfscmd使用`exec`子命令执行磁盘管理或修改系统参数操作，命令格式为：

```text
exec ["] DG_DDL ["] [;]
```

其中：  

- 交互模式下`exec`指令支持多行输入，以`;`结束。
- 非交互模式下DG_DDL必须使用`""`括起来， 确保将整个DDL语句作为1个参数传给`exec`子命令，例如`yfscmd -D $YASCS_HOME exec "create diskgroup DG_0 ..."`。

## 别名

yfscmd内置了一些指令的别名。

- `ll` = `ls -l`，同Linux系统`ll`，以Long format打印文件信息。
- `q` = `exit`
- `quit` = `exit`
- `bye` = `exit`
- `resize` = `truncate -q`，静默调整文件大小，取消安全提示。

> **Note**: 
> 
> 本功能属于实验室特性。

## bash

本命令可在yfscmd中，以当前会话用户调用shell指令，但不支持管道、含有空格的参数等特性，复杂shell指令请退出yfscmd执行。

> **Note**: 
> 
> 本命令属于实验室特性，且依赖于Linux系统的bash指令。

命令格式：

```bash
bash [command [args]]
```

示例
```bash
YFSCMD >  bash uname -r 
3.10.0-1160.el7.x86_64
```
