XML Function refers to a series of functions used for processing XML data. The prerequisites for using these functions are as follows:

- All XML functions are applicable only to HEAP tables in Standalone Deployment and YAC Deployment.

- The YashanDB server must have the XML plugin installed.

    > **Note**:
    > 
    > The XML plugin is installed by default when the database service is installed with default parameters. You can confirm its presence by checking for the xml folder under the $YASDB_HOME/third path.
    >
    > When upgrading from an older version to YashanDB 23.4.2 or later, the XML plugin will not be automatically installed. To use XML processing functions, you must reinstall YashanDB.

In YashanDB, XML processing functions include: 

[EXISTSNODE](EXISTSNODE)

[EXTRACTVALUE](EXTRACTVALUE)

[XMLAGG](XMLAGG)

[XMLEXTRACT](XMLEXTRACT)

[XMLPARSE](XMLPARSE)

[XMLSEQUENCE](XMLSEQUENCE)

[XMLTABLE](XMLTABLE)
