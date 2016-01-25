# OpenSwitch Guidelines for Developing UIs

## Contents

- [Guidelines for CLI help strings](#guidelines-for-cli-help-strings)
- [Guidelines for show command output](#guidelines-for-show-commands-output)
- [Guidelines for log messages](#guidelines-for-log-messages)

This document provides guidelines to follow while developing user-facing interfaces.

## Guidelines for CLI help strings

 1. Verify proper English is used.
 2. The first letter of the first word in each sentence of a help string should be in upper case.
 3. Unless there are acronyms, the rest of the words should be in lower case.
 4. No “.” (dot) at the end of the help string.
 5. Do not wrap lines; let the CLI infra do it uniformly.
 6. Specify units where applicable:
    - KB is a kilobyte.
    - Kb is a kilobit
    - Mb is a megabit
    - MB is a megabyte
    - GB is a gigabyte
    - Gb is a gigabit
 7. Time units should not be shortened; write "seconds" instead of "sec" or "s".
 8. The ps suffix is for “per second”. For example, Gbps stands for gigabit per second.
 9. The pps is a suffix for packets per second. For example, Gpps stands for giga packet per second.
 10. The word "packets" should not be shortened to anything like pkts.
 11. Add the “Default” description in the following form:
    - If it is a help message for a multiple choice configuration, mark the default choice as “(Default)”. For example, “IPv4 (Default)”.
    - If the help message describes a possible value, then add “(Defaut: default_value)”. For example, “Set hostname of the system (Default: switch)”.
 12. Make sure the ERR logs are really errors; move them to a lower level as appropriate.
 13. When adding a new command which uses an already existing token, use the macro of the existing token help string for the starting token.
     For example, all show commands use SHOW_STR macro as the help string for “show” token. If the first token does not have a macro, move the help string to the macro and use it in both places.

The above guidelines should be followed for contextual help strings as well.

## Guidelines for show command output

1. Make sure that singular/plural nouns are always correct. For example, no “5 minute” or “1 minutes”.
2. Verify table alignments:
   - Headings must be aligned to the margin (no space at the beginning).
   - Subsequent lines must be aligned to the margin by a single space; one space must be added to each line.
   - Subsequent lines after headings can have tabs to logically arrange the output.
   - Different rows must be separated by a new line, not a blank line or “--------------------------------“.
   - Headings must be separated from output body by “-----------------------------------------“.
3. Do not include developer level debug information.
4. Shortening time units is allowed. For example, “sec.”, “min.”, “hr.”, “wk.”, or “yr.”.
5. The word packets may be shortened to “pkt.”/”pkts.”.
6. Mind the dots at the end of acronyms.
7. Use an 80 character limit for outputs.
8. Units must be specified, preferably with the heading.
9. Keep one blank line at the end of the display to visually separate the console prompt.
10. For non-tabular outputs use a colon ":" to separate the column name from the value.
11. Align colons ":" as much as possible. Otherwise, separate them into different groups and make it appealing.
12. Do not use “+++++++++++++++++++” or “===================” for borders.
13. If show command output involves the configuration of multiple instances, it should print sorted output in user readable format.


## Guidelines for log messages
OpenSwitch leverages the  VLOG capability of OpenVSwitch as its logging infrastructure. Following are the guidelines when introducing log messages into the OpenSwitch codebase:
 1. Do not use personal references. For example, do not use “I”, “me”, “you”, “he”, “she”, or “they”.
 2. Be short and precise.
 3. Use short intuitive phrases instead of complete sentences: “Wrong hostname” is better than “The hostname is wrong”.
 4. If it is a proper sentence, put a period “.” at the end.
 5. Use correct punctuation; commas, colons, semi-colons, etc.
 6. Shortening follows the rules of help messages.
 7. Do not use special characters like “!” or “?”.
