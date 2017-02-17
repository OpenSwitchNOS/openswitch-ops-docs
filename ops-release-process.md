# OPS Release Process
- [Overview](#overview)
- [Release Management](#release-management)
    - [Versioning Strategy](#versioning-strategy)
    - [Module Maintainer Responsibilities](#module-maintainer-responsibilities)

## Overview
OpenSwitch (OPS) uses a time-based release model, operating on ~ a 13 week cycle target. The dates for the milestones and final release in a given development cycle are defined by the Release Management team, and communicated before the new development cycle starts. At any gven time a single release branch is supported by the community until the next release is triggered. Only Critical and High severity defects are permitted to be fixed in the release branch to minimize rate of change during the release. Medium and low severity defect fixes are to be fixed on the master branch for inclusion in the next release.

### OPS Release Cycle Timeline Targets
#### Release cycle start
ops-build repository master branch is open for commits and defect fixes and the master branch version is incremented.

## Release Management
### Versioning Strategy
Given a version number MAJOR.MINOR.PATCH, increment the:
 1. MAJOR version for major feature completion milestones or break backwards compatibility
 2. MINOR version when introduce a new feature set
 3. PATCH version bug fixes.

### Module Maintainer Responsibilities
 - Overall quality/usability/condition and roadmap of the repositories
 - Versioning of individual repositories (based on semantic versioning semver.org) (Training/guidelines will be provided)
 - Decisions on when to sync release->master to absorb C/H defects into master
 - What SHA is included in ops-build for the repo during Feature Freeze (FF) for release branch pull
     - This will include making sure that release branch does not get any partial features in the release branch
 - Ensure maximum test coverage for new code in the repository (may involve working with Feature Leads)





