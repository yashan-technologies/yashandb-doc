The focus of SQL-level optimization is to improve the performance of SQL statements through various methods, in order to meet specific and measurable performance standards.

Typically, users hope to achieve the following two goals through SQL tuning:

- Reduce the response time of SQL execution, i.e., decrease the time from when the user sends the execution command to when the data reply is received.
- Increase throughput, which means handling the data demands of statements with less resources under normal load conditions.

This requires analysis and, when necessary, tuning from both the perspective of how developers write SQL and how the database executes it, in order to achieve the ideal performance goals as much as possible.

This manual will provide an introduction to SQL tuning, progressing from basic to advanced topics through [Introduction to SQL Tuning Fundamentals](Introduction to SQL Tuning Fundamentals/00Introduction to SQL Tuning Fundamentals) and [Principle and Rules of SQL Tuning](Principle and Rules of SQL Tuning/00Principle and Rules of SQL Tuning). The goal is to help users understand the working principles and execution processes of the YashanDB optimizer. At the same time, carrying out SQL tuning also requires users to have a deep understanding of their business logic and database environment. A comprehensive analysis that combines various aspects is necessary to ensure better and more suitable tuning results.