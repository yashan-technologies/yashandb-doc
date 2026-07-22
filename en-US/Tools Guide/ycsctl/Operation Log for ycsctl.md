The *ycsctl* running log mainly records key information during the execution of *ycsctl* commands, helping operation and maintenance personnel to locate and address issues that arise during the execution of commands.

The specifications of the running log are as follows:

- Log level: INFO.
- Default archive size: 20MB, which cannot be adjusted.
- Default number of archives: 10, which cannot be adjusted. When the number exceeds the default number of archives, the earliest log files are automatically cleared.
- Default path: $YASCS_HOME/log/ycsctl.