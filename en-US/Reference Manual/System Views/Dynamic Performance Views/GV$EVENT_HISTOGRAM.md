This view displays a histogram of wait counts, maximum wait times, and total wait times for various [Wait Events](../../Wait Events) from the creation of the database to the present, with wait time measured in milliseconds.

|Field |Type |Description |
| --- | --- | --- |
| GROUP\_ID | NUMBER | Group ID |
| GROUP\_NODE\_ID | NUMBER | Node ID within the group |
| INST\_ID | NUMBER  | Instance ID |
| EVENT\_ID | INTEGER | Wait event ID |
| EVENT | VARCHAR(32) | Wait event name |
| WAIT\_TIME\_MILLI | BIGINT | Distribution of wait time for the corresponding event. This value determines the specific time interval for the wait event to be included in the histogram (the right boundary of the matching time interval). The intervals include `(0,1)`, `[1,2)`, `[2,4)`, `[4,8)`, …, `[2<sup>n-1</sup>,2<sup>n</sup>)`, …, `[2<sup>21</sup>,2<sup>22</sup>)` and `[2<sup>22</sup>,∞)`, measured in milliseconds.<br/>For example, a value of 2 indicates that the current event falls into the histogram interval `[1,2)` |
| WAIT\_COUNT | BIGINT | Indicates the number of occurrences of the corresponding event's wait time distribution within the current time interval |
| LAST\_UPDATE\_TIME | TIMESTAMP(6) | The time of the last data retrieval for the current view, specifically the end SCN for the last time the corresponding event's wait time distribution fell within the current time interval |