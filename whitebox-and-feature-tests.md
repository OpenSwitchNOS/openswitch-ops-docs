# Whitebox and feature tests

This document explains the type of test cases that are supported by OpenSwitch test framework and enabled in OpenSwitch CIT.

## Whitebox testing
Whitebox test cases test against internal interfaces of the system. For OpenSwitch, the primary method of whitebox testing employed is component testing.

### Component testing
Component tests test the functionality of a component as presented to rest of the system. Because the functionality is presented through internal interaction interfaces, the functionality has to be verified using internal interaction interfaces. Typically this requires writing test driver to drive the input interfaces to the component and simulator to verify the corresponding reaction on output interfaces of the component. Rather than investing in writing such drivers and simulators from scratch, OpenSwitch component test cases, typically, leverage existing system components to serve as drivers and simulators. DB (through OVSDB commands) end up being most often used test driver for this reason. In general, the guidance is to drive the interfaces of the component as directly as possible, and only as an exception drive it indirectly.

OpenSwitch virtual test framework allows writing test cases using Mininet/Docker combination and test component boundaries.

### Unit testing
Certain components of OpenSwitch are not standalone applications or daemons, or do not present their interaction interfaces that can be readily driven through OVSDB commands, they need to be wrapped within an executable to test their interfaces. Such component testing typically leverages more traditional unit testing tools like GTest.

Unit testing tools can also be leveraged to supplement component test cases for a certain component where coverage for very specific situation is found lacking based on the component testing "happy path", such as:

 - complex algorithms or state machines
 - critical code sections
 - high defect density areas
 - critical error paths
 - important corner cases

## Blackbox testing
Blackbox testing tests the functionality of OpenSwitch as exposed by a certain feature to the end user.

### Feature testing
Typically, functionality of a feature is brought together by interaction between multiple OpenSwitch components. Feature tests test this functionality of a feature, that spans multiple components, and will be functionality as exposed to end user on supported end-user interfaces, such as CLI, REST or WebUI. Feature tests should always leverage one of these interfaces for driving and verification. A feature test should be something that end user of OpenSwitch can verify in their private setups using supported end-user interfaces, and hence is not white-box.

OpenSwitch virtual test framework also supports development of feature tests, especially by simulating multiple network nodes, each an instance of OpenSwitch or end hosts.

## Platform dependent testing
So far we have talked about how OpenSwitch test framework supports developing test cases that leverage virtual instances of OpenSwitch using Mininet/Docker infrastructure.

However, certain feature (or sometimes component) functionality will depend on explicit support from the hardware platform where OpenSwitch is supported. While virtual instance of OpenSwitch can be extended to simulate such hardware support, testing cannot be considered complete until test cases can also be verified against OpenSwitch running on real hardware.

When developing such feature functionality, ensure that you have access to a test lab that hosts such hardware, against which these tests can be validated.

OpenSwitch CIT infrastructure supports triggering test cases on RTLs (Remote Test Labs) where such hardware can be hosted. If you are a hardware vendor interested in supporting OpenSwitch on your platform, reach out on [infra@lists.openswitch.net](mailto:infra@lists.openswitch.net?subject=Support%20OpenSwitch%20on%20new%20hardware) mailer to learn how you can set up OpenSwitch to integrate with your RTL for validation of OpenSwitch functionality against your hardware.

## CIT Tiers
OpenSwitch CIT system has the following two tiers.

| Tier | Trigger | Runtime Target | Tests target | Test run |
|------|---------|-------------|--------------|----------|
| 1| Commit (gating) | <60 mins | Virtual switch | Upstream |
| 2 | Periodic | <6 hrs| Physical hardware switch | Vendor Remote Test Lab (RTL) |

Tier 1  CIT is done using virtual switch and is triggered on any change to any component. During this Tier1 run, all whitebox tests (unit and/or component tests) for the component that was changed will be run, along with all the blackbox (feature tests) for all features (that reside in ops repository) will be run.

Correspondingly, Tier 2 CIT focuses on test cases that require to be run on physical hardware. OpenSwitch leverages Remote Test Labs (RTLs) that reside in member labs to run these tests. When a hardware vendor joins OpenSwitch, they would contribute support for their hardware to OpenSwitch code base, and set up a RTL that integrates with OpenSwitch CIT to ensure that OpenSwitch features that are hardware dependent are tested on the hardware from that vendor. This ensures that functioning of those features on that hardware does not regress.
