## Flash Partition Table Specification

The project uses a **custom partition table** defined in `partitions.csv`. The partition table offset is set to `0xc000` (required when flash encryption is enabled). All memory offsets are calculated automatically by the build system.

### Partition Layout

| Name | Type | Subtype | Size | Notes |
|---|---|---|---|---|
| `nvs` | data | nvs | 24 KB (0x6000) | Non-volatile storage; cannot be encrypted directly |
| `nvs_key` | data | nvs_keys | 4 KB (0x1000) | NVS encryption keys; marked encrypted |
| `phy_init` | data | phy | 4 KB (0x1000) | PHY calibration data; marked encrypted |
| `otadata` | data | ota | 8 KB (0x2000) | OTA boot selection metadata |
| `ota_0` | app | ota_0 | 5.0 MB (0x4E2000) | Primary OTA application slot |
| `ota_1` | app | ota_1 | 5.0 MB (0x4E2000) | Secondary OTA application slot |
| `littlefs` | data | littlefs | 4 MB (0x3E8000) | LittleFS file system partition |

### Excluded Services
We will not be making any plans to capture coredumps.  Exclude that possible need from the partition design.

There will NOT be a factory image because we are low on FLASH space already and OTA services are important.   We can't afford to dedicate a third space to program code and is rarely and most of the time, never used.

### Memory Summary

Claude will calculate and append as comments in the partition tables the following:

- Total flash capacity (decimal bytes and hex)
- Total space allocated (decimal bytes and hex)
- Unued / reserved space (decimal bytes and hex)

### OTA (Over-the-Air Update) Support

The partition layout explicitly supports dual-bank OTA updates. The `otadata` partition controls which application slot (`ota_0` or `ota_1`) is booted. Each OTA application partition is 5.0 MB, providing ample space for commercial-grade firmware images.

### File System

A `littlefs` partition of approximately 4 MB is included for persistent file storage. This is suitable for storing configuration files, certificates, logs, or other user data.

### Flash Encryption Notes

The partition table is prepared for optional flash encryption. The following rules apply when encryption is activated:

- The `nvs` partition cannot be encrypted at the partition level, but its data is stored in an encrypted form when flash encryption is enabled system-wide.
- Application (`app`) partitions are always treated as encrypted when system-level encryption is active; no explicit `encrypted` flag is needed. 
- The `nvs_key` and `phy_init` partitions are flagged `encrypted` and require the boot partition table offset to be set to `0xc000` (already configured in this project). 
