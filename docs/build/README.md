## Tools and Building

### Prerequisites

- ESP-IDF v6.0.0 installed and sourced
- VS Code with the Espressif IDF Extension (recommended)
- A physical ESP32-P4 development board with 16 MB flash

### Development Environment

| Tool | Version / Requirement |
|---|---|
| ESP-IDF Framework | v6.0.0 |
| CMake | Minimum version 3.16 |
| IDE | VS Code (recommended) |
| Language Standard | C / C++ |
| Build System | CMake via `idf.py` |

### Steps

1. Clone the repository and open the root directory in VS Code.
2. Run `idf.py set-target esp32p4` to configure the target chip.
3. Run `idf.py menuconfig` to review or adjust any SDK settings (optional).
4. Set your COM port and flash method (UART).
5. Run `idf.py build flash monitor` to compile, flash, and open the serial monitor.

---

### Build Configuration

The project uses a **minimal build** configuration (`MINIMAL_BUILD ON`), meaning CMake only compiles the `main` component and its direct dependencies. This keeps build times fast and binary size small.

The project name as registered with the CMake build system is: `p4-base-project-16m`

---