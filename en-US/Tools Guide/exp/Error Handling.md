## Database Error Codes

For detailed information about error codes composed of 'YAS-' followed by five digits, please refer to [Database Error Codes](../../Reference Manual/Error Codes).

## exp Error Codes

Error Number: Composed of 'YASEXP-' followed by five digits.

Error Message: A description of the error information. In different error scenarios, the system passes messages with specific details about that error through variables.

### YASEXP-001:EXIM_ERR_UNRECOGNIZED_PARAM

**Message**: unrecognized parameter %s

**Action**: Remove the unrecognized parameter.

### YASEXP-002:EXIM_ERR_INVALID_PARAM_VALUE

**Message**: invalid value for parameter %s

**Action**: Check the parameter based on the information.

### YASEXP-003:EXIM_ERR_INVALID_PARAM

**Message**: invalid parameter %s occurs

**Action**: Check the data parameter based on the information.

### YASEXP-004:EXIM_ERR_PARAMS_EXPECTED

**Message**: parameter %s is requested

**Action**: Add the necessary parameter based on the information.

### YASEXP-005:EXIM_ERR_INVALID_USERID

**Message**: invalid %s

**Action**: Check the connection string information based on the information.