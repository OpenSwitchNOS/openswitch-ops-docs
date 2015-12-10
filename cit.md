# CIT (Continuous Integration Testing)

## Git and Gerrit
[Git](https://www.gerritcodereview.com/) and [Gerrit](http://code.google.com/p/gerrit/) are used as the version control and review management systems for OpenSwitch.

If you are a contributor, you must initially clone the repository you need to a local Git repository. Once you are satisfied with the changes made, the patch set is sent with the `git review` command to Gerrit for review and validation.

The Gerrit user interface can be accessed via this [link](https://review.openswitch.net/)

## Jenkins and Zuul
[Jenkins](https://jenkins-ci.org/) and [Zuul](http://docs.openstack.org/infra/zuul/) are used as the Continuous Integration, Scheduling and Gating systems for OpenSwitch.

Zuul manages pipelines. A pipeline is configured to run a series of tests. Zuul compiles code and load balances tests for Jenkins to run. Zuul listens for events from Jenkins and processes the results of the tests.

Currently the pipelines configured are:

* `Check`: Newly uploaded patch sets enter this pipeline to receive an initial **+/-1** `Verified` vote from Jenkins. This pipeline make sure the new changes compile.
* `Gate`: Changes approved by core developers are queued in order in this pipeline, and if they pass tests in Jenkins, will be merged. The test run for this pipeline depends on each module.
* `Periodic`: Jobs in this queue are triggered on a timer. The pipeline consists on a complete set of test that run regularly to assure the repository is stable.

If the tests do not pass, the CIT system adds a **-1** vote with a comment to the change review. If you want the tests to run again, do one of the following to make Zuul re-schedule the tests:

**Using `git`:**
* Make the changes needed and commit them with the `--amend` switch (`git commit --amend`) so that they're added to the same change review. The new changes shows up as a second (or more) patch set under the same Change Review in Gerrit.

**Using Gerrit:**
* If you want the tests to be run again without making any modifications to the patch set (for example, the failure was not with the code but with Zuul or something else that was fixed separately), you might want to simply add a comment to the patch set with the word `recheck` in it.

**NOTE**: For jobs that compile source code, only the source code needed for the job is compiled as opposed to the entire project.

The Jenkins and Zuul user interfaces can be accessed by their respective links: [Jenkins](https://jenkins.openswitch.net/) and [Zuul](http://zuul.openswitch.net/)

## CIT workflow

After you have cloned a project and submitted a patch set using `git-review`, the change goes through a validation process in the Continuous Integration workflow. The following diagram shows a typical workflow:

![CIT Workflow](/img/CIT-workflow.png "CIT Workflow")

The current status for all tests currently scheduled or running can be seen at the [Zuul](http://zuul.openswitch.net/) status page.

### Requirements for merging
The following votes needs to be given to the change before it is attempted to be merged:
* A **+2** vote in `Code Review` from a Core Reviewer.
* A **+1** vote in `Verified` from Jenkins.
* A **+1 (Approved)** vote in `Workflow` from the Change Owner.

### Approval process

1. Jenkins runs the `Check` tests for newly updated patch sets.
2. Jenkins reports the results of the tests by leaving a comment and a vote in the `Verified` category. Jenkins votes can be **+/-1** for a succesful/unsucessful test. The results are added to Gerrit with a user called `Zuul`.
3. The Core Reviewers provide  **+/-2** votes for the changes in the `Code Review` category.
4. Once that the change has been voted by a Core Reviewer with a **+2** vote and Jenkins has confirmed that all tests pass with **+1**, the owner approves the change by giving a **+1 Approved** vote in the `Workflow` category. **Change owners should only do this after validating that the changes are desired as this is the last checkpoint before Zuul schedules the `Gate` pipeline prior to the merge process.**


**NOTES:**
* Any developer can review the code changes and leave a message as well as a vote (**+/-1**).
* The change can be `Abandoned` in the Gerrit review site.
* A green checkmark in Gerrit indicates that the change has met the review requirements for that category.

### Merge process
1. Once the change has been approved, Zuul schedules the `Gate` pipeline before merging.
2. If the tests pass, the change gets merged into the project, if the `Gate` tests fail, a message is left by Jenkins and the contributor needs to take action.

### Process feedback
If the tests failed or feedback is received from the community or the Core Reviewers, the contributor should have followed the process described in the [How to contribute](contribute-code) guide to amend the changes and re-submit them for review. In a nutshell:

1. The contributor makes the changes and commits them with the `git commit --amend` command so that Gerrit can process them as part of the same Change Request (another patch set within the same change).
2. The contributor sends the changes back for review with `git review`
3. The same process described above for Approval follows.
4. If the `Gate` tests pass, the change is merged into the code base.


## Architecture
The following diagram describes how the different servers integrate with each other.

![CIT Architecture](/img/CIT-architecture.png "CIT Architecture")
