After installation, the database installation user on all nodes will add the following environment variable information:

```properties
# The specific path should be based on the actual value
export YASDB_HOME=/data/yashan/yasdb_home/{version_number}
export PATH=$YASDB_HOME/bin:$PATH 
export LD_LIBRARY_PATH=$YASDB_HOME/lib:$LD_LIBRARY_PATH
export YASDB_DATA=/data/yashan/yasdb_data/db-1-1   

# In YAC/Distributed Cluster Deployment, there are also the following environment variable
export YASCS_HOME=/data/yashan/yasdb_data/ycs/ce-1-1
```

- **YASDB_HOME**: Points to the path of the YashanDB HOME directory, which contains product-related program files. When directly viewing the cluster name .bashrc environment variable file, YASDB_HOME will show as a different path (soft link pointing to `/data/yashan/yasdb_home/{version_number}`), **manual** modification of this soft link is not recommended.

- **$YASDB_HOME/bin**: Contains YashanDB auxiliary tools, which should be added to the PATH system path to allow direct execution.

- **LD_LIBRARY_PATH**: Points to the path of the YashanDB LIB directory, which contains library files required for product operation.

- **YASDB_DATA**: Points to the path of the YashanDB DATA directory, which contains product-related data files.

- **YASCS_HOME**: Unique to YAC/Distributed Cluster Deployment, pointing to the YCS service directory.

Some features require additional environment variables. If you need to use [External UDF (Java)](../../All Manuals/开发手册/PL参考手册/PL对象/自定义函数.html#ext_udf), the following environment variables also need to be configured and take effect.

```shell
# The following path should be replaced with the actual JDK installation path
$ export LD_LIBRARY_PATH=/etc/jdk-18.0.2/lib/server:$LD_LIBRARY_PATH
```
