Writing Automated Feature Test Cases
=================

- [Introduction](#introduction)
- [Test Case Sections](#test-case-sections)
	- [Module Import Section](#module-import-section)
	- [Topology Definition](#topology-definition)
	- [Test Procedures](#test-procedures)
	- [Pytest Test Class Definition](#pytest-test-class-definition)
- [Framework Objects](#framework-objects)
	- [The testEnviron Object](#the-testenviron-object)
	- [The Topology Object](#the-topology-object)
	- [Device Objects](#device-objects)
	- [The returnStruct Object](#the-returnstruct-object)
- [Helper / Utiilty Libraries](#helper-utiilty-libraries)

## Introduction
This document will discuss how to write a feature test case for the OpenSwitch project.  Sections of the test case will be discussed initially.  Next we will discuss the opstestfw objects.  Finally we will list out available modules that area available fro all developers.

The opstestfw framework object oriented Python framework  The OpenSwitch project has standardized using the pytest module wen creating test cases.

## Test Case Sections
###  Module Import Section
The first section of any test case will start with the library import section.  Below is an example of a basic test case that...
```
import pytest
from opstestfw import *
from opstestfw.switch.CLI import *

```
The first module that should be imported is pytest.  Next  we need to bring in the opstestfw modules.  Here is where the framework objects are brought in from.  The third line is bringing in all the switch CLI  helper library routines.

### Topology Definition
Each test case must have a "topoDict" dictionary defined.  This dictionary describes the way the topology is configured.  Below is an example topology.
```
topoDict = {"topoTarget": "dut01",
			"topoDevices": "dut01 wrkston01 wrkston02",
            "topoLinks": "lnk01:dut01:wrkston01,lnk02:dut01:wrkston02",
            "topoFilters": "dut01:system-category:switch,dut02:system-category:switch"}

```
The names used in the dictionary values should follow this notation..

 - **Switches**:  These should be named "dut0x",  If I have a two switch topology I would call these switches logically dut01 and dut02.
 - **Workstations / Hosts**:  These should be named "wrkston0x".  If I have three workstations in the topology, these should be referred to as wrkston01, wrkston02, and wrkston03.
 - **Links**:  Links aer connections between two entities and should be named in the following way, lnk0x.  If I have three links in my topology they should be named lnk01, lnk02, and lnk03 respectively.

Below is a description of the topoDict key / value pairs.

 - **topoTarget**:  This is identifying in the topology (switches) that will install the build under test.  If I have three switches in the topology, the value would look like "dut01 dut02 dut03".
 - **topoDevices**:  This identifies all the logical devices in the topology.  If I have two switches and three workstations in my topology the value of this would look like "dut01 dut02 wrkston01 wrkston02 wrkston03".
 - **topoLinks**:  This defines the links for the topology and specifies the entities that the link interconnects.  The link definition is delimited by the ":" character.  The notation of a link definition statement is linkname:device1:device2.  Multiple links are delimited by the "," character.  If I had 3 links defined the statement might look like "lnk01:dut01:wrkston01,lnk02:dut01:wrkston02,lnk03:dut01:dut02".  This example states that "lnk01" is a connection between dut01 and wrkston01, "lnk02" is a connection between dut01 and wrkston02, and "lnk03" is a connection between dut01 and dut02.
 - **topoFilters**:  This gives base definition of what each device is.  This is in the form of  "device":"attribute":"value".  The one attribute that is currently accepted today is "system-category" and the value of this is either switch or workstation.
 - **topoLinkFilter**:  This is a filter on the link that can add requirements to a specific link.  For example, if we want to make sure a port is connected to a specific port on the switch you would add it in the following notation <lnkName>:<device>:interface:<interfaceName>
"topoLinkFilter": "lnk01:dut01:interface:eth0" - this makes sure that lnk01 port on dut01 is over eth0.  This topology key is optional.

### Test Procedures
Next in the test suite we would have procedures that define test cases.  Below is an example of this test case hat configures a switch interface.
```
def config_switch_interface(dut01):
    # Switch configuration
    retCode = 0
    LogOutput('info', "Configuring Switch to be an IPv4 router")
    retStruct = InterfaceEnable(deviceObj=dut01,
                                enable=True,
                                interface=dut01.linkPortMapping['lnk01'])
    if retStruct.returnCode() != 0:
        LogOutput('error', "Failed to enable port on switch")
        retCode = 1
    else:
        LogOutput('info', "Successfully enabled port on switch")

    retStruct = InterfaceIpConfig(deviceObj=dut01,
                                  interface=dut01.linkPortMapping['lnk01'],
                                  addr="140.1.1.1",
                                  mask=24,
                                  config=True)

    ptosRetStruct = returnStruct(returnCode=retCode)
    return ptosRetStruct

```
The suite file should have multiple test case procedures defined.  There are two strategies to writing these procedures.  The first is to write the test suite where each test case (procedure) builds upon the next.  The second strategy is to have each of these procedures be 100% independent of each other.  If this strategy is taken, keep in mind there may be some need for some cleanup configuration in each procedure.  This way tests can be skipped and not affect other tests.

### Pytest Test Class Definition
Finally, each test suite would have a pytest class definition. Below is an example of a Pytest Class.
```
class Test_ft_framework_basics:
    def setup_class(cls):
        # Create Topology object and connect to devices
        Test_ft_framework_basics.testObj = testEnviron(topoDict=topoDict)
        Test_ft_framework_basics.topoObj = Test_ft_framework_basics.testObj.topoObjGet()

    def teardown_class(cls):
        # Terminate all nodes
        Test_ft_framework_basics.topoObj.terminate_nodes()

    def test_reboot_switch(self):
	    LogOutput('info', "Reboot the switch")
        dut01Obj = self.topoObj.deviceObjGet(device="dut01")
        devRebootRetStruct = switch_reboot(dut01Obj)
        if devRebootRetStruct.returnCode() != 0:
            assert 1 == 0, "Failed to reboot Switch"
        else:
            LogOutput('info', "Passed Switch Reboot piece")

    def test_ping_to_switch(self):
        LogOutput('info', "Configure and ping to switch")
        dut01Obj = self.topoObj.deviceObjGet(device="dut01")
        wrkston01Obj = self.topoObj.deviceObjGet(device="wrkston01")
        pingSwitchRetStruct = ping_to_switch(dut01Obj, wrkston01Obj)
        if pingSwitchRetStruct.returnCode() != 0:
            assert 1 == 0, "Failed to ping to the switch")\
        else:
            LogOutput('info', "Passed ping to switch test")

    def test_ping_through_switch(self):
        LogOutput('info', "Additional configuration and ping through switch")
        dut01Obj = self.topoObj.deviceObjGet(device="dut01")
        wrkston01Obj = self.topoObj.deviceObjGet(device="wrkston01")
        wrkston02Obj = self.topoObj.deviceObjGet(device="wrkston02")
        pingSwitchRetStruct = ping_through_switch(dut01Obj, wrkston01Obj, wrkston02Obj)
        if pingSwitchRetStruct.returnCode() != 0:
            assert 1 == 0, "Failed to ping through the switch"
        else:
            LogOutput('info', "Passed ping to switch test")

    def test_clean_up_devices(self):
        LogOutput('info', "Device Cleanup - rolling back config")
        dut01Obj = self.topoObj.deviceObjGet(device="dut01")
        wrkston01Obj = self.topoObj.deviceObjGet(device="wrkston01")
        wrkston02Obj = self.topoObj.deviceObjGet(device="wrkston02")
        cleanupRetStruct = deviceCleanup(dut01Obj, wrkston01Obj, wrkston02Obj)
        if cleanupRetStruct.returnCode() != 0:
            assert 1 == 0 , "Failed to cleanup device"
        else:
            LogOutput('info', "Cleaned up devices")



```
There are key methods for the class are explained below...

 - **setup_class**:  In this class we must define the testEnviron object.  This object will take the topoDict and operate no that topology definition.  It will build an start your topology along with establishing interactive connections (terminal) with each device defined.  Next we must obtain the topology object from the testEnviron object.
 ```
        Test_ft_framework_basics.testObj = testEnviron(topoDict=topoDict)
        Test_ft_framework_basics.topoObj = Test_ft_framework_basics.testObj.topoObjGet()

 ```

 - **teardown_class**:  This class method will be run post test case.  In this method we must call the topology teardown method as shown below.  If you have other things to clean up in the  suite, this would be the place to do it.
```
		# Terminate all nodes
	    Test_ft_framework_basics.topoObj.terminate_nodes()
```

- **test_ methods**:   Pytest tests are defined as methods in the class.  Each test needs to be named "test_"  In the example above   the first test is called "test_reboot_switch".   One think that will occur in the test procedure is the device objects will be local to the test procedure and should be obtained in the procedure.  Below is an example of how we get the device objects from the topology object.
```
	   dut01Obj = self.topoObj.deviceObjGet(device="dut01")
       wrkston01Obj = self.topoObj.deviceObjGet(device="wrkston01")
       wrkston02Obj = self.topoObj.deviceObjGet(device="wrkston02")
```

## Framework Objects
The framework has some core objec.  This section will describe their key function and describe how to use them.  It is important to know the hierarchy of the objects to be successful with working with them.

### The testEnviron Object
This object is the first object created in the test suite.  This object will do the following...

 - Create the logging structure for the suite. By default, this will create a log structure in /tmp/opsTest-results.  Each test run will have an individual date stamp directory under the main results directory.
 - Figure out topology specifications and instantiate the topology object.
 - The topology object is a member of this class.

 As mentioned in the Pytest setup_class section, the testEnviron object needs to be defined in the setup_class method.

### The Topology Object
The topology object is instantiated and is stored within the testEnviron object.  This is actually created in setup_class.

The topology code will build and map the topology.  This will also create all device objects and enable those objects to create interactive connects wit the devices.  One of the most important tasks the Topology object performs is the create and populate the linkPortMapping dictionary for each device.

### Device Objects
The device objects are stored within the topology objects.  The device object contains connection information and objects for the device.  This objects has methods to interact with the device and error check the transaction with the device.  The device object also contains the linkPortMapping dictionary that maps a physical port to a logical link for the device.  If you wanted to get the port of a switch that is connected over lnk01, you would reference it as dut01Obj.ilnkPortMapping['lnk01'].

The device object is the object that will be passed into the helper library functions.  The device object enables the helper libraries to interact with the device.

### The returnStruct Object
The helper libraries and many object methods will return the returnStruct object to the calling process.  Helper libraries or class methods will create this object and return it to the calling routine.
The returnStruct object provides consistency in library returns and gives  standard methods for pulling values out of the return structure.  
Arguments to this class are: returnCode, buffer, and data (data can be either a value or a dictionary).  Methods to retrieve data…

 - returnCode()  gets he returnCode for the library called.
 - buffer() returns the raw buffer if one is given to the object on creation.
 - dataKeys() gives top level dictionary keys of the data stored
 - valueGet(key=)  If no key is given, just returns data.  If a key is
   given, will return the value for the key in the dictionary
   retValueString()  method to return a JSON string of the returnCode,
   buffer and data
   printValueString()  same as retValueString, but prints the JSON string

## Helper / Utiilty Libraries
Helper and utility libraries are used for performing certain functions like enabling an interface, configure or unconfigure a feature.  Currently the framework packages up these libraries in the opstestfw.host package for any host specific libraies and the opstestfw.switch.CLI package for switch CLI libraries.
