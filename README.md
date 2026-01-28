# Robust Dual-Slot FOTA Manager for STM32F407



#  Project Overview
This project implements an autonomous, cloud-connected Firmware Over-The-Air (FOTA) update system. It utilizes an **ESP32** as a host controller to bridge **AWS S3** cloud storage with an **STM32F407** target. The system is designed with a "safety-first" philosophy, employing a dual-bank memory strategy that allows firmware updates to be streamed into a secondary slot while maintaining a stable primary application, ensuring zero-risk deployments.
Moving beyond traditional asynchronous methods, this system leverages SPI (Serial Peripheral Interface) to create a high-bandwidth data pipeline. The architecture retains a "safety-first" philosophy, employing a dual-bank memory strategy that streams firmware into a secondary slot while maintaining a stable primary application, ensuring zero-risk deployments with superior transmission speeds.



# Hardware Used
* **STM32F407 Discovery Board:** The target microcontroller (Cortex-M4) featuring the dual-slot application logic.
* **ESP32 (NodeMCU/DevKit):** The host controller acting as the SPI Master, managing Wi-Fi connectivity and the update clock.

* **Physical Interconnects:** * **SPI1 (PA4/PA5/PA6/PA7):** High-speed synchronous programming interface.
    * **GPIO Control:** Reset (NRST) and Boot Mode (BOOT0) lines.
    * **Common Ground:**Absolute reference required for signal integrity and noise immunity in high-frequency clocking.



#  Hardware Configuration
The system requires a common ground between the ESP32 and STM32. Wiring is critical for the synchronization phase.The shift to SPI requires a 4-wire bus topology. Unlike UART, wiring length and impedance matching become more critical due to the clock signal (SCK).

| ESP32 Pin | STM32 Pin | Function |
| :--- | :--- | :--- |
| **GPIO 23 (MOSI)** | **PA7 (MOSI)** | Data: Host to Target |
| **GPIO 19 (MISO)** | **PA6 (MISO)** | Data: Target to Host |
| **GPIO 18 (SCK)** | **PA5 (SCK)** | Clock Source |
| **GPIO 5 (CS/SS)** | **PA4 (NSS)** | Chip Select / Slave Select |
| **GND** | **GND** | Common Ground Reference |


#  Software Used
* **STM32CubeIDE:** Used for developing the Custom Flash Bootloader and the main application code (C/HAL).
* **Arduino IDE:** Used for developing the ESP32 Host logic (C++/Arduino).
* **AWS S3:** Cloud infrastructure for binary storage and version metadata.
* **ST Serial Bootloader Protocol:** The low-level communication standard for flash memory access.
* **ST SPI Bootloader Protocol:** The specific command set (documented in ST AN4286) used for communicating with the STM32 system memory via SPI.


# Key Components & Process Highlights

### Key Components
* **Cloud Gateway (AWS S3):** Acts as the remote repository for firmware binaries, utilizing **ETags** for efficient version tracking.
* **Host Controller (ESP32):** Orchestrates the update lifecycle, including cloud polling, physical pin manipulation (BOOT0/NRST), and protocol execution.
* **Custom Flash Bootloader (STM32):** A dedicated first-stage bootloader residing in Sector 0 that manages the logic-based jump between Slot A and Slot B.
* **SPI Master (ESP32):** Orchestrates the update lifecycle. It controls the bus clock, manages the Chip Select (NSS) line to frame transactions, and handles physical pin manipulation (BOOT0/NRST).
* **SPI Slave (STM32 Bootloader):** Listens on the SPI bus. It receives commands and data strictly on the clock edges provided by the ESP32.
* **Metadata Sector:** A reserved Flash region (Sector 11) used to store the "Magic Number" and boot flags required for atomic commitment.

### Process Highlights
1.  **Version Interrogation:** The ESP32 performs an HTTP HEAD request to AWS to compare the cloud ETag with the local identifier, saving energy and data.
2.  **Hardware Handshake:** Upon detecting a new version, the ESP32 drives the STM32 **BOOT0** High and pulses **NRST** to force entry into the ROM-based System Memory. Crucial Step: ESP32 waits a defined stabilization period (t_boot) before asserting NSS (Chip Select) to begin SPI transactions.
3.  **Streamed Programming:** Firmware is pulled from the cloud and piped to the STM32 via SPI. The ESP32 sends a Write Memory command followed by data chunks.
**Protocol: ST SPI Bootloader (Ack/Nack handling).Synchronization: ESP32 sends "Dummy Bytes" to clock out the Acknowledgement (ACK) byte from the STM32.**
5.  **Atomic Commitment:** Once the binary is fully written to Slot B, the ESP32 writes the metadata. The STM32 custom bootloader validates this metadata before allowing a jump to the new code.



# Achievements
* **Fail-Safe Redundancy:** Successfully implemented a dual-slot layout that prevents system bricking during interrupted updates.
* **Autonomous Versioning:** Optimized the update detection mechanism to verify versions without downloading full binary files until necessary.
* **Inter-Protocol Mastery:** Integrated diverse communication standards, including HTTPS for cloud access and the ST Serial Bootloader protocol (8E1) for hardware flashing.
* **Real-Time Observability:** Developed a custom CLI progress monitor and LED diagnostic system for real-time status tracking during the update cycle.





