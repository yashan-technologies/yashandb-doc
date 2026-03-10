The regular expression implementation of YashanDB is compatible with the same syntax and semantics as Perl, and also supports the Unicode regular expression specification.

> **Note**: 
> "UTF8 support" indicates the support for UTF8 characters (such as Chinese, etc.) other than ASCII, when the character set of the database server is UTF8. 
> Furthermore, if the database server uses other encoding formats, the matching rules will follow the ASCII character matching rules.
> The following rules apply to ASCII encoded characters.

## Metacharacters:

### Table 1-1: Metacharacters (not appearing in character classes)

|Symbol |Description |UTF8 Support |
|---             |---                                                             |---      |
|\                | Universal escape character (multiple usages)                | Supported |
|^                | Start of string (or line in multi-line mode)                | Supported |
|$                | End of string (or line in multi-line mode)                  | Supported |
|.                | Matches any character except newline (default)               | Supported |
|[                | Character class start marker                                  | Supported |
|]                | Character class end marker                                    | Supported |
|&#124;           | Start of alternative branch                                   | Supported |
|(                | Start marker for subpattern or control predicate              | Supported |
|)                | End marker for subpattern or control predicate                | Supported |
|*                | Occurs 0 or more times (greedy)                             | Supported |
|+                | Occurs 1 or more times (possessive)                         | Supported |
|?                | Occurs 0 or 1 time (lazy)                                   | Supported |
|{                | Start marker for repetition quantifier                        | Supported |
|}                | End marker for repetition quantifier                          | Supported |

### Table 1-2: Metacharacters (appearing in character classes)

|Symbol |Description |UTF8 Support |
|---             |---                                                             |---      |
|\                | Universal escape character                                   | Supported |
|^                | Negation of the class, but only effective for the first character | Supported |
|-                | Indicates character range                                    | Supported |
|[                | Start marker for POSIX character class (if followed by POSIX syntax) | Supported |
|]                | End marker for POSIX character class                         | Supported |

## Matching Modes:

### Table 2-1: Single Matching

|Symbol |Description |UTF8 Support |
|---                 |---                                                                      |---      |
|.                     | Matches any character except newline                      | Supported |
|x                     | Single character x                                        | Supported |
|&#91;character class&#93; | Matches one character from the "character class", "character class" is defined later | Supported |
|&#91;^character class&#93; | Matches one character not in the "character class", "character class" is defined later | Supported |
|(xxx)                 | Matches xxx, marking the inner expression as a subexpression   | Supported |
|\lowercase Perl tag   | Matches one character from the "Perl character class", "Perl character class" is defined later | Supported |
|\uppercase Perl tag   | Matches one character not in the "Perl character class", "Perl character class" is defined later | Supported |
|\p{xx}                | Matches one character from the "Unicode class" (General class + Script class), "Unicode class" is defined later | Supported |
|\P{xx}                | Matches one character not in the "Unicode class" (General class + Script class), "Unicode class" is defined later | Supported |
|\pxx                  | Matches one character from the "Unicode class" (General class only), "Unicode class" is defined later | Supported |
|\Pxx                  | Matches one character not in the "Unicode class" (General class only), "Unicode class" is defined later | Supported |

### Table 2-2: Composite Matching

|Symbol |Description |UTF8 Support |
|---             |---                                                          |---      |
|xy                | Matches xy (x followed by y)                              | Supported |
|x&#124;y         | Matches x or y (x has precedence)                         | Supported |

### Table 2-3: Repetitive Matching

|Symbol |Description |UTF8 Support |
| ------- | ----------------------------------------- | -------- |
| x?        | x, 0 or 1 time (greedy)                             | Supported    |
| x?+       | x, 0 or 1 time (possessive)                         | Supported    |
| x??       | x, 0 or 1 time (lazy)                               | Supported    |
| x*        | x, 0 or more times (greedy)                         | Supported    |
| x*+       | x, 0 or more times (possessive)                     | Supported    |
| x*?       | x, 0 or more times (lazy)                           | Supported    |
| x+        | x, 1 or more times (greedy)                         | Supported    |
| x++       | x, 1 or more times (possessive)                     | Supported    |
| x+?       | x, 1 or more times (lazy)                           | Supported    |
| x{n}      | x, exactly n times (greedy)                          | Supported    |
| x{n,m}    | x, at least n times, but not more than m times (greedy) | Supported    |
| x{n,m}+   | x, at least n times, but not more than m times (possessive) | Supported |
| x{n,m}?   | x, at least n times, but not more than m times (lazy) | Supported   |
| x{n,}     | x, at least n times (greedy)                          | Supported    |
| x{n,}+    | x, at least n times (possessive)                      | Supported    |
| x{n,}?    | x, at least n times (lazy)                            | Supported    |

## Matching Positions:

### Table 3-1: Matching Positions

Specifies that a condition must match at a specific point without consuming any characters of the string.

|Symbol |Description |UTF8 Support |
|---             |---                                                          |---      |
|^                | Matches start of string                                   | --           |
|$                | Matches end of string                                     | --           |
|\b               | Matches word boundary                                      | --           |
|\B               | Matches non-word boundary                                  | --           |
|\A               | Matches start of string                                    | --           |
|\Z               | Matches end of string, and also matches before a newline at the end of the string | -- |
|\z               | Matches end of string                                    | --           |
|\G               | Matches the first match position in the string          | --           |

## Escape Characters:

### Table 4-1: Escape Characters

|Escape Character |Description |UTF8 Support |
|---        |---                              |---      |
|\a                | Alarm character (hex 07)                 | --           |
|\cx              | Control character                         | --           |
|\d                | Backreference expression (d is an Arabic numeral between 1 and 9), matches the subexpression that appears between '(' and ')' | -- |
|\e                | Escape character (hex 1B)                | --           |
|\f                | Form feed character (hex 0C)             | --           |
|\n                | Newline character (hex 0A)                | --           |
|\r                | Carriage return character (hex 0D)       | --           |
|\t                | Tab character (hex 09)                   | --           |
|\0dd             | Octal number dd                           | --           |
|\ddd             | Octal number ddd (or the number for backreference) | -- |
|\o{ddd..}        | Octal number ddd..                        | --           |
|\xhh             | Hexadecimal number hh                     | --           |
|\x{hh..}         | Hexadecimal number hh..                   | --           |
|\\\\             | Character \                               | --           |
|\\^              | Character ^                               | --           |
|\\$              | Character $                               | --           |
|\\.              | Character .                               | --           |
|\\*              | Character *                               | --           |
|\\+              | Character +                               | --           |
|\\?              | Character ?                               | --           |
|\\{              | Character {                               | --           |
|\\}              | Character }                               | --           |
|\\(              | Character (                               | --           |
|\\)              | Character )                               | --           |
|\\[              | Character [                               | --           |
|\\]              | Character ]                               | --           |
|\\&#124;         | Character &#124;                          | --           |

## Character Classes:

### Table 5-1: Summary of Character Classes

The general usage for character classes is [...] and for POSIX character classes is [\[:xxx:]].

|Symbol |Description |UTF8 Support |
|---             |---                                                          |---      |
|[...]             | Specifies any character matching an expression represented in the list (the characters mean "or" between them, for example, [12] matches 1 or 2) | Supported |
|&#91;^...&#93;    | Specifies any character matching an expression not represented in the list | Supported |
|[x-y]             | Range (can be used for hexadecimal characters, includes edge characters) | Supported |
|\[[:xxx:]]        | POSIX character class                                | --           |
|\[[:^xxx:]]       | Non-POSIX character class                            | --           |
|[\letter]         | Perl Character Class                                 | Supported |
|[p{xx}]           | Unicode class (General class + Script class)           | Supported |
|[pxx]             | Unicode class (General class only)                  | Supported |

### Table 5-2: POSIX Character Classes

|POSIX Character Class |Description |UTF8 Support |
| ----------- | -------------------------------------------- | -------- |
| alnum                | Alphanumeric (equivalent to [0-9A-Za-z])       | --           |
| alpha                | Alphabetical (equivalent to [A-Za-z])          | --           |
| ascii                | ASCII character set (equivalent to [\x00-\x7F])| --           |
| blank                | Blank placeholder (equivalent to [\t])          | --           |
| cntrl                | Control characters (equivalent to [\x00-\x1F\x7F]) | --        |
| digit                | Digits (equivalent to [0-9])                    | --           |
| graph                | Graphic characters (equivalent to [!-~])       | --           |
| lower                | Lowercase letters (equivalent to [a-z])        | --           |
| print                | Printable characters (equivalent to [-~], equivalent to [\[:graph:]]) | -- |
| punct                | Punctuation (equivalent to [!-/`:-@`[-`{-~])  | --           |
| space                | Whitespace characters (equivalent to [\t\n\v\f\r]) | --       |
| upper                | Uppercase letters (equivalent to [A-Z])       | --           |
| word                 | Word characters (equivalent to [0-9A-Za-z])   | --           |
| xdigit               | Hexadecimal characters (equivalent to [0-9A-Fa-f]) | --      |

### Table 5-3: Perl Character Classes

|Perl Character Class |Description |UTF8 Support |
|---        |---                                              |---      |
|\C                    | A code unit, suggested to avoid (even in UTF-8 mode) | --         |
|\d                    | Decimal digit (equivalent to [0-9])                | --           |
|\D                    | Non-decimal digit                                   | Supported   |
|\h                    | Horizontal whitespace                                 | --           |
|\H                    | Non-horizontal whitespace                             | Supported   |
|\N                    | Non-newline character                                 | Supported   |
|\s                    | Space character (equivalent to [\t\n\f\r ])       | --           |
|\S                    | Non-space character                                   | Supported   |
|\v                    | Vertical whitespace                                   | --           |
|\V                    | Non-vertical whitespace                               | Supported   |
|\w                    | Word character (equivalent to [0-9A-Za-z])         | --           |
|\W                    | Non-word character                                   | Supported   |
|\R                    | Newline character                                     | --           |
|\X                    | Unicode extended grapheme cluster                    | Supported   |

### Table 5-4: Unicode Classes (General Classes)

|\p and \P Attributes |Description |UTF8 Support |
|---        |---                                      |---      |
|C                    | Other (Other)                                 | Supported    |
|Cc                   | Control Characters (Control)                  | Supported    |
|Cf                   | Format Characters (Format)                     | Supported    |
|Cn                   | Unassigned Characters (Unassigned)            | Supported    |
|Co                   | Private Use Area (Private use)                | Supported    |
|Cs                   | Surrogates (Surrogate)                        | Supported    |
|L                    | Letter Characters (Letter)                     | Supported    |
|Ll                   | Lowercase Letters (Lower case letter)         | Supported    |
|Lm                   | Modifier Letters (Modifier letter)            | Supported    |
|Lo                   | Other Letters (Other letter)                   | Supported    |
|Lt                   | Title Case Letters (Title case letter)        | Supported    |
|Lu                   | Uppercase Letters (Upper case letter)         | Supported    |
|L&                   | Ll, Lu or Lt                                   | Supported    |
|M                    | Mark (Mark)                                   | Supported    |
|Mc                   | Spacing Mark (Spacing mark)                   | Supported    |
|Me                   | Enclosing Mark (Enclosing mark)               | Supported    |
|Mn                   | Non-spacing Mark (Non-spacing mark)           | Supported    |
|N                    | Number (Number)                               | Supported    |
|Nd                   | Decimal Number (Decimal number)               | Supported    |
|Nl                   | Letter Number (Letter number)                 | Supported    |
|No                   | Other Number (Other number)                   | Supported    |
|P                    | Punctuation (Punctuation)                     | Supported    |
|Pc                   | Connector Punctuation (Connector punctuation) | Supported    |
|Pd                   | Dash Punctuation (Dash punctuation)           | Supported    |
|Pe                   | Close Punctuation (Close punctuation)         | Supported    |
|Pf                   | Final Punctuation (Final punctuation)         | Supported    |
|Pi                   | Initial Punctuation (Initial punctuation)     | Supported    |
|Po                   | Other Punctuation (Other punctuation)         | Supported    |
|Ps                   | Open Punctuation (Open punctuation)           | Supported    |
|S                    | Symbol (Symbol)                               | Supported    |
|Sc                   | Currency Symbol (Currency symbol)             | Supported    |
|Sk                   | Modifier Symbol (Modifier symbol)             | Supported    |
|Sm                   | Mathematical Symbol (Mathematical symbol)     | Supported    |
|So                   | Other Symbol (Other symbol)                   | Supported    |
|Xan                  | Union of attributes L and N                   | Supported    |
|Xps                  | POSIX space: attributes Z or tab, NL, VT, FF, CR | Supported |
|Xsp                  | Perl space: attributes Z or tab, NL, VT, FF, CR | Supported |
|Xuc                  | Universal character: can be represented by generic character names | Supported |
|Xwd                  | Perl word: attributes Xan or underscore      | Supported    |
|Z                    | Separator (Separator)                          | Supported    |
|Zl                   | Line Separator (Line separator)                | Supported    |
|Zp                   | Paragraph Separator (Paragraph separator)      | Supported    |
|Zs                   | Space Separator (Space separator)              | Supported    |

### Table 5-5: Unicode Classes (Script Classes)

Note: UTF8 supported.

Adlam, Ahom, Anatolian_Hieroglyphs, Arabic, Armenian, Avestan, 
Balinese, Bamum, Bassa_Vah, Batak, Bengali, Bhaiksuki, Bopomofo, Brahmi, Braille, Buginese, Buhid, 
Canadian_Aboriginal, Carian, Caucasian_Albanian, Chakma, Cham, Cherokee, Chorasmian, Common, Coptic, Cuneiform, Cypriot, Cypro_Minoan, Cyrillic, 
Deseret, Devanagari, Dives_Akuru, Dogra, Duployan, 
Egyptian_Hieroglyphs, Elbasan, Elymaic, Ethiopic, 
Georgian, Glagolitic, Gothic, Grantha, Greek, Gujarati, Gunjala_Gondi, Gurmukhi, 
Han, Hangul, Hanifi_Rohingya, Hanunoo, Hatran, Hebrew, Hiragana, 
Imperial_Aramaic, Inherited, Inscriptional_Pahlavi, Inscriptional_Parthian, 
Javanese, 
Kaithi, Kannada, Katakana, Kayah_Li, Kharoshthi, Khitan_Small_Script, Khmer, Khojki, Khudawadi, 
Lao, Latin, Lepcha, Limbu, Linear_A, Linear_B, Lisu, Lycian, Lydian, 
Mahajani, Makasar, Malayalam, Mandaic, Manichaean, Marchen, Masaram_Gondi, Medefaidrin, Meetei_Mayek, Mende_Kikakui, Meroitic_Cursive, Meroitic_Hieroglyphs, Miao, Modi, Mongolian, Mro, Multani, Myanmar, 
Nabataean, Nandinagari, New_Tai_Lue, Newa, Nko, Nushu, Nyakeng_Puachue_Hmong, 
Ogham, Ol_Chiki, Old_Hungarian, Old_Italic, Old_North_Arabian, Old_Permic, Old_Persian, Old_Sogdian, Old_South_Arabian, Old_Turkic, Old_Uyghur, Oriya, Osage, Osmanya, 
Pahawh_Hmong, Palmyrene, Pau_Cin_Hau, Phags_Pa, Phoenician, Psalter_Pahlavi, 
Rejang, Runic, 
Samaritan, Saurashtra, Sharada, Shavian, Siddham, SignWriting, Sinhala, Sogdian, Sora_Sompeng, Soyombo, Sundanese, Syloti_Nagri, Syriac, 
Tagalog, Tagbanwa, Tai_Le, Tai_Tham, Tai_Viet, Takri, Tamil, Tangsa, Tangut, Telugu, Thaana, Thai, Tibetan, Tifinagh, Tirhuta, Toto, 
Ugaritic, 
Vai, Vithkuqi, 
Wancho, Warang_Citi, 
Yezidi, Yi, 
Zanabazar_Square.

## Matching Control Classes:

### Table 6-1: Matching Rule Control Classes

These matching control options take effect only during a single match and will automatically cancel at the end of the match.

These match options have a higher priority than the respective attributes set by the match_param expression in regexp_like, regexp_count, regexp_instr, regexp_substr, regexp_replace function, for example:

- `SELECT regexp_like('aa','(?i)AA','c') FROM DUAL;` returns TRUE
- `SELECT regexp_like('aa','AA','c') FROM DUAL;` returns FALSE
- The `(?i)` in the pattern has a higher priority than the `c` in match_param

At the same time, matching control options also take effect for the remainder of the pattern that follows, for example: `(a(?i)b)c`

This pattern matches abc or aBc, allowing different settings for options in different parts of the pattern.

|Matching Rule Control Classes |Description |
|---            |---                                 |
|(?i)                          | Case insensitive                           |
|(?J)                          | Allow repeated named groups                |
|(?m)                          | Multi-line mode                           |
|(?n)                          | Disable automatic capturing                |
|(?s)                          | . matches newline                          |
|(?U)                          | Default matching mode is changed to lazy   |
|(?x)                          | Extended: ignore whitespace outside of classes |
|(?xx)                         | Same as (?x), but also ignores spaces and TABs in classes |
|(?-...)                       | Cancel setting                            |
|(?^)                          | Cancel imnsx options                      |

### Table 6-2: Matching Resource Control Classes

The following options can only be recognized at the very start of a pattern or after one of the line break or option syntaxes similar to it. Multiple occurrences are possible.

For the first three, d is a decimal number.

|Matching Resource Control Classes |Description |
| -------------------- | ------------------------------------------------------------ |
| (*LIMIT_DEPTH=d)                  | Set the backtrack limit to d levels (matching uses the system stack for backtracking, this setting can limit the levels of system stack backtracking) |
| (*LIMIT_HEAP=d)                   | Set the heap size limit to d*1024 bytes (default 20000000)        |
| (*LIMIT_MATCH=d)                  | Set the matching limit to d (default 10000000)                    |
| (*NOTEMPTY)                       | Pattern does not match empty string (for example: pattern a?b? matches the empty string at the start; this option makes it ineffective) |
| (*NOTEMPTY_ATSTART)               | Rules are basically the same as above, it only locks empty string matches at the first match position |
| (*NO_AUTO_POSSESS)                | Disable "automatic possession", which is an optimization, for example, converting a+b to a++b to avoid backtracking to an a+ that will never succeed. If you want the matching function to perform a complete unoptimized search and run all annotations, this option can be set, but it is mainly for testing purposes and not recommended for use. |
| (*NO_DOTSTAR_ANCHOR)              | Disable .* anchoring optimization; usage recommendation is the same as (*NO_AUTO_POSSESS), not recommended for use. |
| (*NO_JIT)                         | Disable JIT optimization (YashanDB regex engine does not support JIT mode, this setting will be invalid) |
| (*NO_START_OPT)                   | Disable no-start matching optimization; usage recommendation is the same as (*NO_AUTO_POSSESS), not recommended for use. |
| (*UTF)                           | Set UTF mode for the ongoing match                                   |
| (*UCP)                           | This option has two effects. First, it changes the way PCRE2 handles \B, \b, \D, \d, \S, \s, \W, \w, and some POSIX character classes. By default, only ASCII characters are recognized, but if PCRE2_UCP is set, characters will be classified using Unicode properties. When PCRE2_UCP is set, matching the items it affects takes longer. The second impact of PCRE2_UCP is to enforce the use of Unicode properties for case folding on characters with code points greater than 127, even if PCRE2_UTF is not set. |

### Table 6-3: Line Break Control Classes

These line break control classes will change the rules for recognizing line breaks during matching, with the default line break being LF, which is hex 0A.

The following options can only be recognized at the very start of a pattern or after one of the line break or option syntaxes similar to it. Only one is allowed.

The choice of newline conventions does not affect the interpretation of the escape sequences \n or \r, nor will it affect what \R matches. These have their separate conventions.

|Line Break Control Classes |Description |
|---                  |---                                                                                  |
|(*CR)                      | Only carriage return (hex 0D)                                                       |
|(*LF)                      | Only newline (hex 0A) (default)                                                   |
|(*CRLF)                    | Carriage return + newline (hex 0D + hex 0A)                                       |
|(*ANYCRLF)                 | Any of the above three                                                             |
|(*ANY)                    | Any Unicode newline sequence                                                        |
|(*NUL)                    | NUL character (binary zero)                                                       |
|(*BSR_ANYCRLF)           | CR, LF, or CRLF                                                                     |
|(*BSR_UNICODE)           | Any Unicode newline sequence                                                        |

## Group Classes:

### Table 7-1: Capturing Group Classes

Capturing groups are typically used with backreferences, for example:
1. Pattern (?\<leader>king|queen)\g{leader} matches the string kingking.
   The named group leader matches king, then \g{leader} backreferences the matched king.
2. Pattern (?\<leader>king|queen)\g{leader} does not match the string kingqueen.

The sequence number of capturing groups is arranged according to the order of the left parentheses in the pattern.

Note: Names may contain underscores, ASCII letters, and digits in non-UTF mode; in UTF mode, any Unicode letters and Unicode decimal digits are allowed.
In both cases, names cannot start with a digit.

|Capturing Group Classes |Description |
|---                  |---                                                                                  |
|(...)                     | Capturing group                                               |
|(?\<name>...)              | Named capturing group (Perl)                                   |
|(?'name'...)              | Named capturing group (Perl)                                   |
|(?P\<name>...)            | Named capturing group (Python)                                 |
|(?:...)                   | Non-capturing group                                         |
|(?&#124;...)               | Non-capturing group; resets group number, captures each group in each alternative option |

### Table 7-2: Atomic Group Classes

|Atomic Group Classes |Description |
|---                  |---                                                                                  |
|(?>...)                | Atomic non-capturing group                                     |
|(*atomic:...)         | Atomic non-capturing group                                     |

### Table 7-3: Comment Group Classes

|Comment Group Classes |Description |
|---                  |---                                                                                  |
|(?#....)               | Comment (not nestable)                                         |
|#....                  | Comment (only valid when match_param setting includes "x")    |

## Lookaround Assertions:

### Table 8-1: Lookahead and Lookbehind Assertions

Lookahead assertions can control the match return results to only include part of the matched items, other assertions are similar:
Invoking the regexp_substr function to match the string beijing_2022, you can use the pattern with lookahead assertion "beijing(?=_[1-9]*)" to return only beijing.

Examples of positive lookahead, negative lookahead, positive lookbehind, and negative lookbehind:

1. Pattern `\w(?=;)` matches any word followed by a semicolon, but does not include this semicolon
2. Pattern `\w(?!=;)` matches any word followed by a non-semicolon, but does not include this non-semicolon
3. Pattern `(?<=;)\w` matches any word following a semicolon, but does not include this word
4. Pattern `(?<!;)\w` matches any non-word following a semicolon, but does not include this non-word

Note: Each top-level branch of the lookbehind assertion must have a fixed length.

|Assertion |Description |
|---                        |---                                                                            |
|(?=...)                                        | Positive lookahead                                       |
|(*pla:...)                                     | Positive lookahead                                       |
|(*positive_lookahead:...)                     | Positive lookahead                                       |
|(?!...)                                        | Negative lookahead                                       |
|(*nla:...)                                     | Negative lookahead                                       |
|(*negative_lookahead:...)                     | Negative lookahead                                       |
|(?<=...)                                       | Positive lookbehind                                      |
|(*plb:...)                                     | Positive lookbehind                                      |
|(*positive_lookbehind:...)                    | Positive lookbehind                                      |
|(?<!...)                                       | Negative lookbehind                                      |
|(*nlb:...)                                     | Negative lookbehind                                      |
|(*negative_lookbehind:...)                    | Negative lookbehind                                      |

### Table 8-2: Non-Atomic Lookaround Assertions

|Assertion |Description |
|---                                    |---                                                                |
|(?*...)                                | Non-atomic positive lookahead                               |
|(*napla:...)                           | Non-atomic positive lookahead                               |
|(*non_atomic_positive_lookahead:...)   | Non-atomic positive lookahead                               |
|(?<*...)                               | Non-atomic positive lookbehind                              |
|(*naplb:...)                           | Non-atomic positive lookbehind                              |
|(*non_atomic_positive_lookbehind:...)  | Non-atomic positive lookbehind                              |

## Running Scripts:

### Table 9-1: Running Script Classes

A running script class is a series of characters all from the same Unicode script, such as Latin or Greek.
Using running script classes can be used to detect deceptive attacks, where characters might look identical but originate from different scripts.
The string "paypal.com" is a notorious example, where letters may be a mix of Latin and Cyrillic.
This pattern ensures that characters matching in a sequence of non-whitespace following a whitespace are in the Latin script:

\s+(?=\p{Latin})(*sr:\S+)

|Running Script Classes |Description |
|---                                    |---                                                                |
|(*script_run:...)                  | Script run, can be backtracked                      |
|(*sr:...)                          | Script run, can be backtracked                      |
|(*atomic_script_run:...)           | Atomic script run                                  |
|(*asr:...)                         | Atomic script run                                  |

## References:

YashanDB's regular expression only supports backreferences within patterns, and does not support their use in the replace parameter of regexp_replace function.

### Table 10-1: Backreference Symbols

|Backreference Symbols |Description |
|---                                    |---                                                                |
|\n                                             | Numerical reference (may be ambiguous)            |
|\gn                                            | Numerical reference                               |
|\g{n}                                          | Numerical reference                               |
|\g+n                                           | Relative numerical reference (extended)           |
|\g-n                                           | Relative numerical reference                        |
|\g{+n}                                         | Relative numerical reference (extended)           |
|\g{-n}                                         | Relative numerical reference                        |
|\k\<name>                                     | Numerical reference (Perl)                       |
|\k'name'                                     | Numerical reference (Perl)                       |
|\g{name}                                     | Numerical reference (Perl)                       |
|\k{name}                                     | Numerical reference (NET)                        |
|(?P=name)                                    | Numerical reference (Python)                     |

### Table 10-2: Subroutine Reference Symbols (may be recursive)

|Subroutine Reference Symbols |Description |
|---                                    |---                                                                |
|(?R)                                             | Recursively calls the entire pattern              |
|(?n)                                             | Calls subroutine by absolute number                |
|(?+n)                                           | Calls subroutine by relative number                |
|(?-n)                                           | Calls subroutine by relative number                |
|(?&name)                                        | Calls subroutine by name (Perl)                   |
|(?P>name)                                       | Calls subroutine by name (Python)                 |
|\g\<name>                                       | Calls subroutine by name (Oniguruma)              |
|\g'name'                                       | Calls subroutine by name (Oniguruma)              |
|\g\<n>                                          | Calls subroutine by absolute number (Oniguruma)   |
|\g'n'                                          | Calls subroutine by absolute number (Oniguruma)   |
|\g\<+n>                                         | Calls subroutine by relative number (extended)     |
|\g'+n'                                         | Calls subroutine by relative number (extended)     |
|\g\<-n>                                         | Calls subroutine by relative number (extended)     |
|\g'-n'                                         | Calls subroutine by relative number (extended)     |

## Conditional Patterns:

### Table 11-1: Conditional Pattern Calls

|Conditional Pattern |Description |
|---                                    |---                                                                |
|(?(condition)yes-pattern)                    | If condition meets, match yes pattern, fails to not match  |
|(?(condition)yes-pattern&#124;no-pattern)   | If condition meets, match yes pattern, fails to match no pattern   |

### Table 11-2: Conditional Classes

|Conditional Classes |Description |
|---                                    |---                                                                |
|(?(n)                                     | Absolute reference condition                     |
|(?(+n)                                   | Relative reference condition                      |
|(?(-n)                                   | Relative reference condition                      |
|(?(\<name>)                               | Named reference condition (Perl)                 |
|(?(‘name’)                                | Named reference condition (Perl)                  |
|(?(name)                                 | Named reference condition (deprecated)           |
|(?(R)                                     | Overall recursive condition                       |
|(?(Rn)                                   | Specific numbered group recursive condition       |
|(?(R&name)                               | Specific named group recursive condition          |
|(?(DEFINE)                               | Defines group for reference                        |
|(?(VERSION[>]=n.m)                        | Tests version                                      |
|(?(assert)                               | Assertion condition                              |

## Backtracking Control:

### Table 12-1: Backtracking Control Symbols

The format for all backtracking control verbs can be (*VERB:NAME), for (*MARK), the name is required, other names are optional.

Note:
1. The fourth to eighth items indicate that the corresponding operation is only executed when backtracking reaches them due to subsequent matching failure.
They will force the matching to fail, but they differ in what happens after the matching.
They will only advance the matching start point if the pattern is not anchored.

2. In groups called subroutines, the effect of one of these verbs is limited to the subroutine call.

|Backtracking Control Symbols |Description |
| ------------ | ----------------------------------------------------------- |
| (*ACCEPT)               | Force successful match                          |
| (*FAIL)                 | Force backtrack; synonym (*F)                   |
| (*MARK:NAME)           | Set the name to be passed back; synonym (*:NAME)           |
| (*COMMIT)               | Overall failure, start point does not advance      |
| (*PRUNE)                | Advance to the next starting character          |
| (*SKIP)                 | Advance to earlier (*MARK:NAME) position; if not found, ignore (*SKIP) |
| (*SKIP:NAME)           | Absolute reference condition                      |
| (*THEN)                 | Local failure, return to next alternative              |

## Annotation:

### Table 13-1: Annotation Symbols

Allowed string delimiters are “'”^%\$ (the starting and ending delimiters must be the same), the starting delimiter { must match with the ending delimiter }.
To match the end delimiter in a string, repeat it.

|Annotation |Description |
|---                                    |---                                                                |
|(?C)                                       | Annotation (assuming number 0)                  |
|(?Cn)                                      | Annotated numerical data n                       |
|(?C"text")                               | Annotated with string data                       |