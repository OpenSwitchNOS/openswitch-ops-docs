# OPS Release Process
- [Overview] (#overview)
    - [OPS Release Cycle Timeline Targets] (#ops-release-cycle-timeline-targets)
        - [Release cycle start] (#release-cycle-start)
        - [Readiness Review] (#readiness-review)
        - [Release Branch Pull] (#release-branch-pull)
        - [Release Branch Quality Checkpoint] (#release-branch-quality-checkpoint)
        - [Community Release Ready] (#community-release)
- [Release Management] (#release-management)
    - [Versioning Strategy] (#versioning-strategy)
    - [OpenSwitch Release Branch Management] (#openswitch-release-branch-management]
    - [Module Maintainer Responsibilities] (#module-maintainer-responsibilities)
    - [Repository Release Readiness] (#repository-release-readiness)
    - [Working With Feature Branches] (#working-with-feature-branches)
- [Criteria For Completeness] (#criteria-for-completeness)
    - [Criteria to Submit code to Master Branch] (#criteria-to-submit-code-to-master)
    - [Criteria for inclusion in Release Branch (Feature Completeness)] (#criteria-for-inclusion-in-release-branch)
    - [Templates and Examples]
    - [Checklist to enable smoothest documentation Gerrit review] (#checklist-to-enable-smoothest-documentation-gerrit-review)


## Overview
OpenSwitch (OPS) uses a time-based release model, operating on ~ a 13 week cycle target. The dates for the milestones and final release in a given development cycle are defined by the Release Management team, and communicated before the new development cycle starts. There is a single release branch where a release is supported by the community until the next release is triggered. Only Critical and High severity defects are permitted to be fixed in the release branch to minimize rate of change during the release. Medium and low severity defect fixes are to be fixed on the master branch for inclusion in the next release.

### OPS Release Cycle Timeline Targets
#### Release cycle start
ops-build repository master branch is open for commits and defect fixes and the master branch version is incremented.

#### Readiness Review
Release cycle start + 11 weeks

The release manager will review readiness for all planned features with the feature owners at this time. Ahead of the Readiness Review, feature owners should review their approved proposals and for any items not considered ready for this review, proactively move those items into a future release window. Community members will identify those features that meet the full "Criteria for Completeness" (see below) and are ready for inclusion in the current release. Feature leads and repo owners must make sure that all the modified repos for a given feature are in a state where the completed and approved features for a given release can be clearly marked for inclusion in the release branch.

#### Release Branch Pull
Release cycle start + 13 weeks

The release branch is pulled every ~ 13 weeks for each repository on OpenSwitch (OPS). The master branch starts on the new release/development cycle (see Release Cycle Start above). The release notes creation process will trigger at this time. This process is to be defined. Feature owners are encouraged to drive closure on all Critical and High severity defects applying to content on the release branch to enable successful pass of the Quality Checkpoint (see below).

#### Release Branch Quality Checkpoint
Release Branch Pull + 2 weeks (release branch):

For the release to occur, all Critical and High severity defects identified as critical to the release must be fixed for supported features. Potential new release critical & high severity issues are first to get fixed on the release branch, and then are to be merged to the master branch. Periodic builds and release candidate builds on the release branch and additional quality checkpoints will be used if necessary to achieve the required quality.

#### Community Release Ready
Successful Quality Checkpoint + 1 week (release branch)

One week after the quality checkpoint there will be a one week code freeze (CF) on the release branch [ops-build becomes read only], and the end of that week the release will occur. On final release day we will set the release branch build version X.Y.Patch+1 and the release branch [ops-build] is no longer frozen/read-only.

## Release Management
### Versioning Strategy
Given a version number MAJOR.MINOR.PATCH, increment the:
 1. MAJOR version for major feature completion milestones or break backwards compatibility
 2. MINOR version when introduce a new feature set
 3. PATCH version bug fixes.

### OpenSwitch Release Branch Management
 - Release branch is pulled ~ every 13 weeks for each repository on OpenSwitch
 - Only Critical and High severity defects can be Merged/Committed to release branch
     - These defect fixes can be sync’d to master on need basis by the repo maintainers.
     - C/H defects are to be submitted to the release branch, and then merged to master
 - Medium and Low Severity defects should go into master for subsequent release branch
 - Release branch must have fully completed features and its related documentation
     - Release branch cannot have partial code or code that does not satisfy Definition of Done and Feature Readiness criteria
     - Feature Leads and Repo owners make sure that all the modified repos for a feature are in a state where the completed features can be clearly marked for release branch

### Module Maintainer Responsibilities
 - Overall quality/usability/condition and roadmap of the repositories
 - Versioning of individual repositories (based on semantic versioning semver.org) (Training/guidelines will be provided)
 - Decisions on when to sync release->master to absorb C/H defects into master
 - What SHA is included in ops-build for the repo during Feature Freeze (FF) for release branch pull
     - This will include making sure that release branch does not get any partial features in the release branch
 - Ensure maximum test coverage for new code in the repository (may involve working with Feature Leads)

### Repository Release Readiness
- Every repo should be able to identify a SHA (ChangeID) in their master branch git log which will have only completed features and no partial features.
- The above SHA will be used for subsequent release branch.
- Feature leads should work with module maintainers to plan this out so that all the repositories that were modified are in a “release-ready” state

### Working With Feature Branches
 1. Identify all the repositories that will be modified for feature development
 2. Create feature branch with the same name on all these repositories
 3. Additionally create feature branch with the same name for ops-build and ops repositories.
     - This is needed to make sure that your branch is protected from active work on master branches
 4. Update the SRC_URI to pick changes from the feature branch:
     - Example: ``` SRC_URI = "git://git.openswitch.net/openswitch/ops;protocol=https;branch=feature/versioning"```
 5. Optionally in the ops-build feature branch, change the recipe files of all the identified repositories to have the ```SRCREV=${AUTOREV}```
     - This will make sure that for your feature branch, the make will always pick the latest changes from your branch.

## Criteria For Completeness
### Criteria to Submit code to Master Branch
 To keep the quality of Master high, the code being committed must be accompanied by the appropriate tests and documentation. These commits must not introduce any regressions in existing test cases. It is not implied that all commits to master represent a fully integrated top to bottom feature.  Components that make up a feature may be delivered to Master at different times.

 Code being submitted to master must be accompanied by:

 - Design Doc - Based on the nature of code being submitted, this would be either:
     - Feature Design doc (sections relevant to the code, or full doc if this is the final drop for the feature), OR
     - Component design doc (sections relevant to the code, or full doc if this is the final drop for the component)
 - Tests - Based on the nature of code being submitted, these would be either:
     - Feature Test Plan (relevant to the code, or full plan if this is the final drop for the feature), and relevant Feature Tests in CIT capable of running in a virtual environment as well as on a target platform, OR
     - Component Test Plan (relevant to the code, or full plan if this is the final drop for the component), and relevant Component Tests in CIT
 - Functionality Guide – This document is meant for consumption by other engineers at this point, not end-users; and will be polished for that purpose at the Feature Complete checkpoint below. Functionality guide template is located [here](http://git.openswitch.net/cgit/openswitch/ops-docs/tree/user/templates/%5BFunctionality-Guide%5D.md).


### Criteria for inclusion in Release Branch (Feature Completeness)
 A feature checked into master may be considered complete if the following conditions are met, in addition to meeting the criteria for submitting to master above.  Feature owners must account for these at the time of committing the last piece of code for a feature:

- Functionality Guide – reviewed and edited for release to end users (if your corporation has a Technical Writing team, they could do this version)
- All relevant System Infra items submitted:
    - REST custom validators
    - Logging
    - Diagnostics – ASCII and/or Binary
    - Show Tech
    - RBAC
    - Audit Logs
    - Link to System Infra items: http://www.openswitch.net/develop/develophome
- All Critical and High defects resolved
- If a feature is incomplete at the time of release branch creation, the feature lead will discuss with release manager what may need to be disabled or backed out. Example: CLI commands committed but corresponding feature not ready – might need to unregister those commands in release branch.

### Templates and Examples (working on finding good examples to list here)

- Feature Design doc:  http://git.openswitch.net/cgit/openswitch/ops-docs/tree/user/templates/[Feature]_design.md
- Component Design doc: http://git.openswitch.net/cgit/openswitch/ops-docs/tree/user/templates/DESIGN.md
- Functionality Guide: http://git.openswitch.net/cgit/openswitch/ops-docs/tree/user/templates/[Functionality-Guide].md
    - Note that this is a new requirement in place of the separate User Guide and Command References
- Feature and Component Test Plan: http://git.openswitch.net/cgit/openswitch/ops-docs/tree/user/templates/[Feature-Component]_test.md

- User Guide: http://git.openswitch.net/cgit/openswitch/ops-docs/tree/user/templates/[Feature]_user_guide.md
- Command Reference: http://git.openswitch.net/cgit/openswitch/ops-docs/tree/user/templates/[Feature]_cli.md

### Checklist to enable smoothest documentation Gerrit review
There are several things that contributors can do ahead of their document submittal to help ensure the fastest successful review and posting of their documents to the OPS web site. Recommendations here can also help ensure the documentation is clear and well understood.

**Verify:**

 - The document does not contain any HTML tags, including anchor tags in the heading and ```<a href= “URL”> ```tags for linking. Grommet displays HTML tags as text in the output.
 - The document does not contain a [TOC] tag.
 - The document does not contain TBDs.
 - Headers are formatted properly.
     - In sentence case, meaning only the first word is capitalized in the header, except if the header contains a proper noun or acronym.
     - Hash tags only appear in the beginning of the header:
            ``` ### My header```

     - There is only one space between the last hash tag and the header.
 - The title is in title case.
 - Links in the TOC work properly.
     - Use StackEdit (https://stackedit.io/) to confirm the TOC links work, especially those links with special characters. Grommet removes special characters from the heading IDs, which the TOC uses for linking. Some special characters in TOC links, generated by Atom, might need to be removed.
     - Remove the TOC level with the redundant TOC headers. The TOC link generator in Atom does not create unique links for redundant headers, resulting in the users always being pointed to the first instance.
 - Articles (a, an, and the) are being used. See https://owl.english.purdue.edu/owl/resource/540/01/
for more information on when and how to use articles.
 - Run on sentences have been removed. See https://en.wikipedia.org/wiki/Run-on_sentence for more information.
 - The sentences are clear to users. Would a user not familiar with this product understand what you have written?
 - The case used for acronyms is consistent.




