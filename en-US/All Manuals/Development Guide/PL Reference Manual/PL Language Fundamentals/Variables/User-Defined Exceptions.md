Custom exception variables are used in user-defined exception handling.

After a custom exception variable is declared, it can be initialized, raised, and caught within the procedure body, as detailed in the [Exception Handling](../../PL Exception Handling) chapter.

Use the EXCEPTION keyword to define an exception variable, with the definition format as follows:

*exception_name EXCEPTION;*

Where exception_name is the name of the exception, with a maximum length of 64 bytes. The exception name may conflict with YashanDB's [system predefined exceptions](../../PL Exception Handling).