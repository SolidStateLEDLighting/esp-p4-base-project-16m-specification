# Software Project Specification

## 1. Project: esp-p4-base-project-16m-specification

**Author / Organization:** SolidStateLEDLighting  
**Document Version:** 1.0  
**Date:** April, 2026  

---  

## 2. Overview

### 2.1. Purpose
This project is a minimal base (boilerplate) application for the Espressif microcontroller compiled with the ESP-IDF. Its purpose is to serve as a clean, pre-configured starting point for new firmware projects.   The specific hardware will be outlined from within the specification.

---  

## Order of Project Parsing
- Read the hardware subdirectory first.
- Next read the build subdirectory.
- Finally read the firmware subdirectory.

---  

## 3. Design Constraints and Assumptions

- The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED",  "MAY", and "OPTIONAL" in this specification are to be interpreted as described in RFC 2119.
- Skills for this project exist only in the C:\Users\Upwork\.claude\skills\ directory.  
- Claude will inspect all markdown documents inside this project and look its lists of required skills.  
- If Claude is missing the required skill at the user level, the generation of the project will terminate and a warning will be printed to the command line output.

---  

## 4. Scope

Claude SHALL:
- Create a compilable, minimal ESP-IDF application skeleton
- Create a pre-defined flash partition table
- Write the default SDK configuration file
- Add the required CMakeLists.txt files in all the required places throughout the project.
- Use the IDE tooling support for VS Code via a Clangd configuration file
- Create a main subdirectory located at the root of the project.
- Create a components subdirectory located at the root of the project.

---  

## 5. Possible Intended Function

The following are possible uses by the firmware and as such the base design should try to accomodate the follow workload contexts.

### 5.1. Machine Learning Workload Context

#### 5.1.1. Inference Task
| Parameter | Value |
|---|---|
| Primary task | e.g. image classification / audio keyword detection / anomaly detection |
| Input modality | e.g. camera frame / microphone / ADC sensor data |
| Inference frequency | e.g. continuous / triggered / periodic (every N seconds) |

#### 5.1.2. Model Characteristics
| Parameter | Value |
|---|---|
| Framework | e.g. TensorFlow Lite for Microcontrollers |
| Quantization | e.g. INT8 / INT16 / float32 |
| Approximate model size | e.g. 500 KB |
| Number of models resident simultaneously | e.g. 1 |

#### 5.1.3. Model Delivery and Update Strategy
| Parameter | Value |
|---|---|
| Model delivery method | e.g. baked into firmware / stored in LittleFS / dedicated partition |
| Model update mechanism | e.g. updated via OTA with firmware / updated independently via LittleFS write |
| Update frequency | e.g. infrequent / frequent |

#### 5.1.4. File System Usage
| Data Type | Approximate Size | Notes |
|---|---|---|
| Model files | e.g. 500 KB | |
| Configuration data | e.g. 50 KB | |
| Inference logs | e.g. 200 KB | |
| Certificates | e.g. 10 KB | |
| **Total estimated** | e.g. 760 KB | |

---  

## 6. Glossary

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
