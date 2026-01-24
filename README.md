# Robust Dual-Slot FOTA Manager for STM32F407

---

#  Project Overview
This project implements an autonomous, cloud-connected Firmware Over-The-Air (FOTA) update system. It utilizes an **ESP32** as a host controller to bridge **AWS S3** cloud storage with an **STM32F407** target. The system is designed with a "safety-first" philosophy, employing a dual-bank memory strategy that allows firmware updates to be streamed into a secondary slot while maintaining a stable primary application, ensuring zero-risk deployments.

---

# Hardware Used
* **STM32F407 Discovery Board:** The target microcontroller (Cortex-M4) featuring the dual-slot application logic.
* **ESP32 (NodeMCU/DevKit):** The host controller managing Wi-Fi connectivity and STM32 hardware pins.

* **Physical Interconnects:** * **USART1 (PA9/PA10):** Primary programming interface.
    * **GPIO Control:** Reset (NRST) and Boot Mode (BOOT0) lines.
    * **Common Ground:** Essential reference for signal integrity.

---


#  Hardware Configuration
The system requires a common ground between the ESP32 and STM32. Wiring is critical for the synchronization phase.

| ESP32 Pin | STM32 Pin | Function |
| :--- | :--- | :--- |
| **Pin 4** | **BOOT0** | Force System Memory Mode |
| **Pin 5** | **NRST** | Hardware Reset |
| **TX0** | **PA10 (RX)** | USART1 Communication |
| **RX0** | **PA9 (TX)** | USART1 Communication |
| **GND** | **GND** | Common Ground Reference |


#  Software Used
* **STM32CubeIDE:** Used for developing the Custom Flash Bootloader and the main application code (C/HAL).
* **Arduino IDE:** Used for developing the ESP32 Host logic (C++/Arduino).
* **AWS S3:** Cloud infrastructure for binary storage and version metadata.
* **ST Serial Bootloader Protocol:** The low-level communication standard for flash memory access.


# Key Components & Process Highlights

### Key Components
* **Cloud Gateway (AWS S3):** Acts as the remote repository for firmware binaries, utilizing **ETags** for efficient version tracking.
* **Host Controller (ESP32):** Orchestrates the update lifecycle, including cloud polling, physical pin manipulation (BOOT0/NRST), and protocol execution.
* **Custom Flash Bootloader (STM32):** A dedicated first-stage bootloader residing in Sector 0 that manages the logic-based jump between Slot A and Slot B.
* **Metadata Sector:** A reserved Flash region (Sector 11) used to store the "Magic Number" and boot flags required for atomic commitment.

### Process Highlights
1.  **Version Interrogation:** The ESP32 performs an HTTP HEAD request to AWS to compare the cloud ETag with the local identifier, saving energy and data.
2.  **Hardware Handshake:** Upon detecting a new version, the ESP32 drives the STM32 **BOOT0** High and pulses **NRST** to force entry into the ROM-based System Memory.

3.  **Streamed Programming:** Firmware is pulled from the cloud and piped directly to the STM32 via **USART1 (PA9/PA10)** in 256-byte chunks using Even Parity.
4.  **Atomic Commitment:** Once the binary is fully written to Slot B, the ESP32 writes the metadata. The STM32 custom bootloader validates this metadata before allowing a jump to the new code.

---

# Achievements
* **Fail-Safe Redundancy:** Successfully implemented a dual-slot layout that prevents system bricking during interrupted updates.
* **Autonomous Versioning:** Optimized the update detection mechanism to verify versions without downloading full binary files until necessary.
* **Inter-Protocol Mastery:** Integrated diverse communication standards, including HTTPS for cloud access and the ST Serial Bootloader protocol (8E1) for hardware flashing.
* **Real-Time Observability:** Developed a custom CLI progress monitor and LED diagnostic system for real-time status tracking during the update cycle.

---




