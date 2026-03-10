本视图显示当前系统提供的所有窗口函数信息。

| 字段  | 类型  | 说明                                                                                                                                   |
| --- | --- |--------------------------------------------------------------------------------------------------------------------------------------|
| ID  | SMALLINT | 函数ID的枚举<br>0 AVG  <br>1 COUNT  <br>2 DENSE_RANK  <br>3 FIRST  <br>4 FIRST_VALUE  <br>5 LAG  <br>6 LAST  <br>7 LAST_VALUE  <br>...... |
| NAME | VARCHAR(64) | 函数名称                                                                                                                                 |
| ISAGGR | VARCHAR(8) | 是否为AGGR函数<br>0 Y  <br>1 Y  <br>2 N  <br>3 N  <br>4 N  <br>5 N  <br>6 N <br>7 N  <br>......                                           |