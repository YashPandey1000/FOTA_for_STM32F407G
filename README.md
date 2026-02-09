# STM32 Firmware Over-The-Air (FOTA) Update System
### Using Custom Bootloader, ESP32 and AWS S3

---

## 📌 Project Overview

This project presents a **robust and fail-safe Firmware Over-The-Air (FOTA) update system** developed for the **STM32F407VG microcontroller**. The system enables remote firmware updates without requiring physical access to the device, while ensuring system stability and recovery in the event of update failure.

A **custom bootloader** is implemented in a protected region of the internal Flash memory. The bootloader manages firmware reception, Flash programming, firmware validation, and controlled application switching using a **dual-application (Slot A / Slot B) architecture**. Firmware updates are delivered via an **ESP32 Wi-Fi module**, and firmware binaries are hosted on **AWS S3 cloud storage**.

---

## 🎯 Objectives

- Enable remote firmware updates without physical access
- Prevent device bricking during firmware update failures
- Maintain at least one valid firmware image at all times
- Implement a scalable and cloud-based OTA solution
- Ensure reliability under power failure and communication loss

---

## 🧠 System Architecture

AWS S3 (Firmware Storage)
|
v
Python OTA Server (Flask)
|
v
ESP32 (Wi-Fi Module)
|
SPI Communication
|
STM32 Bootloader
| |
Slot A Slot B
(Active) (Update)


---

## 🔑 Key Features

### Custom Bootloader
- Executes immediately after system reset
- Stored in a protected Flash memory region
- Handles firmware reception, validation, and slot switching

### Dual Application (Ping-Pong) Architecture
- Slot A: Active application firmware
- Slot B: Update / backup firmware
- Guarantees system recovery in case of update failure

### Fail-Safe OTA Mechanism
- Firmware written only to inactive slot
- Execution switch occurs only after successful validation
- Automatic rollback to previous firmware on failure

### Cloud-Based Firmware Delivery
- Firmware binaries hosted on AWS S3
- Version control using `version.txt`
- Scalable and globally accessible update system

---

## 🛠 Hardware Platform

| Component | Description |
|--------|-------------|
| STM32F407VG | ARM Cortex-M4 @ 168 MHz |
| ESP32 | Wi-Fi communication module |
| SPI Interface | Firmware transfer |
| ST-LINK | Programming and debugging |

---

## 🧑‍💻 Software Tools Used

| Tool | Purpose |
|-----|--------|
| STM32CubeIDE | Bootloader and application development |
| STM32CubeProgrammer | Flash programming and verification |
| STM32 HAL Library | Peripheral and Flash abstraction |
| Arduino IDE | ESP32 firmware development |
| Python Flask | OTA firmware server |
| AWS S3 | Cloud firmware storage |

---

## 📂 Repository Structure

.
├── bootloader.c # STM32 custom bootloader
├── slot_a.c # Application firmware (Slot A)
├── slot_b.c # Application firmware (Slot B)
├── esp32.c # ESP32 OTA client code
├── server.py # Python Flask OTA server
├── slot_a.bin # Compiled firmware binary (Slot A)
├── slot_b.bin # Compiled firmware binary (Slot B)
├── version.txt # Firmware version file
└── README.md


---

## 🔄 OTA Update Workflow

1. STM32 resets and executes the bootloader
2. Bootloader checks active firmware slot
3. ESP32 connects to Wi-Fi network
4. OTA server checks firmware version on AWS S3
5. New firmware version detected
6. Firmware downloaded via ESP32
7. Firmware transferred to STM32 via SPI
8. Firmware written to inactive Flash slot
9. Firmware integrity validated
10. Bootloader switches execution to updated firmware

---

## 🧪 Testing and Validation

The FOTA system was tested extensively under various conditions:

- Multiple OTA update cycles
- Power failure during firmware update
- Corrupted firmware transmission
- SPI communication interruption
- Flash memory read-back verification

The system consistently prevented execution of invalid firmware and safely recovered from failures.

---

## 📊 Experimental Results

- Reliable firmware transfer over SPI
- Correct Flash erase and write operations
- Successful application slot switching
- Safe fallback to previous firmware
- No device bricking observed

---

## 🔐 Failure Handling

| Failure Scenario | System Response |
|-----------------|----------------|
| Power loss | Rollback to previous firmware |
| Corrupted image | Firmware rejected |
| SPI error | Update aborted safely |
| Invalid version | Update ignored |

---

## 🔮 Future Enhancements

- Secure boot and firmware authentication
- Encrypted firmware updates
- MQTT / HTTPS OTA support
- Delta firmware updates
- AWS IoT Core integration

---

## 👤 Author

**Yash Pandey **  
Embedded Systems & Firmware Developer  
STM32 • ESP32 • Embedded C • FOTA • IoT

---

## 🙏 Acknowledgements

- Awesome README Templates  
  https://awesomeopensource.com/project/elangosundar/awesome-README-templates
- Awesome README  
  https://github.com/matiassingers/awesome-readme
- How to Write a Good README  
  https://bulldogjob.com/news/449-how-to-write-a-good-readme-for-your-github-project

---

## 📜 License

This project is developed for **academic, educational, and research purposes**.

---

