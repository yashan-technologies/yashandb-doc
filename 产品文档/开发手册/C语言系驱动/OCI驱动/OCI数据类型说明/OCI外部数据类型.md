外部数据类型的数据类型代码如下表所示。

| YashanDB外部数据类型代码 | 绑定、定义支持情况（B in/B out/D 表示bind input/bind output/define） | 对应C数据结构                                                |
| ------------------------ |---------------------------------------------------------| ------------------------------------------------------------ |
| SQLT_INT                 | B in/D                                                  | signed char<br />signed short<br />signed int<br />signed long long |
| SQLT_STR                 | B in/D                                                  | char[n+1]                                                    |
| SQLT_AFC                 | B in/D                                                  | char[n]                                                      |
| SQLT_CHR                 | D                                                       | char[n]                                                      |
| SQLT_LNG                 | B in/D                                                  | char[n]                                                      |
| SQLT_FLT                 | B in/D                                                  | float<br />double                                            |
| SQLT_TIMESTAMP           | B in/D                                                  | OCIDateTime *                                                |
| SQLT_INTERVAL_DS         | B in/D                                                  | OCIInterval *                                                |
| SQLT_VNU                 | B in                                                    | char[22]                                                     |
| SQLT_BDOUBLE             | B in                                                    | double                                                       |
| SQLT_BLOB                | B in/D                                                  | OCILobLocator*                                               |
| SQLT_CLOB                | B in/D                                                  | OCILobLocator*                                               |
| SQLT_RSET                | B out/D                                                 | OCIStmt*                                               | 

