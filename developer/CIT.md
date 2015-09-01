#CIT (Continuos Integration Testing)

## Git and Gerrit
[Git](https://www.gerritcodereview.com/) and [Gerrit](http://code.google.com/p/gerrit/) are used as the versioning control and review management systems for OpenSwitch.

Contributors initially clone the repo that they want to work with to a local Git repository and once they're satisfied with the changes made, the patchset is sent with the `git review` command to Gerrit for review and validation.

The Gerrit user interface can be accessed via its link: https://review.openswitch.net/

## Jenkins and Zuul
[Jenkins](https://jenkins-ci.org/) and [Zuul](http://docs.openstack.org/infra/zuul/) are used as the Continuos Integration, Scheduling and Gating systems for OpenSwitch.

Zuul manages pipelines. A pipeline is configured to run a series of tests. Zuul schedules and load balances the tests. Jenkins is the Continuous Integration system that actually runs them.

Currently the tests configured are:

1. `Check`: Newly uploaded patchsets enter this pipeline to receive an initial **+/-1** `Verified` vote from Jenkins.
2. `Gate`: Changes approved by core developers are enqueued in order in this pipeline, and if they pass tests in Jenkins, will be merged.
3. `Periodic`: Jobs in this queue are triggered on a timer.

Zuul is capable of provisioning Jenkins servers on demand so that more tests can be run during spikes.

If the tests don't pass, the CIT system adds a **-1** vote with a comment to the change review. The contributor can then make changes, add a comment and send the test again for validation as seen below.

For tests that compile source code, only the source code needed for the test is compiled as opposed to the entire project.

The Jenkins and Zuul user interfaces can be accessed by their respective links: https://jenkins.openswitch.net/ and http://zuul.openswitch.net/

## How to review code in Gerrit
The steps to perform code revies using Gerrit are the following:

* Log in to https://review.openswitch.net/
* Click on the `Review button`
* Provide a message as well as a vote (-2, -1, 0, +1, +2)
	* Only Core Reviewers can vote ** +/-2**
	* It is also possible to leave messages for specific source code lines.

## CIT workflow

After a contributor has cloned a project and submitted a patch set using `git-review`, the change goes through a validation process in the Continuous Integration workflow. The workflow can be observed in the following diagram.

![CIT Workflow](./images/CIT-workflow.png "CIT Workflow")

The current status for all tests currently scheduled can be seen at the [Zuul](http://zuul.openswitch.net/) status page.

### Approval process

1. Jenkins runs the `Check tests` for newly updated patchsets. The `Check tests` include actions like compiling the changes and making sure that the image does not crash.
2. Jenkins reports the results of the tests by leaving a comment and a vote in the `Verified` category. Jenkins votes can be **+/-1** for a succesful/unsucessful test. The results are added to Gerrit with a user called `Zuul`.
3. The Core Reviewers provide  **+/-2** votes for the changes in the `Code Review` category.
4. Once that the change has been voted by a Core Reviewer with a **+2** vote and Jenkins has confirmed that all tests pass with **+1**, the owner approves the change by giving a **+1** vote in the `Workflow` category. **Change owners should only do this after validating that the changes are desired as this is the last checkpoint before Zuul schedules the change to the merge process.**

### Requirements for merging
The following votes needs to be given to the change before it is attempted to be merged:
* A +2 vote in `Code Review` from a Core Reviewer.
* A +1 vote in `Verified` from Jenkins.
* A +1 vote in `Workflow` (**Approved**) from the Change Owner.


**NOTES:**
* At any time, any developer can review the code changes and leave a message as well as a vote (**+/-1**).
* The change can be `Abandoned` in the Gerrit review site.
* A green checkmark in Gerrit indicates that the change has met the review requirements for that category.

### Merge process

1. Jenkins sees the approved change and it starts the `Gate tests` before merging.
2. If the tests pass, the change get merged into the project, if the `Gate tests` tests fail, a message is left by Jenkins and the contributor needs to take action.

### Process feedback
If the tests failed or feedback is received from the community or the Core Reviewers, the contributor should have followed the process described in the [How to contribute](./how-to-contribute.html) guide to amend the changes and re-submit them for review.

1. The contributor makes the changes and commits them with the `git commit --amend` command so that Gerrit can process them as part of the same Change Request (another patch set within the same change).
2. The contributor sends the changes back for review with `git review`
3. The same process described above for Approval follows.
4. If the `Gate tests` pass, the change is merged into the code base.


## Architecture
The following diagram describes how the different servers integrate with each other.

![CIT Architecture](./images/CIT-architecture.png "CIT Architecture")