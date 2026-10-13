
# Hardware




## Current

### dingoPDM `v7`

### dingoPDM-Max `v1`

### CANBoard `v2`

### LINBoard `v1`

## Design

I've started working on a new set of hardware designs to update the existing set of designs which are getting older and used a set of constraints/decisions that were driven by the state of hobby electronics in 2020/2021. 

Some of the design constraints/decisions:
- Move away from `0805` only components to `0402` now that PCB assembly is so much cheaper and faster
  - This allows my designs to be much more compact
- Still prefer single side assembly wherever possible
- Use a single STM32 family across all devices
  - `STM32G474`
  - Different packages for different size designs
- Simplify design and manufacturing of custom PDMs or other boards
  - Move MCU, analog, USB, CAN, etc. to daughterboard
  - PDM board contains only 12V->5V and Profets
- Create a daughterboard that can be used across multiple devices
  - Simplifies ordering and inventory for me
  - Also simplifies firmware, documentation, etc.
  - Keep the connector to the mainboard on one edge to reduce mainboard size
- Still prefer low cost and easily accessible solutions where possible
  
### dingoIO `v1`

![dingoIO](./images/dingoIO.png)

- `v1.0 boards on hand`
- 60 pin header
- Size of 2x CANBoards (40mmx80mm)
- STM32G474VET3 (100 LQFP)
- 5V powered (onboard LDO -> 3.3v)
- 2x CAN
- 1x LIN
- USB-C (vertical)
- 1x I2C
- 6x digital inputs
- 4x analog inputs (scaled)
- 16x analog inputs (raw)
- 5x Neopixels, with Neopixel output
- Additional 8 pin header
- 6x GPIO pins (raw)
- 1x I2C
  
### dingoPDM `v8`

- `Design - Complete`
- `v1.0 not ordered yet`
- Uses dingoIO as a daughterboard
- 16x 13A outputs
  - 4x w/ large flybacks
- 2x DT-12
- 1x DTM-12
- M6 battery and ground lugs
- 2x CAN
- 1x LIN
- 6x digital inputs w/ ESD
- 4x analog inputs w/ ESD
- 1x FRAM/EEPROM (optional)

## Future

### CANBoard `v3` 

- `Design - active`
- dingoIO-Mini?
- Updated CANBoard
- Same 40mmx40mm size
- STM32G474CET3 (48 LQFP)
  
### dingoPDM-Mini `?`

- `Design - haven't started`
- Smaller dingoPDM, similar to v7
- Uses dingoIO as a daughterboard
- 8x 13A outputs
  - 4x w/ large flybacks
- M6 battery and ground lugs
- 1x CAN
- 1x LIN
- 2x digital inputs
- 1x FRAM/EEPROM (optional)

### dingoIO-Base `?`

- `Design - haven't started`
- Adds a connector to dingoIO
- Additional analog input scaling for dingoIO raw inputs
- Optional CAN termination resistors

# Firmware

## Current Supported Hardware

- dingoPDM `v7`
- dingoPDM-Max `v1`
- CANBoard `v2`
- LINBoard `v1`
- PT-DPDM `v1`

## New Hardware (To Add)
- dingoIO `v1`
- dingoPDM `v8`
- CANBoard `v3`

## New Features to Discuss

1. Timers (on-delay / off-delay / pulse) and condition hysteresis
   - Definitely needs to be added
   - paleppp: https://github.com/corygrant/dingoFW/pull/66 
2. Firmware update over CAN
   - OpenBLT bootloader, install once over SWD/DFU, then every update goes over CAN with no USB
   - When I looked at OpenBLT a few years ago, I remember there being some downsides that I didn't think made it worthwhile. I think they were related to debugging?
   - What is needed to implement this? 
   - How much flash is OpenBLT using on your build? 
3.  Embedded Lua 5.5 on the PDMs: read any signal, drive 32 output slots, send/receive CAN, timers, errors read back over CAN
    - Something I've wanted to add for awhile, but haven't looked into too much
    - How much flash is Lua using on your build? 
    - What is needed to implement this? 
4.  PWM input on digital inputs: duty % and frequency, glitch filter, frequency cap per board
    - What are the inputs, outputs and parameters?
5.  Analog inputs: calibrated multi-position switch (up to 10 positions) and linear sensor scaling (bar, °C…)
    - Regarding scaling, would a "Math" function be a more universal solution? Something that could muliply/divide/add/subtract?
    - The multi-position switch is already handled by Rotary Switch in the Analog Inputs, right?
6.  PWM output frequency from a signal, duty slew, PWM on CANBoard DO1–DO4
    - This may be possible and I think I had it enabled at one point, but I think I disabled this because the flash size is so small on the F303K8
7.  Warning limit and open-load detection (report only, the output keeps running)
    - The warning limit is a percentage of the limit? 
    - And open-load is also a low percentage limit?
8.  Trip log with the current waveform around each trip, read over CAN
    - How does this work? 
9.  Output bench test: force on or PWM for a limited time
    - I've avoided any sort of "force output" functionality to avoid accidentaly turning it on on by accident
    - I like the time limit idea though
10. Lookup tables, up to 8×8 with bilinear interpolation (PDMs)
    - Yes, need to add
    - PDMs only because of memory constraints? 
11. Sleep: force-sleep / mute-TX inputs, wake source per pin, outputs off on sleep
    - This should be covered by the dev build
12. CAN robustness: full RX FIFO drain, 48-frame CANBoard mailbox, back-off when frames go unacknowledged (no frame loss on a busy bus)
    - Can you explain this one some more? 
13. Refused param writes get a reply instead of silence
    - This should be fixed in the dev build
14. CANBoard built for the hardware FPU (Cortex-M4F, -Os), which frees flash; host self-test and an SWD batch flasher
    - I try to keep the builds at -O0 so it's easy to debug, but can you explain some of these more? 
15. Add LIN support (LIN outputs and LIN inputs, with LIN startup messages)
    - Have to add for dingoIO/dingoPDM v8
16. Fueltech FTCAN support
    - Awhile back I had a lot of requests for this
    - Fueltech uses a weird implementation of extended IDs
    - Not sure how popular they are in the rest of the world, but they're really popular here in the US
17. Add vehicle functions: wrapped up helper functions
    - Ignition, headlights, tailights, turn signals and hazards, interior lights, horn
18. Add Ignition function
    - https://github.com/corygrant/dingoFW/pull/67
    - https://github.com/corygrant/dingoFW/pull/69
    - https://github.com/corygrant/dingoFW/pull/70
    - https://github.com/corygrant/dingoFW/pull/71
    - https://github.com/corygrant/dingoFW/pull/72
    - https://github.com/corygrant/dingoFW/pull/73
    - https://github.com/corygrant/dingoFW/pull/74
19. Add CAN message block
    - https://github.com/corygrant/dingoFW/pull/68

# Software

## dingoConfig

### New Features

1. Firmware flashing in the app: USB DFU, blank boards, and over CAN
   - This would use web-dfu? 
2. Kvaser adapter
   - Makes sense to add
3. SLCAN over TCP
   - Sounds like a cool feature, what sort of things does this enable? 
4. Works on a flooded bus
   - Is this referring to config read/write?
   - I think I made some progress on this in the dev build
5. Discovery with Add from CAN
   - This is to detect devices based on the messages they're sending?
6. System view: every module, car-layout map
    - This is a way to assign the device cards to a generic car image? 
7. Vehicle worst-case load
    - This would sum the total max of every output? 
8. CAN-ID conflict guard with Suggest free base ID
    - How would the conflict guard work? Prevent the user from entering a conflicting range? 
9.  Wiring node graph with Circuit Builder wizards and a guided rule builder
    - Dev build has React Flow configuration 
10. Signals view with a live 30 s chart per row, plus a global plot
    - Dev build has sparklines and a global plot
11. Lua editor with upload verified by read-back and slot lint
    - Have you found any good Lua editors? 
12. Cross-module functions, and CAN inputs that take another module's signal by name
    - Isn't this something that is aleady working? 
13. Editors for PWM input, hysteresis, timers, lookup tables, calibrated rotary and sensor scaling
    - Yes for all added functions
14. Shutdown & sleep sequence designer
    - What does this mean? 
15. Trip log viewer, CAN log recording, bench test, wire gauge / voltage-drop hints
    - You can record a CAN log now
    - What does the trip log viewer do? 
    - Bench test, maybe with a time limit
    - What is the wire gauge/voltage drop hints? 
16. Confirmed writes: a refused value is reported, and Write All falls back to acknowledged single writes
    - I think this is handled in dev build
17. REST API and an MCP server, so an AI agent can configure everything
    - Andrew Beck's approach: https://github.com/esensible/dingo-cli
    - I think the REST API approach may be the better way to go
18. Single-file exe with the UI built in, unit tests and CI
    - What advantages would this have?
    - The current structure is a backend and a web front end, which enables different use cases
20. Unit tests + CI
    - Definitely need to work on adding unit tests
21. Recently opened projects on the home page
    - Seems like a nice feature to add
22. Vehicle functions: wrapped up helper functions
    - Ignition, headlights, tailights, turn signals and hazards, interior lights, horn
    - https://github.com/corygrant/dingoConfig/pull/71
    - I like the idea, but I'm concerned that its easy to configure these vehicle functions to be specific to our use cases. 
    - How can we make these more generic or universal? 
    - Maybe Lua? 
23. Learn a switch from the bus
    - https://github.com/corygrant/dingoConfig/pull/74
24. Release the serial port when an SLCAN adapter is replaced
    - This seems like a bug, should definitely fix

## Simulator

[Coffee's simulator](https://github.com/Coffee0297/CoffeeDingoSim) looks really mature, there's no reason to build anything else. 

I need to spend some more time playing with it to offer any ideas, but it's awesome that it uses Renode. 

# Documentation

- This is becoming a huge open issue as more and more things get added. 