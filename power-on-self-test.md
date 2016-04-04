# AS5712 Power On Self Test Requirements

## Contents
- [Description](#description)
- [POST Requirements](#post-requirements)

## Description
A power-on self test (POST) is run automatically on various components at power-on.

## POST Requirements
Below are the tests that should be done at a high level.  The tests are run from ROM during boot-up.
- Check if different interfaces are present
- on board hardware components test
- CPU test
- traffic (traffic from CPU to switch in a loop)
- packet/system memory
- card 
- fan
- power supply

There should be an option to run the test from cli as well:

OPS# config
OPS(config)# diag fan <fan number>
OPS(config-diag-fan-1)# test self-test
OPS(config-diag-fan-1)# start
OPS(config-diag-fan-1)# exit
OPS(config)# exit

OPS# show diagnostic fan

OPS> enable
OPS# config
OPS(config)# diag card <card number> 
OPS(config-diag-card-11)# test self-test
OPS(config-diag-card-11)# start
OPS(config-diag-card-11)# exit
OPS(config)# exit

OPS# show diagnostic card

OPS> enable
OPS# config 
OPS(config)# diag power-supply <number>
OPS(config-diag-power-supply-1)# test led
OPS(config-diag-power-supply-1)# start
OPS(config-diag-power-supply-1)# stop
OPS(config-diag-power-supply-1)#

OPS# show diagnostic power-supply
