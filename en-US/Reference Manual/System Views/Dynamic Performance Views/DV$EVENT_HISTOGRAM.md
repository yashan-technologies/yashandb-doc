This view shows a histogram of the wait counts, maximum wait times, and total wait times for each [wait event](../../Wait Events) from the database creation to the present. The unit of wait time is milliseconds.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID          | INTEGER   | Group ID                                                           |
| GROUP_NODE_ID     | INTEGER   | Node ID within the group                                            |
| EVENT_ID          | INTEGER   | Wait event ID                                                      |
| EVENT             | VARCHAR(32)| Wait event name                                                    |
| WAIT_TIME_MILLI   | BIGINT    | The wait time distribution for the corresponding event, used to determine the specific time period for the wait event included in the histogram (right boundary of the matching time period). The periods include `(0,1)`, `[1,2)`, `[2,4)`, `[4,8)`, …… , `[2<sup>n-1</sup>,2<sup>n</sup>)`, …… , `[2<sup>21</sup>,2<sup>22</sup>)` and `[2<sup>22</sup>,∞)`, with the unit being milliseconds.<br/>For example, a value of 2 indicates that the current event falls within the histogram range of `[1,2)` |
| WAIT_COUNT        | BIGINT    | Indicates the count of the corresponding event's wait time distribution in the current time period |
| LAST_UPDATE_TIME  | TIMESTAMP | The last time the current view data was retrieved, i.e., the last SCN when the corresponding event's wait time distribution ended in the current time period |