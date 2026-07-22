## Functionality Overview

The OCIEnvInit function is used to allocate and initialize an environment handle.

## Function Declaration

```c
sword OCIEnvInit ( OCIEnv    **envhpp,
                   ub4         mode,
                   size_t      xtramemsz,
                   void      **usrmempp );
```

## Parameter Description

|Parameter Name |Description |
| -------------- | ---------------------------------------------------------- |
| envhpp (OUT)    | Environment handle.<br />Its encoding setting is specified by mode, which is inherited by statement handles derived from envhpp. |
| mode (IN)       | Specifies the mode of initialization.                    |
| xtramemsz (IN)  | Specifies the amount of user memory to allocate for the duration of the environment. |
| usrmempp (OUT)  | Returns a pointer to the user memory allocated by the caller of size xtramemsz. |

Some parameters currently only support the following values:

|Parameter |Valid Values |
| ---- | ------------------------------------------ |
| mode      | OCI_DEFAULT (default value)<br /> OCI_ENV_NO_UCB |