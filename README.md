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

## Build & Test Results

### 1. Firmware Build Environment

| Parameter | Configuration |
|---|---|
| Microcontroller | ESP32 Dev Module |
| Platform | Espressif 32 |
| Framework | Arduino |
| Build Tool | PlatformIO |
| Compiler | Xtensa ESP32 GCC |
| Language Standard | C++17 |
| Build Command | `pio run` |

### 2. Compilation Results

**Build Status: SUCCESS**
<img width="1187" height="202" alt="image" src="https://github.com/user-attachments/assets/b4557bbf-74f3-43ce-bca7-7f7e25ff49bf" />



The firmware was successfully compiled using PlatformIO without requiring physical hardware.

| Test Parameter | Result |
|---|---:|
| Firmware compilation | PASS |
| INA219 driver compilation | PASS |
| Main firmware compilation | PASS |
| ESP32 firmware binary generation | PASS |
| Build time | 572.98 seconds |

### 3. Memory Usage

| Memory Type | Used | Available | Usage |
|---|---:|---:|---:|
| RAM | 22,024 bytes | 327,680 bytes | 6.7% |
| Flash | 303,813 bytes | 1,310,720 bytes | 23.2% |

### 4. Generated Firmware

Successful compilation generated the following firmware binary:

` .pio/build/esp32dev/firmware.bin `

### 5. Testing Status

| Feature | Testing Status |
|---|---|
| Firmware compilation | PASS |
| INA219 driver compilation | PASS |
| Memory usage analysis | PASS |
| UART command execution | Pending |
| INA219 sensor communication | Pending hardware |
| Voltage and current measurement | Pending hardware |
| Overcurrent fault detection | Pending runtime testing |
| Watchdog reset | Pending hardware testing |

### 6. Testing Notes

- Firmware compilation was completed successfully using PlatformIO.
- No physical ESP32 or INA219 sensor was connected during this test.
- Hardware-dependent functionality has not yet been verified.
- A compiler warning was reported in the ESP32 Arduino framework's UART source file; the build completed successfully despite this warning.

**Overall Result:** ESP32 firmware compilation completed successfully. Hardware validation and runtime testing are planned for the next stage.
