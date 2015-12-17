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

# Feature name #
<!--Provide the title of the feature-->

[TOC]
<!-- Remove the TOC tag and replace with an actual table of contents -->

## Overview ##
 <!--Provide an overview here. This overview should give the reader an introduction of when, where and why they would use the feature. -->

## (Optional) Conceptual or reference info here ##
<!--Change heading for conceptual or reference info, such as Prerequisites. -->

## How to use the feature ##

### Setting up the basic configuration

 1. Step 1

### Setting up the optional configuration

 1. Step 1

### Verifying the configuration

 1. Step 1

### Troubleshooting the configuration

#### Condition
Type the symptoms for the issue.

#### Cause
Type the cause for the issue.

#### Remedy
Type the solution.

## CLI ##
<!--Provide a link to the CLI command related to the feature. The CLI files will be generated to a CLI directory.  -->
Click [here](https://openswitch.net/cli_feature_name.html#cli_command_anchor) for the CLI commands related to the named feature.

## Related features ##
<!-- Enter content into this section to describe features that may need to be considered in relation to this particular feature, under what conditions and why.  Provide a hyperlink to each related feature.  Sample text is included below as a potential example or starting point.  -->
When configuring the switch for FEATURE_NAME, it might also be necessary to configure [RELATED_FEATURE1](https://openswitch.net./tbd/other_filefeatures/related_feature1.html#first_anchor) so that....

