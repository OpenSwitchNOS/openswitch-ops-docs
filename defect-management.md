
Defects Management Process
=======

Defects in software are considered errors or bugs that create a discrepancie between actual and expected results of the system functionalities.

If a defect was found while using the OpenSwitch, you as a community member, can contribute to the quality of the software by reporting it. This section describes the defect management process that must be followed in order to report and manage defects in the OpenSwitch project.

# Table of Content

- [Defect Workflow](#defect-workflow)
  - [1. Defect Submission](#1-defect-submission)
    - [*How to submit a new defect*](#how-to-submit-a-new-defect)
    - [Defect Status](#defect-status)
  - [2. Engineer Assigment](#2-engineer-assigment)
    - [Defect status](#defect-status)
  - [3. Defect Resolution](#3-defect-resolution)
      - [Defect status](#defect-status)
  - [4. Defect closure](#4-defect-closure)


## Defect Workflow

In most cases, defects will follow the flow described below. However, exceptions to this regular flow may occur, hence special handling may be required.

The detail on how to manage a defect in the OpenSwitch project will be explained on the following sections.

![Defect Management Flow](https://lh3.googleusercontent.com/-DYjRh0qJUoM/VgRZgOR1kXI/AAAAAAAAAL8/zTaAb404-uQ/s0/defect-workflow.jpg "defect-workflow.jpg")

> Find below the description of each phase of the Defect Workflow.

### 1. Defect Submission

Once a defect is discovered, it can be submitted into the [OpenSwitch project in Taiga ](https://tree.taiga.io/project/openswitch/issues?page=1).
Use the following guidelines to successfully submit and manage a defect within [Taiga](https://tree.taiga.io/project/openswitch/issues?page=1).

#### *How to submit a new defect*


##### Defect Status

**New:** Every newly introduced defect, must set the status in "New" state. This state will indicate that a new defect has been entered in the tool and attention is required. The ownership of the new defect must be set to the Component/Feature owner

### 2. Engineer Assigment

Submitted defects will be assigned to engineers in charge of starting a review process.
The defect information provided by submitters will be crucial in this phase because it will allow engineers to get an understanding of the issue, do a root cause analysis and, if possible, implement a fix for the bug.

#### Defect status

**Open:** The Component/Feature owner will set the defect to "Open", when the review of the defect begins. During this state, the Component/Feature owner will do an initial triage of the defect and will determine if the defect has all the required information to allow an engineer to start root causing it.

**Assigned:** After the Component/Feature owner determines that the defect contains everything that is needed to root cause it, she will proceed to assign an engineer as Owner so that the root cause analysis begins.

**More Info:** In case the Component/Feature owner determines that there not enough information has been provided to proceed with defect investigation, the defect state will be set to "More Info" and send back to the submitter. The reasons for this states could be that the engineer do not have the required information to reproduce the issue or relevant defect information is pending.  

Once pending information is provided Status & Owner should be set back to *Assigned* and *Engineer*.  

**Blocked:** The Blocked status is set by the assigned engineer when there are external factors preventing the engineer to move forward with the fix, the bug is a parent waiting for all child to get fixed in all relevant branches or the engineer is awaiting another fix to happen before continuing with the fix process.


### 3. Defect Resolution

Once all the required information has been provided and a root cause analysis has been conducted, the engineer assigned will conclude with the resolution of the defect. It means that the defect reaches a resolved state.

#### Defect status

**Skip-fix:** The engineer has completed the investigation and recommends not to fix the defect.  This status could be transitioned later on to either deferred or to a closed state.

**Duplicate:** A defect will be considered as Duplicate, when there is a defect with the same unexpected behavior already filed in a [Taiga](https://tree.taiga.io/project/openswitch/issues?page=1) ticket.

**Invalid-bug:** This status means the submitted defect is not a valid issue. This status is used when the defect entered reflects an expected behavior, is using wrong assumptions, is an enhancement or is referring to an unsupported functionality.

**Unreproducible:** This status is assigned when there is no way to replicate the issue so it makes it impossible to fix. It could be that the submitter did not have all the information required to replicate the behavior or it could be that the described issue was a rare event that may not happen again.

**Fixed:** This is the terminal state and it means that defect has been resolved by the engineer.

### 4. Defect closure

When the engineer has reached a resolution for the submitted defect and all the proposal actions has been completed, the process will be considered as closed.
