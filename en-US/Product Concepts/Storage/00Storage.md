YashanDB provides efficient, flexible, and convenient storage management capabilities by dividing the database storage structure into logical storage structure and physical storage structure:

- [Physical Storage Structure](Physical Storage Structure): The physical form of data storage, usually physical files at the operating system level.

- [Logical Storage Structure](Logical Storage Structure): The logical structure based on physical files, which facilitates the management of physical storage.

By separating the physical storage structure and the logical storage structure, YashanDB users can manage different structures independently without affecting each other. For example, extending the data file will not affect the table data stored within it, and deleting a table will not impact the actual stored file structure.