<!--

The following are guidelines for OPS documentation

See the https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet for additional information about markdown text.
Here are a few suggestions in regards to style and grammar:
* Use active voice. With active voice, the subject is the doer of the action. Tell the reader what
to do by using the imperative mood, for example, Press Enter to view the next screen. See https://en.wikipedia.org/wiki/Active_voice for more information about the active voice.
* Use present tense. See https://en.wikipedia.org/wiki/Present_tense for more information about using the present tense.
* See https://en.wikipedia.org/wiki/Wikipedia%3aManual_of_Style for an online style guide.
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

-->

# High level design of FEATURE #

High level description of a FEATURE design.

If the feature is essentially composed out of a single daemon (don’t count OVSDB-Server and management interfaces, unless they have some very special role in the feature design), then just reference to a DESIGN.md document of the repo that contains the daemon. Otherwise continue to the next sections.

## Design choices ##

Discuss any design choices that were made.

## Participating modules ##

``` ditaa
Put ascii block diagram in the format of http://ditaa.sourceforge.net/
include relationships of all participating modules including OVSDB-Server.
You can use http://asciiflow.com/ or any other tool to generate the diagram.
```

Explain all interactions between the modules that sum up to the feature functionality. Add flow diagrams as appropriate.

## OVSDB-Schema ##

Discuss which tables/columns this feature interacts with. Where it gets the configuration, exports statuses and statistics. Reference specific modules DESIGN.md documents for further information on the schema.

## Any other sections that are relevant for the module ##

## References ##

* [Reference 1](http://www.openswitch.net/docs/redest1)
* ...

Include references to DESIGN.md of any module that participates in the feature.
Include reference to user guide of the feature.