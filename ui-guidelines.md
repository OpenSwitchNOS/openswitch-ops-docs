# OpenSwitch guidelines for implementing UIs

This document provides guidelines to follow while developing different user-facing interfaces.

## Guidelines for CLI help strings

 1. Verify proper English
 2. First letter of the first word in each sentence in the help message should be in upper case
 3. Unless there are acronyms, the rest should be in lower case.
 4. No “.” (dot) at the end
 5. Don’t wrap lines. Let CLI infra do it uniformly
 6. Specify units where applicable
    - KB is a kilobyte.
    - Kb is a kilobit
    - Mb is a megabit
    - MB is a megabyte
    - GB is a gigabyte
    - Gb is a gigabit
 7. Time units should not be shortened, e.g., write "seconds" instead of "sec" or "s"
 8. ps suffix is for “per second” - for example Gbps - giga bit per second
 9. pps is a suffix for packets per second - for example Gpps - giga packet per second
 10. "packets" should not be shortened to anything like pkts.
 11. Add “Default” description in the following form:
    - If it’s a help message on a multiple choice configuration just mark the default choice as “(Default)” - example “IPv4 (Default)”
    - If help message describes a possible value, then add “(Defaut: default_value)”, example “Set hostname of the system (Default: switch)”
 12. Make sure that ERR logs are really errors, move to lower level as appropriate

## Guidelines for `show` commands output

1. Make sure that singular/plural is always correct - no “5 minute” or “1 minutes”
2. Verify table alignments
3. No developer level debug information
4. Time shortening is allowed - “sec.” “min.” “hr.” “wk.” “yr.”
5. Packets may be shortened to “pkt.”/”pkts.”. 
6. Mind the dots at the end of the acronyms


## Guidelines for log messages
OpenSwitch leverages VLOG capability of OpenVSwitch as its logging infrastructure. Here are the guidelines to follow while introducing log messages in OpenSwitch codebase.

 1. No personal references - no “I”, “me”, “you”, “he”, “she”, “they”
 2. Be short and precise.
 3. Use short intuitive phrases instead of complete sentences: “Wrong hostname” is better that “The hostname is wrong”
 4. If it’s a proper sentence, then put “.” (dot) at the end.
 5. Put correct punctuation - commas, colons, semi-colons etc.
 6. Shortening follows the rules of help messages



