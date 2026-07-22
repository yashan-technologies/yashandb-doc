YashanDB Python driver (python-yaspy) is a Python extension module that supports the [Python DB API specification](https://peps.python.org/pep-0249/), allowing general Python applications to connect directly to the YashanDB database.

This driver supports connections only when YashanDB is deployed in yashan mode.

YashanDB provides two Python driver packages: yaspy and yasdb. Either package can be installed to connect to the YashanDB database.

YashanDB v23.4.4 has introduced performance improvements for the yaspy module (significant performance advantages over yasdb under the same test environment). Additionally, the priority for future feature development will be: yaspy > yasdb. The yaspy module is recommended.

This chapter uses yaspy as an example, and the yasdb module can be referenced for similar operations.