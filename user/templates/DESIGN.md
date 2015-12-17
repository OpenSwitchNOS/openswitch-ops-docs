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

# High level design of OPS-FOO #
High level description of OPS-FOO design.

## Responsibilities ##
Discuss module responsibilities

## Design choices ##
Discuss any design choices that were made.

## Relationships to external OpenSwitch entities ##

```ditaa
Put ascii diagram in the format of http://ditaa.sourceforge.net/
include relationship to ovsdb-server, ops-appctl if available and any other relationships
You can use http://asciiflow.com/ or any other tool to generate the diagram.
```

Provide detailed description of relationships and interactions.

```ditaa
+---------------+            +---------------+             +---------------+
|               |            |               |             |               |
|   ops-[xxx]   <----------->+     OVSDB     <-------------+   ops-[xxx]   |
|               |            |               |             |               |
+---------------+            +---------------+             +---------------+
```

## OVSDB-Schema ##

Discuss which tables/columns this module is interested in. Where it gets the configuration, exports statuses and statistics. Anything else which is relevant to the data model of the module.

## Internal structure ##

Put diagrams and text explaining major modules, threads, data structures, timers etc.

## Any other sections that are relevant for the module ##


## References ##

* [Reference 1](http://www.openswitch.net/docs/redest1)
* ...

Include references to any other modules that interact with this module directly or through the database model. For example, CLI, REST, etc.
ops-fand might provide reference to ops-sensord, etc.
