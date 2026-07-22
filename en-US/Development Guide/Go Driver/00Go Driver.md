The YashanDB Go driver is an implementation of the Go standard library database/sql/driver interface. In user-built projects, you only need to import the YashanDB Go driver to use the interfaces of the standard library database/sql, allowing you to connect to and operate YashanDB.

This driver supports connection only when YashanDB is deployed in yashan mode.

For the meaning and usage instructions of all methods in the YashanDB Go interface, please refer to the Go standard library [database/sql official documentation](https://pkg.go.dev/database/sql). This manual will primarily cover the installation, usage introduction, and examples of the YashanDB Go driver.