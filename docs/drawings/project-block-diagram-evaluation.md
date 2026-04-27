## Evaluation: project-block-diagram.drawio.png

> **Note:** This drawing is a test diagram to establish diagramming conventions. It was not intended to match the hardware in this project.

---

### Overall Architecture

The diagram is titled **"Project Block Diagram"** and depicts a hub-and-spoke embedded firmware architecture running on an Espressif microcontroller. Three horizontal layers are visible:

1. **Default Main Component** (tan/yellow background) — the ESP-IDF `main` component
2. **Project Defined Components** (green background) — user-created ESP-IDF components
3. **ESP-IDF / ESP Components** (orange, bottom) — the framework and its built-in components

---

### App Entry and Main Component

- The application entry point is labeled **App Entry** and flows via an arrow into **`main.cpp`**
- `main.cpp` resides inside the Default Main Component region
- `main.cpp` instantiates a central **System Object** (depicted as a blue rounded rectangle)
- The System Object displays RTOS Task labels on its left side — these are shown at the overview level so a viewer can see the total task count across the project at a glance. The System Object has additional internal structure not exposed in this diagram.
- The System Object is the coordination hub for all Project Defined Components
- The Boot Switch and Input Switches are external hardware owned and governed directly by the System Object. Their internal handling is part of the System Object's specification, which is not yet defined.

---

### Project Defined Components

Seven components are shown in the green region. Each component follows an identical structural pattern:

- An **RTOS** indicator on its left edge (green box) — indicating it receives calls or signals via RTOS mechanisms
- An **RTOS Task** label at its top right — indicating it runs its own dedicated FreeRTOS task
- An **API** label at its bottom — indicating it exposes a public interface to the System Object
- A directional or bidirectional arrow connecting it to the System Object — arrow direction indicates the flow of data or commands

The seven components, top to bottom:

| Component | External Hardware Connected |
|---|---|
| **Bus SPI** | OLED Display |
| **Control Heater** | Heater Elements |
| **Control LEDs** | Pilot Light LEDs / Nightlight LED |
| **Display OLED** | OLED Display |
| **Network AWS** | None — dependent on Network Wifi for TCP/IP access |
| **Network Wifi** | Antenna (wireless) |
| **Sensors ADC** | Temperature and Light Sensor Input |

**"8 Other RTOS Tasks"** is noted at the bottom of the component region for reference only. The full task inventory will be expanded upon as each component object is presented in detail.

---

### External Hardware

Six external hardware connections are shown on the right side:

| Hardware | Notes |
|---|---|
| 1 Boot Switch | Development only — owned by System Object |
| 6 Input Switches | Owned and governed directly by the System Object |
| OLED Display | Driven by Bus SPI and Display OLED components |
| Heater Elements | Driven by Control Heater component |
| Pilot Light LEDs / Nightlight LED | Driven by Control LEDs component |
| Temperature and Light Sensor Input | Read by Sensors ADC component |

---

### ESP-IDF Layer

- The ESP-IDF layer sits at the bottom and underlies all components
- **ESP Components** (orange boxes) are shown as the foundation
- Additional RTOS tasks exist within this layer and will be detailed as each component is specified

---

### Component Naming Convention

The component names follow a consistent two-part naming pattern: `category_subject`:

- `bus_spi`
- `control_heater`
- `control_leds`
- `display_oled`
- `network_aws`
- `network_wifi`
- `sensors_adc`

This convention groups components by functional category (bus, control, display, network, sensors).

---

### Design Observations

1. **Each component is self-contained and concurrent** — every Project Defined Component owns its own RTOS task, encapsulating its concurrency internally and exposing only an API outward.
2. **The System Object is the sole coordinator** — it holds references to all components and orchestrates interactions between them. No component-to-component direct coupling is visible.
3. **The System Object owns hardware directly** — in addition to coordinating components, the System Object governs certain hardware (switches) internally.
4. **Network AWS depends on Network Wifi** — AWS has no independent network path; all AWS traffic flows through the Network Wifi component.
5. **The Main Component is thin** — `main.cpp` only creates the System Object; all application logic is delegated downward.
6. **APIs are the only coupling boundary** — components expose APIs and the System Object calls them, keeping dependencies explicit and bounded.
7. **The architecture maps directly to ESP-IDF components** — each box in the green region corresponds to a directory under `components/` with its own `CMakeLists.txt`.
8. **Arrow direction is meaningful** — unidirectional arrows show one-way data or command flow; bidirectional arrows indicate two-way exchange. This convention applies to all diagrams in this project.
