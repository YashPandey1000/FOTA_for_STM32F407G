# CDAC_DESD_Group_3_Project
This project implements a robust Firmware Over-The-Air (FOTA) update system for an STM32F407 microcontroller, managed by an ESP32 host. The architecture utilizes a dual-bank update strategy to ensure system reliability and fail-safe recovery.

# STM32-ESP32 Dual-Slot FOTA System

An interdisciplinary Firmware Over-The-Air (FOTA) update system that leverages an **ESP32** as a cloud-bridge to update an **STM32F407** target. The system uses a dual-bank strategy for fail-safe firmware deployments.

## 📌 Project Overview
This project manages the entire lifecycle of a firmware update: from detection on **AWS S3** to physical flashing via the **STM32 ROM Bootloader**, and finally jumping to the new application using a **Custom Flash Bootloader**.

### Key Features
* **Dual-Slot Architecture**: Updates are flashed to `Slot B` while `Slot A` remains a known-good fallback.
* **Cloud-Native Versioning**: Uses AWS S3 **ETags** via HTTP HEAD requests to detect new firmware without full downloads.
* **Atomic Commitment**: A metadata sector (Sector 11) ensures the system only boots verified updates.
* **Real-time Monitoring**: ESP32 provides a CLI progress bar for flash status and LED diagnostics on the STM32.

---

## 🛠 Hardware Configuration
The system requires a common ground between the ESP32 and STM32. Wiring is critical for the synchronization phase.

| ESP32 Pin | STM32 Pin | Function |
| :--- | :--- | :--- |
| **Pin 5** | **BOOT0** | Force System Memory Mode |
| **Pin 4** | **NRST** | Hardware Reset |
| **Pin 17 (TX)** | **PA10 (RX)** | USART1 Communication |
| **Pin 16 (RX)** | **PA9 (TX)** | USART1 Communication |
| **GND** | **GND** | Common Ground Reference |

[Image of STM32F407 discovery board pinout highlighting USART1 PA9 and PA10]

---

## 📂 Repository Structure
```text
├── esp32_host/           # Arduino code for ESP32 (Cloud & ROM Protocol)
├── stm32_bootloader/     # STM32 Custom Flash Bootloader (Slot Jumper)
├── stm32_app/            # Main Application / Connectivity Test
├── hardware/             # Schematics and Pin Maps
└── docs/                 # Workflow diagrams and logic documentation
