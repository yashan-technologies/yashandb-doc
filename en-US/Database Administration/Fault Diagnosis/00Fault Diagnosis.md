YashanDB provides a fault diagnosis architecture to collect and manage diagnostic data to diagnose and resolve database issues.

The fault diagnosis architecture helps in preventing, detecting, diagnosing, and resolving problems. When a severe error occurs, it will be assigned an event number, and diagnostic data for that error will be captured immediately and marked with the number; this diagnostic data is then stored in an automatic diagnostic repository and can be retrieved and analyzed later based on the event number.

The goals of the fault diagnosis architecture are as follows:

* First fault diagnosis
* Fault prevention
* Reporting errors upon detection to prevent further data corruption
* Reducing the time for fault diagnosis
* Reducing the time for fault resolution

The key technologies to achieve these goals are:

* **Event Alert**: For severe errors, diagnostic data will be collected immediately, an event number will be assigned for identification, and stored in the automatic diagnostic repository for issue tracking and resolution.
* **Fault Detection and Response**: When the database detects an error, certain measures will be taken to limit damage or interruption. For example: when archiving disk space is insufficient, alarm logs will be recorded, and the database will be set to a fault state to prevent users from executing operations without being aware of the error. The DBA needs to intervene promptly to handle the relevant faults and restore the database status.
* **[Health Monitor](Health Monitor)**: After detecting a severe error, the database will automatically trigger one or more health check tasks to perform a deeper analysis of the severe error, and the results of the health checks are stored in the automatic diagnostic repository. A single health check will check for data block corruption, redo bad blocks, etc. The DBA can also manually invoke these health checks regularly or on demand.
* **[Patrol](Patrol)**: It monitors some components of the database in real-time and immediately reports or automatically repairs when a severe error is detected, preventing more severe errors, such as monitoring data files.
* **[Manual Data Capture](Dump) — dump**: Allows users to manually execute the dump command to dump internal system structure information to trace files, analyzing this information to determine fault issues.
* **[Automatic Data Capture](Dump.md#blackbox) — "Black Box"**: Automatically collects process running stack information before the database process experiences a fault crash.
* **[Trace Events](Trace)**: YashanDB defines a series of trace events that users can choose to trace different events as needed to understand SQL execution information, aiding in performance observation and fault localization.