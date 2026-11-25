# Smart Energy Monitor Firmware (ESP32)

Firmware that measures bus voltage and current with an INA219 over I2C, exposes a UART command line,
detects over-current faults with a state machine, and logs the last fault to flash. A Python tool
(`tools/diagnose.py`) talks to the board for live readings and CSV logging.

## Features
- INA219 driver written directly against the register map (no vendor library)
- UART receive callback feeding a lock-free single-producer/single-consumer ring buffer
- State machine: IDLE -> SAMPLING -> REPORTING -> FAULT
- Task watchdog; last fault stored in NVS flash
- Python diagnostic tool (pyserial) with CSV logging

## Hardware
| Part | Connection |
|---|---|
| ESP32 dev board | USB to laptop |
| INA219 module | VCC->3.3V, GND->GND, SDA->GPIO21, SCL->GPIO22 |
| Load (motor / resistor, 5-12 V DC) | in series through INA219 VIN+ / VIN- |

Low-voltage DC only. Never connect mains.

## Build and flash (PlatformIO)
```
pio run -t upload
pio device monitor
```

## Serial commands
`status`, `read`, `start`, `stop`, `set_interval <ms>`, `set_limit <mA>`, `reset`, `faultlog`, `stats`, `crash` (watchdog test)

## Python tool
```
pip install -r requirements.txt
python tools/diagnose.py --port COM5 status
python tools/diagnose.py --port COM5 set-limit 800
python tools/diagnose.py --port COM5 monitor --duration 3600 --csv log.csv
```

## Test results
Fill in from your own runs.

| Test | Result |
|---|---|
| Soak test duration | __ h |
| Samples per second | __ |
| I2C errors | __ |
| Reading vs multimeter | __ mA difference |
| Over-current fault triggers FAULT state | yes / no |
| Watchdog resets board after `crash` | yes / no |

## Photos / demo
Add a wiring photo, serial screenshot and demo video link in `docs/`.
