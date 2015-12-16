<!--  See the https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet for additional information about markdown text.
Here are a few suggestions in regards to style and grammar:
* Use active voice. With active voice, the subject is the doer of the action. Tell the reader what
to do by using the imperative mood, for example, Press Enter to view the next screen. See https://en.wikipedia.org/wiki/Active_voice for more information about the active voice.
* Use present tense. See https://en.wikipedia.org/wiki/Present_tense for more information about using the present tense.
* Avoid the use of I or third person. Address your instructions to the user. In text, refer to the reader as you (second person) rather than as the user (third person). The exception to not using the third-person is when the documentation is for an administrator. In that case, *the user* is someone the reader interacts with, for example, teach your users how to back up their laptop.
* See https://en.wikipedia.org/wiki/Wikipedia%3aManual_of_Style for an online style guide.
Note regarding anchors:
--StackEdit automatically creates an anchor tag based off of each heading.  Spaces and other nonconforming characters are substituted by other characters in the anchor when the file is converted to HTML.

The following is a proposed syntax for the CLI command references (in the Syntax section below)

[] for an optional parameter
<> for a parameter to be replaced with user input
| for a choose one option

i.e
[<optional-user-input>] for an optional parameter that is an user input variable too
    e.g enable keepalive [<retries>]
    where retries is optional and takes a number between 1-10

[optional-literal-word] for an optional parameter that takes a literal expression
<non-optional-user-input> for non optional parameter
    e.g ip address <A.B.C.D/M> [secondary]
    where <A.B.C.D/M> must be entered with user-input
    and secondary is optional but must be entered literaly

option-a | option-b for “choose a or b”
    e.g enable mode normal|fast
    where normal or fast must be entered
-->
XYZ Heading1
=======

<!--Provide the name of the grouping of commands, for example, LLDP commands-->

 [TOC]

## XYZ Configuration Commands ##
<!-- Change LLDP -->
###  First command ###
#### Syntax ####
<!--For example,    myprogramstart [option] <process_name> -->
#### Description ####
<!--Provide a description of the command. -->
#### Authority ####
<!--Provide who is authorized to use this command, such as Super Admin or all users.-->
#### Parameters ####
<!--Provide for the parameters for the command.-->
#### Examples ####
<!--    myprogramstart -s process_xyz-->
### Second command ###
<!--Change the value of the anchor tag above, so this command can be directly linked. -->
#### Syntax ####
<!--For example,    myprogramstart [option] <process_name> -->
#### Description ####
<!--Provide a description of the command. -->
#### Authority ####
<!--Provide who is authorized to use this command, such as Super Admin or all users.-->
#### Parameters ####
<!--Provide for the parameters for the command.-->
#### Examples ####
<!--    myprogramstart -s process_xyz-->

##Display Commands ##
### First command ###
<!--Change the value of the anchor tag above, so this command can be directly linked. -->
#### Syntax ####
<!--For example,    myprogramstart [option] <process_name> -->
#### Description ####
<!--Provide a description of the command. -->
#### Authority ####
<!--Provide who is authorized to use this command, such as Super Admin or all users.-->
#### Parameters ####
<!--Provide for the parameters for the command.-->
#### Examples ####
<!--    myprogramstart -s process_xyz-->
###Second command ###
<!--Change the value of the anchor tag above, so this command can be directly linked. -->
#### Syntax ####
<!--For example,    myprogramstart [option] <process_name> -->
#### Description ####
<!--Provide a description of the command. -->
#### Authority ####
<!--Provide who is authorized to use this command, such as Super Admin or all users.-->
#### Parameters ####
<!--Provide for the parameters for the command.-->
#### Examples ####
<!--    myprogramstart -s process_xyz-->

