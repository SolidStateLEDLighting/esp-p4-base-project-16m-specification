## File and Directory Structure

## Project File Structure

```
esp-p4-base-project-16m/
├── main/                        # Main component (C/C++ source files)
├── main/src                     # Main component source files
├── main/include                 # Main component include files
├── components/                  # User created components (C/C++ source files)
├── .clangd                      # Clangd language server configuration for VS Code
├── .gitignore                   # Git ignore rules
├── CMakeLists.txt               # Top-level CMake build file
├── README.md                    # Repository description
├── partitions.csv               # Custom flash partition table
└── sdkconfig.defaults           # Default SDK configuration overrides
```

The `main/src` subdirectory contains the application entry point inside in a file called `main.cpp` and a component-level `CMakeLists.txt`. The primary language of the application code is **C++** with the build system written in **CMake**.

We will create an empty `main.hpp` in `main/include` directory as a placeholder.  Add the `#include "main.hpp"` statement to main.cpp. 

---

### IDE Support

The `.clangd` file that is included at the project root will support the Clangd language server in VS Code. The configuration removes compiler flags prefixed with `-f*` and `-m*`, which are specific to the Xtensa/RISC-V GCC toolchain and would otherwise cause Clangd errors when parsing source files.

---

## SDK Default Configuration (sdkconfig.defaults)

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

### File Format
When Claude creates the sdkconfig.default file, each entry into that file should be organized the same way it is organized in a normal sdkconfig file.  Each entry should be grouped in its category.    The category name should be commented as follows:

#
# `category name`
#

There is the be no line feeds at the top of the file.  There are to be no line feeds inside each category.  There are to be 1 line feed between each category inside the sdkconfig.defaults file.

---