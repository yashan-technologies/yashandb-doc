```ebnf
load_file = LOAD_FILE "(" path_expr ")".
```

The LOAD_FILE function is used to read a file and return its contents as a BLOB.

To use this function, the following preconditions must be met:

- The target file must be located on the server where the database is hosted, and the file must be readable by all users.
- If the secure_file_priv system variable is set to a non-empty directory name, the target file must reside in that directory.
- The database user must have the FILE privilege.

**path_expr**

The path_expr must specify the full path of the target file.

If the target file does not exist, the function will return NULL.

***Example*** for Standalone Deployment Heap tables

```sql

SQL> alter system set secure_file_priv='/data/tmp' scope=memory;

Succeed.

SQL> !rm -rf /data/tmp/dance.txt
SQL> ‍️!echo 'abcd💖😜😊🤷‍♀️✔(*＾-＾*)Dancing' >> /data/tmp/dance.txt
SQL> select load_file('/data/tmp/dance.txt') res from dual;

res                                  
---------------------------------------------------------------- 
61626364F09F9296F09F989CF09F988AF09FA4B7E2808DE29980EFB88FE29C94282AEFBCBE2DEFBCBE2A2944616E63696E67

1 row fetched.
```
