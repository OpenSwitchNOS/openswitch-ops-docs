<!--
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
============

<!--Provide the name of the grouping of commands, for example, LLDP commands-->

[TOC]

XYZ Configuration Commands
--------------------------
### First Command ###

#### Syntax ####
<!-- For example,    command [optional parameter] <user-input> [choose|one] -->

#### Description ####
<!-- Provide a description of the command. -->

#### Authority ####
<!-- Provide who is authorized to use this command, such as Super Admin or all users. -->

#### Parameters ####
<!-- Provide for the parameters for the command. -->

| Parameter | Status   | Syntax         | Description                           |
|:-----------|:----------|:----------------:|:---------------------------------------|
| *arg1* | Required | ... | arg1 descr |
| *arg2* | Required | ... | arg2 descr |

#### Examples ####
<!--    myprogramstart -s process_xyz-->

```
switch(config)#feature enable arg1 arg2
```

### Second Command ###
<!--Change the value of the anchor tag above, so this command can be directly linked. -->

### First Command ###

#### Syntax ####
<!-- For example,    command [optional parameter] <user-input> [choose|one] -->

#### Description ####
<!-- Provide a description of the command. -->

#### Authority ####
<!-- Provide who is authorized to use this command, such as Super Admin or all users. -->

#### Parameters ####
<!-- Provide for the parameters for the command. -->

| Parameter | Status   | Syntax         | Description                           |
|:-----------|:----------|:----------------:|:---------------------------------------|
| *arg1* | Required | ... | arg1 descr |
| *arg2* | Required | ... | arg2 descr |

#### Examples ####
<!--    myprogramstart -s process_xyz-->

```
switch(config)#feature enable arg1 arg2
```

Display Commands
----------------

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

| Parameter | Status   | Syntax         | Description                           |
|:-----------|:----------|:----------------:|:---------------------------------------|
| *arg1* | Required | ... | arg1 descr |
| *arg2* | Required | ... | arg2 descr |

#### Examples ####
<!--    myprogramstart -s process_xyz-->

```
switch(config)#feature enable arg1 arg2
```

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

| Parameter | Status   | Syntax         | Description                           |
|:-----------|:----------|:----------------:|:---------------------------------------|
| *arg1* | Required | ... | arg1 descr |
| *arg2* | Required | ... | arg2 descr |

#### Examples ####
<!--    myprogramstart -s process_xyz-->

```
switch(config)#feature enable arg1 arg2
```
