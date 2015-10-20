
Defects management process
=======

Defects in software are considered errors or bugs that create a discrepancy between actual and expected results of the system functionality.

If you find a defect while using the OpenSwitch, you can contribute to the quality of the software by reporting it. This section describes the defect management process that must be followed to report and manage defects in the OpenSwitch project.
- [Defect workflow](#defect-workflow)
    - [1. Defect submission](#1-defect-submission)
	   - [How to submit a new defect](#how-to-submit-a-new-defect)
		- [Defect status](#defect-status)
	- [2. Engineer assigment](#2-engineer-assigment)
		- [Defect status](#defect-status)
    - [3. Defect resolution](#3-defect-resolution)
		- [Defect status](#defect-status)
	- [4. Defect closure](#4-defect-closure)

## Defect workflow

In most cases, defects follow the flow described below. However, exceptions to this regular flow may occur that require special handling.

The details for  managing OpenSwitch project defects is explained in the following sections.

![Defect Management Flow](https://lh3.googleusercontent.com/-DYjRh0qJUoM/VgRZgOR1kXI/AAAAAAAAAL8/zTaAb404-uQ/s0/defect-workflow.jpg "defect-workflow.jpg")

The description of each phase of the Defect workflow follows:

### 1. Defect submission

Once a defect is discovered, it can be submitted into the [OpenSwitch project in Taiga ](https://tree.taiga.io/project/openswitch/issues?page=1).
Use the following guidelines to successfully submit and manage a defect within [Taiga](https://tree.taiga.io/project/openswitch/issues?page=1).

#### How to submit a new defect


##### Defect status

**New:** Every newly introduced defect, must have the status in the **New** state. This state indicates a new defect has been entered in the tool and requires attention. The ownership of the new defect must be set to the **Component/Feature owner**.

### 2. Engineer assigment

Submitted defects are assigned to engineers in charge of starting a review process. The defect information provided by submitters is crucial in this phase because it  allows engineers to get an understanding of the issue, perform a root cause analysis, and if possible, implement a fix for the bug.

#### Defect status

**Open:** The Component/Feature owner sets the defect to **Open** when the review of the defect begins. In this state, the Component/Feature owner does an initial triage of the defect to determine if the defect has all the required information to allow an engineer to begin a root cause analysis.

**Assigned:** After the Component/Feature owner determines that the defect contains everything that is needed to perform a root cause analysis, an engineer can then be assigned as Owner so that the root cause analysis begins.

**More Info:** If the Component/Feature owner determines that there is not enough information to proceed with the defect investigation, the defect state is set to **More Info** and sent back to the submitter. Once pending information is provided, Status & Owner should be set back to **Assigned** and **Engineer**.

**Blocked:** This status is set by the assigned engineer when there are external factors preventing the engineer from moving forward with the fix. The status remains as **Blocked** until the engineer clears the external blocking factors.


### 3. Defect resolution

Once all the required information has been provided and a root cause analysis has been conducted, the engineer assigned concludes with the resolution of the defect, so that the defect reaches a resolved state.

#### Defect status

**Skip-fix:** The engineer has completed the investigation and recommends not to fix the defect.  This status could be transitioned at a later time later to either deferred or to a closed state.

**Duplicate:** A defect is considered as Duplicate when there is a defect with the same root cause already filed in [Taiga](https://tree.taiga.io/project/openswitch/issues?page=1).

**Invalid-bug:** This status means the submitted defect is not a valid issue. This status is used when the defect entered reflects an expected behavior, is using wrong assumptions, is an enhancement, or is referring to unsupported functionality.

**Unreproducible:** This status is assigned when there is no way to replicate the issue, making it impossible to fix. In this case, it is possible the submitter did not have all the information required to replicate the behavior or the described issue was a rare event that may not happen again.

**Fixed:** This is the terminal state and it means that defect has been resolved by the engineer.

### 4. Defect closure

When the engineer reaches a resolution for the submitted defect and all the proposal actions have been completed, the process is considered  closed.
