Installing OpenSwitch on an AS5712 (OpenSwitch supports other switches as well – please see the Hardware compatibility list and follow the more detailed installation guide here)
a.       Download the following recommended OPS image <link to a specific image that we like, not the general download page where you can easily get lost>
b.      If you have networking setup on your AS5712 and an accessible tftp server
                                                               i.      Do XXX to get the switch into ONIE Rescue OS mode
                                                             ii.      Copy the downloaded OPS image into the tftp directory with the name ops_as5712
                                                            iii.      Telnet to the switch and enter these commands
1.       tftp –g –r ops_as5712 –l ops_5712 <tftp_server>
2.       chmod …
3.       export OPS_CLEAN_INSTALL=true
4.       ./ops_5712
5.       Note: just show how to do a fool-proof clean install, provide explicit names, don’t deal with options / complications
                                                           iv.      Reboot <will it now reboot into OPS??>
c.       If you don’t have networking setup, you can use a USB-to-serial as follows
                                                               i.      Now repeat the entire description so it’s easy to follow
                                                             ii.      Help them setup mgmt. networking so they can now telnet to the switch
d.      Now that you have the switch booting to OpenSwitch:
                                                               i.      Telnet to the switch with these username/password
                                                             ii.      Type “show”
                                                            iii.      Try the following sample config for simple networking:
1.       conf t
2.       …
2.       Installing OpenSwitch virtual appliance on virtual box
a.       You can use an OpenSwitch virtual appliance to demo the control-plane features of OpenSwitch. While it is possible to get networking done through an OVA, it is beyond the scope of this manual to get the required network driver mapping to work.
b.      From here follow the same logic as above: be explicit, no choice, get a simple working VirtualBox container up and running
c.       Explain how you can now show the CLI and the WebUI
