# Variable Map

Every value inside the device is a numbered **variable**. Settings that take an input (output control, virtual inputs, conditions, wipers, flashers, CAN outputs, etc.) point at a variable by its index.

In dingoConfig, variables are picked from a list by name, so the index only matters when [writing parameters directly](./can.md#parameters).

Variables are used to:

- Turn outputs on/off
- Build logic with virtual inputs, conditions and counters
- Control wipers, flashers and starter disable
- Set keypad button LEDs
- Send values out on CAN with CAN outputs

!!! Note
    Every variable is a number. On/off variables are `0` or `1`. 
    
    Any variable that is not `0` counts as on. For example an output can be turned on by `CAN In 1 Value` when that value is not `0`.

!!! Warning
    The variable map is different for each device and changes when new functions are added. Indexes are built in this order:

    1. System: None, Always On, Device State, Board Temperature (if the device has one), Battery Voltage (if the device has it)
    2. Digital inputs
    3. Digital outputs
    4. Analog inputs (4 each: raw, mV, rotary position, switch)
    5. CAN inputs (2 each: result, value)
    6. Virtual inputs
    7. Outputs (4 each: on, current, overcurrent, fault)
    8. Flashers
    9. Conditions
    10. Counters
    11. Wiper (6: slow, fast, park, intermittent, wash, swipe)
    12. Keypads (26 each: 20 buttons, 2 dials, 4 analog inputs)

## dingoPDM

| Index | Variable |
| :---: | -------- |
| 0 | None (always 0) |
| 1 | Always On |
| 2 | Device State |
| 3 | Board Temperature (°C) |
| 4 | Battery Voltage (V) |
| 5 | Digital In 1 |
| 6 | Digital In 2 |
| 7 | CAN In 1 |
| 8 | CAN In 1 Value |
| 9 | CAN In 2 |
| 10 | CAN In 2 Value |
| 11 | CAN In 3 |
| 12 | CAN In 3 Value |
| 13 | CAN In 4 |
| 14 | CAN In 4 Value |
| 15 | CAN In 5 |
| 16 | CAN In 5 Value |
| 17 | CAN In 6 |
| 18 | CAN In 6 Value |
| 19 | CAN In 7 |
| 20 | CAN In 7 Value |
| 21 | CAN In 8 |
| 22 | CAN In 8 Value |
| 23 | CAN In 9 |
| 24 | CAN In 9 Value |
| 25 | CAN In 10 |
| 26 | CAN In 10 Value |
| 27 | CAN In 11 |
| 28 | CAN In 11 Value |
| 29 | CAN In 12 |
| 30 | CAN In 12 Value |
| 31 | CAN In 13 |
| 32 | CAN In 13 Value |
| 33 | CAN In 14 |
| 34 | CAN In 14 Value |
| 35 | CAN In 15 |
| 36 | CAN In 15 Value |
| 37 | CAN In 16 |
| 38 | CAN In 16 Value |
| 39 | CAN In 17 |
| 40 | CAN In 17 Value |
| 41 | CAN In 18 |
| 42 | CAN In 18 Value |
| 43 | CAN In 19 |
| 44 | CAN In 19 Value |
| 45 | CAN In 20 |
| 46 | CAN In 20 Value |
| 47 | CAN In 21 |
| 48 | CAN In 21 Value |
| 49 | CAN In 22 |
| 50 | CAN In 22 Value |
| 51 | CAN In 23 |
| 52 | CAN In 23 Value |
| 53 | CAN In 24 |
| 54 | CAN In 24 Value |
| 55 | CAN In 25 |
| 56 | CAN In 25 Value |
| 57 | CAN In 26 |
| 58 | CAN In 26 Value |
| 59 | CAN In 27 |
| 60 | CAN In 27 Value |
| 61 | CAN In 28 |
| 62 | CAN In 28 Value |
| 63 | CAN In 29 |
| 64 | CAN In 29 Value |
| 65 | CAN In 30 |
| 66 | CAN In 30 Value |
| 67 | CAN In 31 |
| 68 | CAN In 31 Value |
| 69 | CAN In 32 |
| 70 | CAN In 32 Value |
| 71 | Virtual In 1 |
| 72 | Virtual In 2 |
| 73 | Virtual In 3 |
| 74 | Virtual In 4 |
| 75 | Virtual In 5 |
| 76 | Virtual In 6 |
| 77 | Virtual In 7 |
| 78 | Virtual In 8 |
| 79 | Virtual In 9 |
| 80 | Virtual In 10 |
| 81 | Virtual In 11 |
| 82 | Virtual In 12 |
| 83 | Virtual In 13 |
| 84 | Virtual In 14 |
| 85 | Virtual In 15 |
| 86 | Virtual In 16 |
| 87 | Output 1 On |
| 88 | Output 1 Current (A) |
| 89 | Output 1 Overcurrent |
| 90 | Output 1 Fault |
| 91 | Output 2 On |
| 92 | Output 2 Current (A) |
| 93 | Output 2 Overcurrent |
| 94 | Output 2 Fault |
| 95 | Output 3 On |
| 96 | Output 3 Current (A) |
| 97 | Output 3 Overcurrent |
| 98 | Output 3 Fault |
| 99 | Output 4 On |
| 100 | Output 4 Current (A) |
| 101 | Output 4 Overcurrent |
| 102 | Output 4 Fault |
| 103 | Output 5 On |
| 104 | Output 5 Current (A) |
| 105 | Output 5 Overcurrent |
| 106 | Output 5 Fault |
| 107 | Output 6 On |
| 108 | Output 6 Current (A) |
| 109 | Output 6 Overcurrent |
| 110 | Output 6 Fault |
| 111 | Output 7 On |
| 112 | Output 7 Current (A) |
| 113 | Output 7 Overcurrent |
| 114 | Output 7 Fault |
| 115 | Output 8 On |
| 116 | Output 8 Current (A) |
| 117 | Output 8 Overcurrent |
| 118 | Output 8 Fault |
| 119 | Flasher 1 |
| 120 | Flasher 2 |
| 121 | Flasher 3 |
| 122 | Flasher 4 |
| 123 | Condition 1 |
| 124 | Condition 2 |
| 125 | Condition 3 |
| 126 | Condition 4 |
| 127 | Condition 5 |
| 128 | Condition 6 |
| 129 | Condition 7 |
| 130 | Condition 8 |
| 131 | Condition 9 |
| 132 | Condition 10 |
| 133 | Condition 11 |
| 134 | Condition 12 |
| 135 | Condition 13 |
| 136 | Condition 14 |
| 137 | Condition 15 |
| 138 | Condition 16 |
| 139 | Condition 17 |
| 140 | Condition 18 |
| 141 | Condition 19 |
| 142 | Condition 20 |
| 143 | Condition 21 |
| 144 | Condition 22 |
| 145 | Condition 23 |
| 146 | Condition 24 |
| 147 | Condition 25 |
| 148 | Condition 26 |
| 149 | Condition 27 |
| 150 | Condition 28 |
| 151 | Condition 29 |
| 152 | Condition 30 |
| 153 | Condition 31 |
| 154 | Condition 32 |
| 155 | Counter 1 |
| 156 | Counter 2 |
| 157 | Counter 3 |
| 158 | Counter 4 |
| 159 | Wiper Slow |
| 160 | Wiper Fast |
| 161 | Wiper Park |
| 162 | Wiper Intermittent |
| 163 | Wiper Wash |
| 164 | Wiper Swipe |
| 165 | Keypad 1 Button 1 |
| 166 | Keypad 1 Button 2 |
| 167 | Keypad 1 Button 3 |
| 168 | Keypad 1 Button 4 |
| 169 | Keypad 1 Button 5 |
| 170 | Keypad 1 Button 6 |
| 171 | Keypad 1 Button 7 |
| 172 | Keypad 1 Button 8 |
| 173 | Keypad 1 Button 9 |
| 174 | Keypad 1 Button 10 |
| 175 | Keypad 1 Button 11 |
| 176 | Keypad 1 Button 12 |
| 177 | Keypad 1 Button 13 |
| 178 | Keypad 1 Button 14 |
| 179 | Keypad 1 Button 15 |
| 180 | Keypad 1 Button 16 |
| 181 | Keypad 1 Button 17 |
| 182 | Keypad 1 Button 18 |
| 183 | Keypad 1 Button 19 |
| 184 | Keypad 1 Button 20 |
| 185 | Keypad 1 Dial 1 |
| 186 | Keypad 1 Dial 2 |
| 187 | Keypad 1 Analog 1 |
| 188 | Keypad 1 Analog 2 |
| 189 | Keypad 1 Analog 3 |
| 190 | Keypad 1 Analog 4 |
| 191 | Keypad 2 Button 1 |
| 192 | Keypad 2 Button 2 |
| 193 | Keypad 2 Button 3 |
| 194 | Keypad 2 Button 4 |
| 195 | Keypad 2 Button 5 |
| 196 | Keypad 2 Button 6 |
| 197 | Keypad 2 Button 7 |
| 198 | Keypad 2 Button 8 |
| 199 | Keypad 2 Button 9 |
| 200 | Keypad 2 Button 10 |
| 201 | Keypad 2 Button 11 |
| 202 | Keypad 2 Button 12 |
| 203 | Keypad 2 Button 13 |
| 204 | Keypad 2 Button 14 |
| 205 | Keypad 2 Button 15 |
| 206 | Keypad 2 Button 16 |
| 207 | Keypad 2 Button 17 |
| 208 | Keypad 2 Button 18 |
| 209 | Keypad 2 Button 19 |
| 210 | Keypad 2 Button 20 |
| 211 | Keypad 2 Dial 1 |
| 212 | Keypad 2 Dial 2 |
| 213 | Keypad 2 Analog 1 |
| 214 | Keypad 2 Analog 2 |
| 215 | Keypad 2 Analog 3 |
| 216 | Keypad 2 Analog 4 |

## dingoPDM-Max

??? Note "dingoPDM-Max variable map"

    | Index | Variable |
    | :---: | -------- |
    | 0 | None (always 0) |
    | 1 | Always On |
    | 2 | Device State |
    | 3 | Board Temperature (°C) |
    | 4 | Battery Voltage (V) |
    | 5 | Digital In 1 |
    | 6 | Digital In 2 |
    | 7 | CAN In 1 |
    | 8 | CAN In 1 Value |
    | 9 | CAN In 2 |
    | 10 | CAN In 2 Value |
    | 11 | CAN In 3 |
    | 12 | CAN In 3 Value |
    | 13 | CAN In 4 |
    | 14 | CAN In 4 Value |
    | 15 | CAN In 5 |
    | 16 | CAN In 5 Value |
    | 17 | CAN In 6 |
    | 18 | CAN In 6 Value |
    | 19 | CAN In 7 |
    | 20 | CAN In 7 Value |
    | 21 | CAN In 8 |
    | 22 | CAN In 8 Value |
    | 23 | CAN In 9 |
    | 24 | CAN In 9 Value |
    | 25 | CAN In 10 |
    | 26 | CAN In 10 Value |
    | 27 | CAN In 11 |
    | 28 | CAN In 11 Value |
    | 29 | CAN In 12 |
    | 30 | CAN In 12 Value |
    | 31 | CAN In 13 |
    | 32 | CAN In 13 Value |
    | 33 | CAN In 14 |
    | 34 | CAN In 14 Value |
    | 35 | CAN In 15 |
    | 36 | CAN In 15 Value |
    | 37 | CAN In 16 |
    | 38 | CAN In 16 Value |
    | 39 | CAN In 17 |
    | 40 | CAN In 17 Value |
    | 41 | CAN In 18 |
    | 42 | CAN In 18 Value |
    | 43 | CAN In 19 |
    | 44 | CAN In 19 Value |
    | 45 | CAN In 20 |
    | 46 | CAN In 20 Value |
    | 47 | CAN In 21 |
    | 48 | CAN In 21 Value |
    | 49 | CAN In 22 |
    | 50 | CAN In 22 Value |
    | 51 | CAN In 23 |
    | 52 | CAN In 23 Value |
    | 53 | CAN In 24 |
    | 54 | CAN In 24 Value |
    | 55 | CAN In 25 |
    | 56 | CAN In 25 Value |
    | 57 | CAN In 26 |
    | 58 | CAN In 26 Value |
    | 59 | CAN In 27 |
    | 60 | CAN In 27 Value |
    | 61 | CAN In 28 |
    | 62 | CAN In 28 Value |
    | 63 | CAN In 29 |
    | 64 | CAN In 29 Value |
    | 65 | CAN In 30 |
    | 66 | CAN In 30 Value |
    | 67 | CAN In 31 |
    | 68 | CAN In 31 Value |
    | 69 | CAN In 32 |
    | 70 | CAN In 32 Value |
    | 71 | Virtual In 1 |
    | 72 | Virtual In 2 |
    | 73 | Virtual In 3 |
    | 74 | Virtual In 4 |
    | 75 | Virtual In 5 |
    | 76 | Virtual In 6 |
    | 77 | Virtual In 7 |
    | 78 | Virtual In 8 |
    | 79 | Virtual In 9 |
    | 80 | Virtual In 10 |
    | 81 | Virtual In 11 |
    | 82 | Virtual In 12 |
    | 83 | Virtual In 13 |
    | 84 | Virtual In 14 |
    | 85 | Virtual In 15 |
    | 86 | Virtual In 16 |
    | 87 | Output 1 On |
    | 88 | Output 1 Current (A) |
    | 89 | Output 1 Overcurrent |
    | 90 | Output 1 Fault |
    | 91 | Output 2 On |
    | 92 | Output 2 Current (A) |
    | 93 | Output 2 Overcurrent |
    | 94 | Output 2 Fault |
    | 95 | Output 3 On |
    | 96 | Output 3 Current (A) |
    | 97 | Output 3 Overcurrent |
    | 98 | Output 3 Fault |
    | 99 | Output 4 On |
    | 100 | Output 4 Current (A) |
    | 101 | Output 4 Overcurrent |
    | 102 | Output 4 Fault |
    | 103 | Flasher 1 |
    | 104 | Flasher 2 |
    | 105 | Flasher 3 |
    | 106 | Flasher 4 |
    | 107 | Condition 1 |
    | 108 | Condition 2 |
    | 109 | Condition 3 |
    | 110 | Condition 4 |
    | 111 | Condition 5 |
    | 112 | Condition 6 |
    | 113 | Condition 7 |
    | 114 | Condition 8 |
    | 115 | Condition 9 |
    | 116 | Condition 10 |
    | 117 | Condition 11 |
    | 118 | Condition 12 |
    | 119 | Condition 13 |
    | 120 | Condition 14 |
    | 121 | Condition 15 |
    | 122 | Condition 16 |
    | 123 | Condition 17 |
    | 124 | Condition 18 |
    | 125 | Condition 19 |
    | 126 | Condition 20 |
    | 127 | Condition 21 |
    | 128 | Condition 22 |
    | 129 | Condition 23 |
    | 130 | Condition 24 |
    | 131 | Condition 25 |
    | 132 | Condition 26 |
    | 133 | Condition 27 |
    | 134 | Condition 28 |
    | 135 | Condition 29 |
    | 136 | Condition 30 |
    | 137 | Condition 31 |
    | 138 | Condition 32 |
    | 139 | Counter 1 |
    | 140 | Counter 2 |
    | 141 | Counter 3 |
    | 142 | Counter 4 |
    | 143 | Wiper Slow |
    | 144 | Wiper Fast |
    | 145 | Wiper Park |
    | 146 | Wiper Intermittent |
    | 147 | Wiper Wash |
    | 148 | Wiper Swipe |
    | 149 | Keypad 1 Button 1 |
    | 150 | Keypad 1 Button 2 |
    | 151 | Keypad 1 Button 3 |
    | 152 | Keypad 1 Button 4 |
    | 153 | Keypad 1 Button 5 |
    | 154 | Keypad 1 Button 6 |
    | 155 | Keypad 1 Button 7 |
    | 156 | Keypad 1 Button 8 |
    | 157 | Keypad 1 Button 9 |
    | 158 | Keypad 1 Button 10 |
    | 159 | Keypad 1 Button 11 |
    | 160 | Keypad 1 Button 12 |
    | 161 | Keypad 1 Button 13 |
    | 162 | Keypad 1 Button 14 |
    | 163 | Keypad 1 Button 15 |
    | 164 | Keypad 1 Button 16 |
    | 165 | Keypad 1 Button 17 |
    | 166 | Keypad 1 Button 18 |
    | 167 | Keypad 1 Button 19 |
    | 168 | Keypad 1 Button 20 |
    | 169 | Keypad 1 Dial 1 |
    | 170 | Keypad 1 Dial 2 |
    | 171 | Keypad 1 Analog 1 |
    | 172 | Keypad 1 Analog 2 |
    | 173 | Keypad 1 Analog 3 |
    | 174 | Keypad 1 Analog 4 |
    | 175 | Keypad 2 Button 1 |
    | 176 | Keypad 2 Button 2 |
    | 177 | Keypad 2 Button 3 |
    | 178 | Keypad 2 Button 4 |
    | 179 | Keypad 2 Button 5 |
    | 180 | Keypad 2 Button 6 |
    | 181 | Keypad 2 Button 7 |
    | 182 | Keypad 2 Button 8 |
    | 183 | Keypad 2 Button 9 |
    | 184 | Keypad 2 Button 10 |
    | 185 | Keypad 2 Button 11 |
    | 186 | Keypad 2 Button 12 |
    | 187 | Keypad 2 Button 13 |
    | 188 | Keypad 2 Button 14 |
    | 189 | Keypad 2 Button 15 |
    | 190 | Keypad 2 Button 16 |
    | 191 | Keypad 2 Button 17 |
    | 192 | Keypad 2 Button 18 |
    | 193 | Keypad 2 Button 19 |
    | 194 | Keypad 2 Button 20 |
    | 195 | Keypad 2 Dial 1 |
    | 196 | Keypad 2 Dial 2 |
    | 197 | Keypad 2 Analog 1 |
    | 198 | Keypad 2 Analog 2 |
    | 199 | Keypad 2 Analog 3 |
    | 200 | Keypad 2 Analog 4 |

## CANBoard

??? Note "CANBoard variable map"

    | Index | Variable |
    | :---: | -------- |
    | 0 | None (always 0) |
    | 1 | Always On |
    | 2 | Device State |
    | 3 | Digital In 1 |
    | 4 | Digital In 2 |
    | 5 | Digital In 3 |
    | 6 | Digital In 4 |
    | 7 | Digital In 5 |
    | 8 | Digital In 6 |
    | 9 | Digital In 7 |
    | 10 | Digital In 8 |
    | 11 | Digital Out 1 |
    | 12 | Digital Out 2 |
    | 13 | Digital Out 3 |
    | 14 | Digital Out 4 |
    | 15 | Analog In 1 Raw |
    | 16 | Analog In 1 mV |
    | 17 | Analog In 1 Rotary Position |
    | 18 | Analog In 1 Switch |
    | 19 | Analog In 2 Raw |
    | 20 | Analog In 2 mV |
    | 21 | Analog In 2 Rotary Position |
    | 22 | Analog In 2 Switch |
    | 23 | Analog In 3 Raw |
    | 24 | Analog In 3 mV |
    | 25 | Analog In 3 Rotary Position |
    | 26 | Analog In 3 Switch |
    | 27 | Analog In 4 Raw |
    | 28 | Analog In 4 mV |
    | 29 | Analog In 4 Rotary Position |
    | 30 | Analog In 4 Switch |
    | 31 | Analog In 5 Raw |
    | 32 | Analog In 5 mV |
    | 33 | Analog In 5 Rotary Position |
    | 34 | Analog In 5 Switch |
    | 35 | CAN In 1 |
    | 36 | CAN In 1 Value |
    | 37 | CAN In 2 |
    | 38 | CAN In 2 Value |
    | 39 | CAN In 3 |
    | 40 | CAN In 3 Value |
    | 41 | CAN In 4 |
    | 42 | CAN In 4 Value |
    | 43 | CAN In 5 |
    | 44 | CAN In 5 Value |
    | 45 | CAN In 6 |
    | 46 | CAN In 6 Value |
    | 47 | CAN In 7 |
    | 48 | CAN In 7 Value |
    | 49 | CAN In 8 |
    | 50 | CAN In 8 Value |
    | 51 | Virtual In 1 |
    | 52 | Virtual In 2 |
    | 53 | Virtual In 3 |
    | 54 | Virtual In 4 |
    | 55 | Virtual In 5 |
    | 56 | Virtual In 6 |
    | 57 | Virtual In 7 |
    | 58 | Virtual In 8 |
    | 59 | Flasher 1 |
    | 60 | Flasher 2 |
    | 61 | Flasher 3 |
    | 62 | Flasher 4 |
    | 63 | Condition 1 |
    | 64 | Condition 2 |
    | 65 | Condition 3 |
    | 66 | Condition 4 |
    | 67 | Condition 5 |
    | 68 | Condition 6 |
    | 69 | Condition 7 |
    | 70 | Condition 8 |
    | 71 | Counter 1 |
    | 72 | Counter 2 |
    | 73 | Counter 3 |
    | 74 | Counter 4 |
