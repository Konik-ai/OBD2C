Designed using Kicad 8

Unlike harness box, OBD2-C doesn't use relay. 

The purpose of it is to sniff CAN buses that are already present in OBD-2 port, similar to what white panda was doing in the past, but compatible with [OBD-C](https://github.com/commaai/neo/blob/master/car_harness/OBD-C.sch.pdf), though often only diagnostic CAN is populated on OBD-2.

The device exposes three CAN buses out of OBD-C:
- CAN0 (3, 11)
- CAN1 is routed to the JST (pin 2,3) connector (can be connected .e.g to [StepperServoCAN](https://github.com/dzid26/StepperServo-hardware)) and has optional 120ohm termination resistor (see jumpers).
Alternatively, bus 1 can be rerouted using jumpers as a GMLAN (obd pin 1) as in [white panda](https://comma-ai.medium.com/a-panda-and-a-cabana-how-to-get-started-car-hacking-with-comma-ai-b5e46fae8646) - but unlike [white panda](https://github.com/martiinezz/panda/blob/master/white-hw/README.md) it uses single wire [hack](https://canhacker.com/examples/gm-lan-single-wire-can-examples/) and is not tested
- CAN2 (pins 12, 13)
- CAN3 (pins 6, 14) - this one can be used e.g. as "[ELM327](https://github.com/commaai/panda/blob/master/examples/query_vin_and_stats.py)" or [vin](https://github.com/commaai/openpilot/blob/master/selfdrive/debug/car/vin.py) reading in openpilot 

![OBD-2 connector pinout](https://www.csselectronics.com/cdn/shop/files/obd2-connector-pinout-socket.svg)
https://www.csselectronics.com/cdn/shop/files/obd2-connector-pinout-socket.svg

Some notes regarding openpilot bus names mapping to STM32 peripherals and [harness](https://github.com/commaai/neo/blob/master/car_harness/v1/open_pinout.sch.pdf) - (the indexes are not always consistent):
`HARNESS_STATUS_NORMAL`
| openpilot | STM32F4 | Harness |  WhitePanda 
| ---|---|---|---|
|sbu1|   | sbu2|  |
|sbu2|   | sbu1|  |
| bus 0  | CAN1 |  CAN0  | CAN1|
| bus 1  | CAN2 |  CAN1  | CAN2 |
|bus 1(obd)| CAN2 alt| CAN3| GMLAN|
| bus 2  | CAN3 | CAN2 | CAN3|


`HARNESS_STATUS_FLIPPED`
| openpilot | STM32F4 | Harness |  WhitePanda 
| ---|---|---|---|
|sbu1|   | sbu1|  |
|sbu2|   | sbu2|  |
| bus 0  | CAN3     |  CAN0  | CAN1|
| bus 1  | CAN2 alt|  CAN1  | CAN2 |
|bus 1(obd)| CAN2      | CAN3   | GMLAN|
| bus 2  | CAN1     | CAN2   | CAN3|

`+obd` means `set_obd_multiplexing(True)` in openpilot aka `panda.set_obd(True)` in panda lib aka `set_can_mode(CAN_MODE_OBD_CAN2)` in panda firmware.

Confusing? Yes. 
Especially inside panda [firmware](https://github.com/commaai/panda/pull/1860).