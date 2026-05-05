## 1. Tools and Building

### 1.1. Prerequisites

- ESP-IDF v6.0.0 installed and sourced
- VS Code with the Espressif IDF Extension (recommended)
- A physical ESP32-P4 development board with 16 MB flash

### 1.2. Development Environment

| Tool | Version / Requirement |
|---|---|
| ESP-IDF Framework | v6.0.0 |
| CMake | Minimum version 3.16 |
| IDE | VS Code (recommended) |
| Language Standard | C / C++ |
| Build System | CMake via `idf.py` |

### 1.3. Steps

1. Clone the repository and open the root directory in VS Code.
2. Run `idf.py set-target esp32p4` to configure the target chip.
3. Run `idf.py menuconfig` to review or adjust any SDK settings (optional).
4. Set your COM port and flash method (UART).
5. Run `idf.py build flash monitor` to compile, flash, and open the serial monitor.

---

### 1.4. Output Project Directory Naming
The generated project directory shall have the same name as the specification directory, but with the `-specification` designation removed.

---