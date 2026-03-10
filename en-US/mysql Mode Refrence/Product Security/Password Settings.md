The password policy is used during the password setting process, where the system determines whether the user's password meets the requirements based on the rules established by the policy.

In YashanDB (mysql mode), the password policy and strength are controlled by the following [system variables](../../All Manuals/Reference Manual of mysql Mode/系统变量/系统变量参考):

|Variable Name |Parameter Description |
|--------------------|--------------------------------------|
| VALIDATE_PASSWORD_POLICY          | Specifies the password strength check policy:<br/>* 0: Only checks password length specifications.<br/>* 1: Based on policy 0, adds character requirements, checking for the number of numeric characters, lowercase letters, uppercase letters, and special (non-numeric) characters.<br/>* 2: Based on policy 1, adds a check for the match between password substrings and the dictionary file. Password substrings with a length of at least 4 must not match any word in the dictionary file. |
| VALIDATE_PASSWORD_LENGTH          | Specifies the minimum length of the password.         |
| VALIDATE_PASSWORD_MIXED_CASE_COUNT| Specifies the minimum number of uppercase and lowercase letters required in the password. |
| VALIDATE_PASSWORD_NUMBER_COUNT    | Specifies the minimum number of digits required in the password. |
| VALIDATE_PASSWORD_SPECAIL_CHAR_COUNT | Specifies the minimum number of special characters required in the password. |
| VALIDATE_PASSWORD_CHECK_USER_NAME | Whether to allow the password to include the corresponding username. |
| VALIDATE_PASSWORD_DICTIONARY_FILE | Specifies the path to the dictionary file that records prohibited password words, which are strings that are not allowed as part of the password. |

Based on the default values of the relevant system variables, the default password strength requirements in mysql mode are:

- The password length must be at least 8 characters.
- The password must contain at least 1 special character, 1 digit, 1 uppercase letter, and 1 lowercase letter.
- There is no check for whether the password includes the corresponding username, and there are no prohibited words.