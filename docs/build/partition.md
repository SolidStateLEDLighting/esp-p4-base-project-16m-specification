## Flash Partition Table Specification

The purpose of this application is to provide correct FLASH space for AI workloads and maintain an ability to update both the AI model(s) and the firmware application.

The project uses a **custom partition table** defined in `partitions.csv`. The partition table offset is set to `0xc000` (required when flash encryption is enabled). All memory offsets are calculated automatically by the build system.   Do not include offsets in any generated partition table.

### Partition Layout

| Name | Type | Subtype | Size | Notes |
|---|---|---|---|---|
| `nvs` | data | nvs | 24 KB (0x6000) | Non-volatile storage; cannot be encrypted directly |
| `nvs_key` | data | nvs_keys | 4 KB (0x1000) | NVS encryption keys; marked encrypted |
| `phy_init` | data | phy | 4 KB (0x1000) | PHY calibration data; marked encrypted |
| `otadata` | data | ota | 8 KB (0x2000) | OTA boot selection metadata |
| `ota_0` | app | ota_0 | 4.0 MB (0x400000) | Primary OTA application slot |
| `ota_1` | app | ota_1 | 4.0 MB (0x400000) | Secondary OTA application slot |
| `littlefs` | data | littlefs | 7 MB (0x700000) | LittleFS file system partition |
| `coredump` | data | coredump | 994 KB (0x0F6000) | Coredump partition |

### Excluded Services
There will NOT be a factory image because we much conserve FLASH space.

### Memory Summary
Claude will calculate and append as comments in the partition tables the following:

- Total flash capacity (decimal bytes and hex)
- Total space allocated (decimal bytes and hex)
- Unused / reserved space (decimal bytes and hex)

### OTA (Over-the-Air Update) Support
The partition layout explicitly supports dual-bank OTA updates. The `otadata` partition controls which application slot (`ota_0` or `ota_1`) is booted. Each OTA application partition is 4.0 MB, providing ample space for commercial-grade firmware images (without embedded AI models).

### File System
A `littlefs` partition is included for persistent file storage. I would like Claude to evaluate this size for basic storage and possible AI model storage.

### Flash Encryption Notes
The partition table is prepared for optional flash encryption. The following rules apply when encryption is activated:

- The `nvs` partition cannot be encrypted at the partition level, but its data is stored in an encrypted form when flash encryption is enabled system-wide.
- Application (`app`) partitions are always treated as encrypted when system-level encryption is active; no explicit `encrypted` flag is needed. 
- The `nvs_key` and `phy_init` partitions are flagged `encrypted` and require the boot partition table offset to be set to `0xc000` (already configured in this project). 

### Working Facts
- 1024 bits x 1024 bits = 1M megabyte
