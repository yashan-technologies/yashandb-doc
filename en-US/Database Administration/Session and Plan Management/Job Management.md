YashanDB job management refers to the scheduled execution of SQL tasks through the *yasboot* tool. For specific command parameter explanations, please refer to the [yasboot job command](../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot job).

The advanced package [DBMS_STATS](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_STATS) can be called via *yasboot* to manage the optimizer's statistics on a schedule.

1. Generate job configuration file.

   ```shell
   $ yasboot job config gen -c yashandb --job-name test --user username --password password --sql "exec DBMS_STATS.GATHER_DATABASE_STATS('GATHER AUTO', 1, 2,  'FOR ALL COLUMNS SIZE AUTO', 'AUTO', TRUE, FALSE);" -ce "30 0 * * *"
   |key                    |value
   |-----------------------+------
   |cluster                |yashandb
   |job_name               |test
   |sql                    |exec DBMS_STATS.GATHER_DATABASE_STATS('GATHER AUTO', 1, 2,  'FOR ALL COLUMNS SIZE AUTO', 'AUTO', TRUE, FALSE);
   |---------------------- |--------
   |cron_expression        |30 0 * * *
   
   Generate config completed
   ```
   
   After successfully executing the above command, the system will generate a job_*cluster name*_*job name*.toml configuration file (according to the above example command, the file name will be job_yashandb_test.toml), allowing manual modifications to this configuration file.
   ```toml
   cluster = "yashandb"
   job_name = "test"
   username = "username"
   password = "password"
   sql = "exec DBMS_STATS.GATHER_DATABASE_STATS('GATHER AUTO', 1, 2,  'FOR ALL COLUMNS SIZE AUTO', 'AUTO', TRUE, FALSE);"
   
   [time_config]
   cron_expression = "30 0 * * *"
   ```

2. Add job.

   ```shell
   $ yasboot job add -t job_yashandb_test.toml
   ```

3. Apply the job to the database node, this article takes node 1-1 as an example.

   ```shell
   $ yasboot job apply -c yashandb --job-name test -n 1-1
   ```
   
4. Check the scheduled execution status.

   The job will be automatically triggered and execute the configured SQL command or SQL file when the scheduled time is reached. After the job is completed, the execution status can be checked using the job show command.
   ```shell
   $ yasboot job show -c yashandb -n 1-1 --job-name test
   +-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | id | job_id                           | job_name | node_id | status  | start_time          | completion_time     | hostid   | failed_reason                                                           |
   +-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | 2  | 65a8a0d8954d05c0fef16ef3021bf34a | test     | 1-1     | failed  | 2024-01-18 11:58:28 | 2024-01-18 11:58:28 | host0001 | node 1-1 exec job sql failed,stdout: [1:1]YAS-04231 keyword expected    |
   +----+----------------------------------+----------+---------+---------+---------------------+---------------------+----------+-------------------------------------------------------------------------+
   | 1  | 65a8a0d8954d05c0fef16ef3021bf34a | test     | 1-1     | success | 2024-01-18 11:55:00 | 2024-01-18 11:55:00 | host0001 | -                                                                       |
   +----+----------------------------------+----------+---------+---------+---------------------+---------------------+----------+-------------------------------------------------------------------------+
   ```
