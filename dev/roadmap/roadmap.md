
# Hardware




## Current

### dingoPDM `v7`

### dingoPDM-Max `v1`

### CANBoard `v2`

### LINBoard `v1`

## Active Development

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

- `v1.0 boards arriving October 13th`
- 60 pin header
- Size of 2x CANBoards (40mmx80mm)
- STM32G474VET3 (100 LQFP)
- 5V powered (onboard LDO -> 3.3v)
- 2x CAN
- 1x LIN
- USB-C
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

## Supported Hardware

- dingoPDM v7
- dingoPDM-Max
- CANBoard
- LINBoard
- PT-DPDM

## New Hardware
- dingoIO v1
- dingoPDM v8


# Software
  - dingoConfig
  - MCP
  - Simulator
- Documentation