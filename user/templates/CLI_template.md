<!--

The following are guidelines for writing OPS documentation

See the https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet for additional information about markdown text.
Here are a few suggestions in regards to style and grammar:
* Use active voice. With active voice, the subject is the doer of the action. Tell the reader what
to do by using the imperative mood, for example, Press Enter to view the next screen. See https://en.wikipedia.org/wiki/Active_voice for more information about the active voice.
* Use present tense. See https://en.wikipedia.org/wiki/Present_tense for more information about using the present tense.
* See https://en.wikipedia.org/wiki/Wikipedia%3aManual_of_Style for an online style guide.
* Avoid the use of I or third person. Address your instructions to the user. In text, refer to the reader as you (second person) rather than as the user (third person). The exception to not using the third-person is when the documentation is for an administrator. In that case, *the user* is someone the reader interacts with, for example, teach your users how to back up their laptop.
* Remember to use articles (a, an, and the), see https://owl.english.purdue.edu/owl/resource/540/01/ for more information on when and how to use them.

Formatting guidelines

Diagrams:
When adding a diagram, make sure that ```ditaa is before the diagram and ``` is after the diagram, as shown in the following graphic.

```ditaa
+----+   +----+
|    +---+    |
+----+   +----+
```

Adding example commands:
When you add an example within a step, it must be indented and proceeded by only one empty line and followed by only one empty line; otherwise the numbering in the procedure will be disrupted. A correct example is shown in the following example:

1. Step 1 Description

 ```
 example here
 ```

2. Step 2 Description

Spacing:
A space must be proceeded after:
- A hash tag in the heading, as in ## My heading
- A bullet, as in – first bullet
- A number, as in 1. First step

CLI tables:
Make sure your tables in the CLI document are properly formatted and contain the required information. The following is example formatting.
| Parameter | Status   | Syntax |               Description          |
|-----------|----------|--------|------------------------|
| interface   | Required | String| The interface name|
| brief   | Optional | Literal | Displays brief information of the interface|

Each parameter in the CLI command should be described, even though it might have been described in a previous command.

-->

# XYZ Heading1 #
<!--Provide the name of the grouping of commands, for example, LLDP commands-->

[TOC]
<!-- Remove the TOC tag and replace with an actual table of contents -->

## XYZ Configuration Commands ##

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

## Show Commands ##

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