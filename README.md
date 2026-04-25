# Software Project Specification

## Project: esp-p4-base-project-16m-specification

**Author / Organization:** SolidStateLEDLighting  
**Document Version:** 1.0  
**Date:** April 24, 2026  

---

## 1. Overview

### 1.1 Purpose

This project is a minimal base (boilerplate) application for the Espressif **ESP32-P4** microcontroller compiled with **ESP-IDF v6.0.0**. Its purpose is to serve as a clean, pre-configured starting point for new firmware projects targeting the ESP32-P4 with 16 MB of external SPI flash memory.

### 1.2 Scope

The project provides:

- A compilable, minimal ESP-IDF application skeleton
- A pre-defined flash partition table sized and organized for a 16 MB flash device
- Default SDK configuration targeting the ESP32-P4
- IDE tooling support for VS Code via a Clangd configuration file

This project does **not** include application-level logic, peripheral drivers, Wi-Fi configuration, or user interface components. Those are expected to be added by the developer using this as a foundation.

---

## 2. Target Hardware

| Parameter | Value |
|---|---|
| Microcontroller | Espressif ESP32-P4 |
| Flash Size | 16 MB (16,384,000 bytes) |
| Minimum Chip Revision | v0.0 (REV_MIN_FULL = 0) |
| Flash Mode | DIO |
| Flash Speed | 80 MHz |

---

## 3. Development Environment

| Tool | Version / Requirement |
|---|---|
| ESP-IDF Framework | v6.0.0 |
| CMake | Minimum version 3.16 |
| IDE | VS Code (recommended) |
| Language Standard | C / C++ |
| Build System | CMake via `idf.py` |

### 3.1 Build Configuration

The project uses a **minimal build** configuration (`MINIMAL_BUILD ON`), meaning CMake only compiles the `main` component and its direct dependencies. This keeps build times fast and binary size small.

The project name as registered with the CMake build system is: `p4-base-project-16m`

### 3.2 IDE Support

A `.clangd` file is included at the project root to support the Clangd language server in VS Code. The configuration removes compiler flags prefixed with `-f*` and `-m*`, which are specific to the Xtensa/RISC-V GCC toolchain and would otherwise cause Clangd errors when parsing source files.

---

## 4. Flash Partition Table

The project uses a **custom partition table** defined in `partitions.csv`. The partition table offset is set to `0xc000` (required when flash encryption is enabled). All memory offsets are calculated automatically by the build system.

### 4.1 Partition Layout

| Name | Type | Subtype | Size | Notes |
|---|---|---|---|---|
| `nvs` | data | nvs | 24 KB (0x6000) | Non-volatile storage; cannot be encrypted directly |
| `nvs_key` | data | nvs_keys | 4 KB (0x1000) | NVS encryption keys; marked encrypted |
| `phy_init` | data | phy | 4 KB (0x1000) | PHY calibration data; marked encrypted |
| `otadata` | data | ota | 8 KB (0x2000) | OTA boot selection metadata |
| `ota_0` | app | ota_0 | 5.0 MB (0x4E2000) | Primary OTA application slot |
| `ota_1` | app | ota_1 | 5.0 MB (0x4E2000) | Secondary OTA application slot |
| `littlefs` | data | littlefs | 4 MB (0x3E8000) | LittleFS file system partition |

### 4.2 Memory Summary

| Metric | Size |
|---|---|
| Total flash capacity | 16,384,000 bytes (0xFA0000) |
| Total space allocated | 14,376,960 bytes (0xDB6000) |
| Unused / reserved space | 2,007,040 bytes (0x1EA000) |

### 4.3 OTA (Over-the-Air Update) Support

The partition layout explicitly supports dual-bank OTA updates. The `otadata` partition controls which application slot (`ota_0` or `ota_1`) is booted. Each OTA application partition is 5.0 MB, providing ample space for commercial-grade firmware images.

### 4.4 File System

A `littlefs` partition of approximately 4 MB is included for persistent file storage. This is suitable for storing configuration files, certificates, logs, or other user data.

### 4.5 Flash Encryption Notes

The partition table is prepared for optional flash encryption. The following rules apply when encryption is activated:

- The `nvs` partition cannot be encrypted at the partition level, but its data is stored in an encrypted form when flash encryption is enabled system-wide.
- Application (`app`) partitions are always treated as encrypted when system-level encryption is active; no explicit `encrypted` flag is needed.
- The `nvs_key` and `phy_init` partitions are flagged `encrypted` and require the boot partition table offset to be set to `0xc000` (already configured in this project).

---

## 5. SDK Default Configuration (sdkconfig.defaults)

The `sdkconfig.defaults` file provides the minimum required configuration overrides for this project. These values are automatically applied when the project is first configured or when `idf.py set-target esp32p4` is run.

| Configuration Key | Value | Description |
|---|---|---|
| `CONFIG_IDF_TARGET` | `esp32p4` | Target chip selection |
| `CONFIG_IDF_TARGET_ESP32P4` | `y` | Enables ESP32-P4-specific build paths |
| `CONFIG_ESPTOOLPY_FLASHSIZE_16MB` | `y` | Declares 16 MB flash to esptool |
| `CONFIG_ESPTOOLPY_HEADER_FLASHSIZE_UPDATE` | `y` | Updates the binary header with flash size automatically |
| `CONFIG_ESP32P4_SELECTS_REV_LESS_V3` | `y` | Enables support for pre-v3 chip revisions |
| `CONFIG_ESP32P4_REV_MIN_0` | `y` | Sets minimum chip revision to 0 |
| `CONFIG_ESP32P4_REV_MIN_FULL` | `0` | Full minimum revision value = 0 |
| `CONFIG_ESP_REV_MIN_FULL` | `0` | System-level minimum revision = 0 |
| `CONFIG_PARTITION_TABLE_CUSTOM` | `y` | Enables custom partition table |
| `CONFIG_PARTITION_TABLE_CUSTOM_FILENAME` | `partitions.csv` | Points to the custom partition file |
| `CONFIG_PARTITION_TABLE_OFFSET` | `0xc000` | Required offset for encryption support |

---

## 6. Project File Structure

```
esp-p4-base-project-16m/
├── main/                        # Main application component (C/C++ source files)
├── .clangd                      # Clangd language server configuration for VS Code
├── .gitignore                   # Git ignore rules
├── CMakeLists.txt               # Top-level CMake build file
├── README.md                    # Repository description
├── partitions.csv               # Custom flash partition table
└── sdkconfig.defaults           # Default SDK configuration overrides
```

The `main/` subdirectory contains the application entry point and any component-level `CMakeLists.txt`. The primary language of the application code is **C++** (approximately 15.5% of the codebase), with the build system written in **CMake** (approximately 84.5%).

---

## 7. Build and Flash Instructions

### 7.1 Prerequisites

- ESP-IDF v6.0.0 installed and sourced
- VS Code with the Espressif IDF Extension (recommended)
- A physical ESP32-P4 development board with 16 MB flash

### 7.2 Steps

1. Clone the repository and open the root directory in VS Code.
2. Run `idf.py set-target esp32p4` to configure the target chip.
3. Run `idf.py menuconfig` to review or adjust any SDK settings (optional).
4. Set your COM port and flash method (UART).
5. Run `idf.py build flash monitor` to compile, flash, and open the serial monitor.

---

## 8. Design Constraints and Assumptions

- This project targets the ESP32-P4 exclusively. It is not directly portable to other ESP32 variants without partition table and `sdkconfig.defaults` adjustments.
- The minimal build flag (`MINIMAL_BUILD ON`) is intentional. Developers adding new features should ensure any required IDF components are explicitly declared in the component's `CMakeLists.txt`.
- The LittleFS partition is defined but not initialized in this base project. Application code must mount it explicitly.
- Flash encryption is supported by the partition layout but is **not enabled by default**. Enabling it requires additional steps in `menuconfig` and physical device provisioning.
- OTA update logic is not included in this base project. The partition table is prepared for OTA; the application developer must implement the OTA workflow.

---

## 9. Glossary

| Term | Definition |
|---|---|
| ESP-IDF | Espressif IoT Development Framework – the official SDK for ESP32 development |
| OTA | Over-the-Air – a mechanism for wirelessly updating device firmware |
| NVS | Non-Volatile Storage – a key-value store in flash for persistent configuration data |
| LittleFS | A lightweight file system designed for microcontrollers with flash memory |
| DIO | Dual I/O – a SPI flash operating mode using 2 data lines |
| Clangd | A C/C++ language server that provides IDE features such as code completion and error checking |
| Partition Table | A data structure in flash that describes how flash memory is divided among different uses |
| sdkconfig.defaults | A file that provides default values for ESP-IDF configuration options |

---

*End of Specification*
