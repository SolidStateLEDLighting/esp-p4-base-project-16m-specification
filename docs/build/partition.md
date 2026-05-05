## 1. Flash Partition Table Specification
The purpose of this application is to provide correct FLASH space for AI workloads and maintain an ability to update both the AI model(s) and the firmware application.

- The LittleFS partition is defined but not initialized in this base project. Application code must mount it explicitly.

- Flash encryption is supported by the partition layout but is **not enabled by default**. Enabling it requires additional steps in `menuconfig` and physical device provisioning.

- OTA update logic is not included in this base project. The partition table is prepared for OTA; the application developer must implement the OTA workflow.


The project uses a **custom partition table** defined in `partitions.csv`. The partition table offset is set to `0xc000` (required when flash encryption is enabled). All memory offsets are calculated automatically by the build system.   Do not include offsets in any generated partition table.

### 1.1. Partition Layout
| Name | Type | Subtype | Size | Notes |
|---|---|---|---|---|
| `nvs` | data | nvs | 24 KB (0x6000) | Non-volatile storage; cannot be encrypted directly |
| `nvs_key` | data | nvs_keys | 4 KB (0x1000) | NVS encryption keys; marked encrypted |
| `phy_init` | data | phy | 4 KB (0x1000) | PHY calibration data; marked encrypted |
| `otadata` | data | ota | 8 KB (0x2000) | OTA boot selection metadata |
| `ota_0` | app | ota_0 | 4.0 MB (0x400000) | Primary OTA application slot |
| `ota_1` | app | ota_1 | 4.0 MB (0x400000) | Secondary OTA application slot |
| `littlefs` | data | littlefs | 6.0 MB (0x600000) | LittleFS file system partition |
| `coredump` | data | coredump | 984 KB (0x0F6000) | Coredump partition |

### 1.2. Excluded Services
There will NOT be a factory image because we must conserve FLASH space.

### 1.3. Memory Summary
Claude will calculate and append to the file after the table has been written the following comments:

`#`
- Total flash capacity (decimal bytes and hex)
- Total space allocated (decimal bytes and hex)
- Unused / reserved space (decimal bytes and hex)
`#`

Since this is just a specification, this action does not occur here.   Claude will append these comments to the final partition table when real source code is being generated.

### 1.4. OTA (Over-the-Air Update) Support
The partition layout explicitly supports dual-bank OTA updates. The `otadata` partition controls which application slot (`ota_0` or `ota_1`) is booted. Each OTA application partition is 4.0 MB, providing ample space for commercial-grade firmware images (without embedded AI models).

### 1.5. File System
A `littlefs` partition is included for persistent file storage. I would like Claude to evaluate this size for basic storage and possible AI model storage.

### 1.6. Flash Encryption Notes
The partition table is prepared for optional flash encryption. The following rules apply when encryption is activated:

- The `nvs` partition cannot be encrypted at the partition level, but its data is stored in an encrypted form when flash encryption is enabled system-wide.
- Application (`app`) partitions are always treated as encrypted when system-level encryption is active; no explicit `encrypted` flag is needed. 
- The `nvs_key` and `phy_init` partitions are flagged `encrypted` and require the boot partition table offset to be set to `0xc000` (already configured in this project). 

### 1.7. Working Facts
- 1024 bits x 1024 bits = 1Mbit
- 1Mbit x 8 = 8 Mbit = 1MB
