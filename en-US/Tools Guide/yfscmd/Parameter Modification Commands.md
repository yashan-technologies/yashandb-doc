The `exec` subcommand of *yfscmd* can be used to modify the configuration of certain YFS parameters online. The specific list of modifiable parameters can be viewed using the show param command.

### Command Format

- In non-interactive mode of *yfscmd*, the parameter modification statement must be enclosed in quotes and passed as a single parameter, without requiring a semicolon (`;`) at the end. The command format is: `yfscmd exec "alter system ..."`.

- In interactive mode of *yfscmd*, the command format is `exec alter system set ...;`. In interactive mode, the `exec` subcommand supports multi-line input until terminated by `;`.

### Statement Definition

**ALTER_SYSTEM::=**

```ebnf
= ALTER SYSTEM SET param_name "=" param_value.
```

***Example***

```shell
# Update upper threshold for triggering resource reclaim in non-interactive mode
$ yfscmd exec "ALTER SYSTEM SET RECY_UPPER_THRESHOLD = 90"

# Enter interactive mode
$ yfscmd -D $YASCS_HOME

-- Update termination resource recovery lower threshold (single-line input)
YFSCMD >  exec ALTER SYSTEM SET RECY_LOWER_THRESHOLD = 50;
-- Adjust local memory upper limit (multi-line input)
YFSCMD >  exec 
>  ALTER SYSTEM SET 
>  SYS_AREA_SIZE = 1G; 
```
