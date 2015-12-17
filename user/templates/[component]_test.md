<!--

The following are guidelines for writing OPS documentation

See the https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet for additional information about markdown text.
Here are a few suggestions in regards to style and grammar:
* Use active voice. With active voice, the subject is the doer of the action. Tell the reader what
to do by using the imperative mood, for example, Press Enter to view the next screen. See https://en.wikipedia.org/wiki/Active_voice for more information about the active voice.
* Use present tense. See https://en.wikipedia.org/wiki/Present_tense for more information about using the present tense.
* Avoid the use of I or third person. Address your instructions to the user. In text, refer to the reader as you (second person) rather than as the user (third person). The exception to not using the third-person is when the documentation is for an administrator. In that case, *the user* is someone the reader interacts with, for example, teach your users how to back up their laptop.
* See https://en.wikipedia.org/wiki/Wikipedia%3aManual_of_Style for an online style guide.
* Remember to use articles (a, an, and the), see https://owl.english.purdue.edu/owl/resource/540/01/ for more information on when and how to use them.

* The subject is the test case. Explain the actions as if the "test case" is doing them. For example, "Test case configures the IPv4 address on one of the switch interfaces". Avoid the use of first (I) or second person. Explain the instructions in context of the test case doing them.

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

-->

# [Feature|Component] Test Cases #
<!--Provide the name of the grouping of commands, for example, LLDP commands-->

[TOC]
<!-- Remove the TOC tag and replace with an actual table of contents -->

##  [Title for First Test Case] ##
### Objective ###
<!--Describe the objective of the test such that any user would be able ascertain what the test case is attempting to validate -->
### Requirements ###
The requirements for this test case are:
<!-- list as bulleted items of the equipment needed, software versions required, etc. -->
 - [item]
 - [item]
### Setup ###
<!--Describe the topologies and equipment needed to perform this test case. This includes, but is not limited to -->
#### Topology Diagram ####
#### Test Setup ####
### Description ###
<!--Describe the testing scenario which must be executed by the tester. Include enough detail such that the flow and scope of the test is clear. Reference standards or attachments if additional details are required. ->
### Test Result Criteria ###
<!--    Explain the criteria that clearly identifies under whch conditions would the test be considered as pass or fail. Also if the test case can exit with any other result, explain that result and similarly the relevant criteria. -->
#### Test Pass Criteria ####
#### Test Fail Criteria ####

##  [Title for Second Test Case] ##
### Objective ###
<!--Describe the objective of the test such that any user would be able ascertain what the test case is attempting to validate -->
### Requirements ###
The requirements for this test case are:
<!-- list as bulleted items of the equipment needed, software versions required, etc. -->
 - [item]
 - [item]
### Setup ###
<!--Describe the topologies and equipment needed to perform this test case. This includes, but is not limited to -->
#### Topology Diagram ####
#### Test Setup ####
### Description ###
<!--Describe the testing scenario which must be executed by the tester. Include enough detail such that the flow and scope of the test is clear. Reference standards or attachments if additional details are required. ->
### Test Result Criteria ###
<!--    Explain the criteria that clearly identifies under whch conditions would the test be considered as pass or fail. Also if the test case can exit with any other result, explain that result and similarly the relevant criteria. -->
#### Test Pass Criteria ####
#### Test Fail Criteria ####
