# 🔵 BLE-MIRABILIS-BLUE

**A Bluetooth Low Energy peripheral firmware designed for mobile developers who want to learn BLE by working with a real device.**

BLE-MIRABILIS-BLUE is a tutorial firmware for an **nRF52840 / nice!nano-compatible board** that exposes a purpose-built GATT interface for experimenting with common BLE operations — from simple reads and writes to notifications, pairing, bonding, encryption, and bidirectional file transfer.

The repository contains the ready-to-flash firmware and its developer documentation.

> **Current firmware:** v0.6.2  
> **Firmware image:** `mirabilisblue.uf2`  
> **BLE device name:** `BLE-MIRABILIS-BLUE`

---

## Why does this project exist?

Learning Bluetooth Low Energy from API documentation alone can be difficult.

Concepts such as GATT services, characteristics, notifications, Write Without Response, pairing, bonding, encrypted attributes, and file-transfer protocols become much easier to understand when you can interact with an actual peripheral.

BLE-MIRABILIS-BLUE provides a small, predictable BLE device specifically for that purpose.

It can be used with:

- iOS / CoreBluetooth
- Android BLE APIs
- Kotlin Multiplatform BLE libraries
- Python / Bleak
- nRF Connect
- Any BLE central capable of interacting with custom GATT services

---

# Hardware

The firmware targets a **nice!nano-compatible nRF52840 board**.

The nRF52840 provides:

- 64 MHz Arm Cortex-M4F
- 1 MB Flash
- 256 KB RAM
- Bluetooth Low Energy
- USB device support

The firmware is distributed as a UF2 image and can be installed through the board's UF2 bootloader.

---

# Installing the firmware

## 1. Enter the UF2 bootloader

Connect the board to your computer and place it in bootloader mode.

A USB volume named:

```text
NICENANO
```

should appear.

## 2. Flash BLE-MIRABILIS-BLUE

Copy:

```text
mirabilisblue.uf2
```

to the `NICENANO` volume.

The board will reboot automatically.

## 3. Scan for the peripheral

Using a BLE scanner such as nRF Connect, look for:

```text
BLE-MIRABILIS-BLUE
```

You can now connect and explore the GATT interface.

---

# What can the firmware do?

BLE-MIRABILIS-BLUE progressively demonstrates several BLE communication patterns.

| Feature | GATT operation |
|---|---|
| Device information | Read |
| Basic value storage | Write → Read |
| Observable state | Write → Read + Notify |
| Peripheral-generated events | Notify |
| Unacknowledged writes | Write Without Response → Read |
| Secure communication | Encrypted Write / Read / Notify |
| Pairing and bonding | BLE security |
| File upload | Write Without Response + Notify |
| File download | Notify + Write Without Response |
| Transfer statistics | Encrypted Read |

The custom tutorial service uses:

```text
7E57A000-0000-4B1A-9C00-000000000001
```

See the **GATT Table** for the complete characteristic reference.

---

# File transfer

BLE-MIRABILIS-BLUE includes a small bidirectional file-transfer protocol.

Two characteristics form the transport:

```text
RX  ...000B   Write Without Response
TX  ...000C   Notify
```

Data is divided into BLE packets containing:

```text
Byte 0      STR / ETX
Byte 1      Sequence number
Bytes 2-19  Payload (up to 18 bytes)
```

Packets are transferred in batches of up to **8 chunks**, followed by an ACK/NACK exchange.

The protocol supports:

- Upload
- Download
- Sequence numbering
- Sequence rollover
- Batch acknowledgements
- ACK / NACK
- Transfer cancellation
- End-of-transfer signalling

For the complete protocol, see the **Firmware Developer Guide**.

---

## File storage limitations

BLE-MIRABILIS-BLUE v0.6.2 stores **one file in RAM**.

Maximum file size:

```text
16 KiB / 16,384 bytes
```

A successful new upload replaces the previous file.

The file survives:

```text
BLE disconnect
BLE reconnect
Download
Repeated downloads
```

The file does **not** survive:

```text
Board reset
Power loss
USB cable removal when USB is powering the board
```

This behaviour is intentional. The project focuses on BLE transport rather than persistent storage or filesystem implementation.

---

# Pairing and bonding

Some characteristics are intentionally protected and require an **encrypted BLE connection**.

These operations demonstrate:

```text
Pairing
      ↓
Encryption
      ↓
Bond creation
      ↓
Reconnect
      ↓
Bond reuse
```

Secure characteristics include the secure read/write/notification example, file-transfer characteristics, and transfer statistics.

See the Firmware Developer Guide for details about security requirements and bond-state recovery.

---

# Documentation

This repository includes two complementary documents.

### 📘 Firmware Developer Guide

**`BLE_MIRABILIS_BLUE_Firmware_Developer_Guide_v0.6.2.pdf`**

Start here if you are implementing an application.

It covers:

- Hardware
- BLE architecture
- GATT operations
- Pairing and bonding
- Encrypted characteristics
- File upload
- File download
- ACK/NACK protocol
- File limits
- Testing
- Integration recommendations

### 📋 GATT Reference

**`BLE_MIRABILIS_BLUE_GATT_Table_v0.6.2.pdf`**

A compact reference containing:

- Services
- Characteristics
- UUIDs
- Properties
- Security requirements
- File-transfer constants
- Suggested tests

---

# Suggested learning path

If you are new to BLE, try the characteristics in this order:

```text
Device Information
        ↓
WRITE → READ
        ↓
WRITE → READ + NOTIFY
        ↓
NOTIFY-only events
        ↓
WRITE WITHOUT RESPONSE
        ↓
Pairing & Bonding
        ↓
Encrypted characteristics
        ↓
File Upload
        ↓
File Download
```

This progression is intentional: each operation introduces another concept commonly encountered when integrating real connected devices.

---

# Testing tools

For initial exploration, **nRF Connect** is sufficient for most GATT operations.

For file transfer, use a programmatic BLE client.

The protocol has been tested from macOS using **Python + Bleak**, including binary uploads/downloads and byte-for-byte integrity verification.

For example:

```bash
cmp demo_upload.bin downloaded.bin
```

or:

```bash
shasum -a 256 demo_upload.bin downloaded.bin
```

Matching files should produce no output from `cmp` or identical SHA-256 hashes.

---

# Repository contents

```text
BLE-MIRABILIS-BLUE/
│
├── README.md
│
├── mirabilisblue.uf2
│
└── docs/
    ├── BLE_MIRABILIS_BLUE_Firmware_Developer_Guide_v0.6.2.pdf
    └── BLE_MIRABILIS_BLUE_GATT_Table_v0.6.2.pdf
```

---

# Firmware version

Current release:

```text
BLE-MIRABILIS-BLUE v0.6.2
```

The firmware revision can also be read through the standard Bluetooth Device Information Service:

```text
Firmware Revision String
UUID: 0x2A26
Value: 0.6.2
```

---

# Project scope

BLE-MIRABILIS-BLUE is an **educational and development peripheral**.

It is intended for learning, experimentation, tutorials, prototyping, and BLE client-development exercises.

It is not intended to be production firmware for a commercial device.

GPIO exercises and Device Firmware Update (DFU) are intentionally outside the current project scope.

---

# License

Add the license selected for the project here.

If you redistribute the firmware or documentation, please follow the terms of the repository license.

---

# About the project

BLE-MIRABILIS-BLUE was created as a companion peripheral for a practical Bluetooth Low Energy tutorial aimed at mobile software engineers.

Rather than teaching BLE exclusively through isolated API examples, the project uses one peripheral throughout the learning process — gradually moving from basic GATT operations to the kinds of communication patterns found in real connected products.

**Scan it. Connect to it. Break it. Pair it. Transfer files through it. Build something with it.**
