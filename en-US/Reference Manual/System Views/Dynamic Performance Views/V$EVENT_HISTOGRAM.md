This view displays a histogram of the number of waits, maximum wait time, and total wait time for various [wait events](../../Wait Events) from the database creation to the present, with wait time measured in milliseconds.

|Field |Type |Description |
| --- | --- | --- |
| EVENT_ID         | INTEGER  | Wait event ID                                                      |
| EVENT            | VARCHAR(32) | Wait event name                                                   |
| WAIT_TIME_MILLI  | BIGINT   | Wait time distribution for the corresponding event, which determines the specific time interval for inclusion in the histogram (the right boundary of the matching time interval). The intervals include `(0,1)`, `[1,2)`, `[2,4)`, `[4,8)`, ..., `[2<sup>n-1</sup>,2<sup>n</sup>)`, ..., `[2<sup>21</sup>,2<sup>22</sup>)` and `[2<sup>22</sup>,∞)`, with units in milliseconds.<br/>For example, a value of 2 indicates that the current event falls into the histogram interval `[1,2)`. |
| WAIT_COUNT       | BIGINT   | Indicates the number of occurrences of the corresponding event's wait time distribution in the current time interval   |
| LAST_UPDATE_TIME | TIMESTAMP(6) | The last time the view data was retrieved, i.e., the end SCN of the last occurrence of the corresponding event's wait time distribution in the current time interval. |