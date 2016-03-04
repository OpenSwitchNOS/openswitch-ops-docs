Writing Automated Feature Test Cases
=================

Writing test cases
==================

A *test suite* is a Python file whose name begins with test, like:
`test_name_of_the_test_suite.py`

Topology definition
-------------------

Suites must have only one *topology definition*, a string that defines a
test topology. The following is an example:

    TOPOLOGY = """ 
    +-------+                                       +-------+ 
    |       |       +-------+       +-------+       |       | 
    | hs1   <------->  sw1  <------->  sw2  <------->  hs2  | 
    |       |       +-------+       +-------+       |       |
    +-------+                                       +-------+
    
    # Nodes 
    [type=openswitch name=“Switch 1”] sw1 
    [type=openswitch name=“Switch 2”] sw2 
    [type=host name=“Host 1”] hs1 hs2
    
    # Ports 
    [up=True] hs1:1
    
    # Links 
    [category=5e] hs1:1 – sw1:3 
    sw1:4 – sw2:3 sw2:4 – hs2:1 
    """

The topology definition must be assigned to a variable named TOPOLOGY.

This syntax supports comments, any line that begins with `#` will be
ignored by the parser.

There are three elements in a topology definition:

1.  **Nodes** represented by a simple string: `sw1`
2.  **Ports** represented by a colon and the name of the port: `hs1:1`
3.  **Links** represented by a double dash between 2 ports:
    `hs1: -- sw1:3`

Each element in a topology definition can have *attributes*, a list of
`key=value` pairs that define their properties. These attributes are
passed to the specific topology platform plugin that handles them
adequately or ignores them.

Test case functions
-------------------

A suite may have several test case functions. These functions must begin
with `test` and may receive [fixtures]. This framework provides the
`topology` fixture that handles topology setup and teardown
automatically. To use it, define a test case like this:

    def test_name_of_the_test_case(topology):
         """
         Description of the test case.
         """

         hs1 = topology.get('hs1')

         ...

As you can see from the example above, `topology` can be used to access
the nodes in the topology.

You can add input in the test logs by calling the `step` function in
your test case like this:

    def test_name_of_the_test_case(topology, step):
         """
         Description of the test case.
         """
         step('Getting the devices')
         hs1 = topology.get('hs1')

         ...

The arguments for the `step` function will show up inside the
`.tox/py34/tmp/tests.xml` file.

  [fixtures]: https://pytest.org/latest/fixture.html?highlight=fixtures