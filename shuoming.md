# SZ901 Hardware and Software User Manual (V3.1.2)

---

## Table of Contents

- 1 Software Overview
    - 1.1 Core Features
- 2 Operating Environment & Dependencies
- 3 Functional Modules Description
    - 3.1 Home / 3.2 Device / 3.3 Flash / 3.4 Converter / 3.5 Notes / 3.6 System
- 4 Troubleshooting (FAQ)
- 5 Known Issues and Explanations
- 6 Technical Support

---

## 1. Software Overview

SZ901 is an adapted hardware control software, available in both Windows and Linux versions. It supports configuring hardware parameters and provides a simple way to quickly flash programs to Flash memory.

### 1.1 Core Features

- **Concurrent Multi-port Flashing**: Supports operating up to 4 independent JTAG ports simultaneously.
- **Intelligent Low-level Scheduling**: Invokes the Vivado environment to erase and write to Flash.
- **Comprehensive Auxiliary Functions**: Integrates network device discovery and configuration modules, as well as firmware format conversion tools (BIT / BIN / MCS conversion).

---

## 2. Operating Environment & Dependencies

To properly run this software and execute firmware flashing, your computer must meet the following requirements:

1. **Operating System**: Windows 10 and above. For Linux, Ubuntu 20 and above are recommended; other versions or systems have not been tested!
2. **Underlying Environment**: A compatible version of **Xilinx Vivado** software must be installed on your computer.
3. **Drivers**: No driver installation is required.

---

## 3. Functional Modules Description

You can switch between different functional modules using the left navigation bar on the main interface after launching the software.

### 3.1 Home
Displays basic information and the vision of the software.

### 3.2 Device
This module is used to scan for hardware devices within the LAN and perform underlying communication configurations (such as IP setting), which is applicable to enhanced downloader models that support Ethernet debugging.

### 3.3 Flash
This is the core operation area of the software, containing four independent JTAG port tabs:

- **Flash Configuration**: Here you can specify your: 
  - **Vivado Path** (e.g., `D:\Xilinx\Vivado\2024.2`).
  - **Series Model**: Drop-down to select the connected SZ901 hardware FPGA model.
  - **BIN File**: Click "Browse" to select the `.bin` file to be written (if it is an `.mcs` file, you can convert it to a `.bin` file via the **Converter** page).
  - **Flash Capacity/Address**: Select the Flash capacity. If you have special requirements, you can adjust the flashing start address, which is suitable for storing multiple bin files in Flash for switching.
  - **Flash Options**: Select either BIN file verification or readback. Only one needs to be selected.
- **Execution Control**: Start flashing.
- **Flash Status**:
  - Reads the hardware identification code of the currently connected chip, displays the flashing time and total time, and shows the bin file size.
- **Log Window**: A real-time output log is provided at the bottom of the interface, supporting filtering levels such as "All", "Info", "Warning", and "Error" to help you diagnose potential issues.

### 3.4 Converter

If you only have a compiled `.bit` file on hand, you need to convert it to the format required for offline boot:

1. Go to the "Converter" tab.
2. Import your original file.
3. Select the target output format and bus width parameters.
4. One-click to generate the required `.bin` file.

### 3.5 Notes
Supports dynamically pulling and displaying the latest software release notes, update logs, precautions, and other contents from the cloud.

### 3.6 System

- Here you can switch the software display language (Simplified Chinese / Traditional Chinese / English).
- You can configure the behavior when the software is closed (Exit directly or Minimize to system tray).
- Check if there are updates for the latest software version.
- View related copyright and technical support information.

---

## 4. Troubleshooting (FAQ)

> [!WARNING]
> If you encounter any operational abnormalities during use, please refer to the following troubleshooting steps first.

**Q1: The device page scans devices in the network, but cannot display detailed device information, such as IP, current JTAG speed, etc.**

- Check if the device and the PC are on the same network segment.

**Q2: Vivado can normally display the Hardware window in Vivado Hardware Manager, but cannot recognize the FPGA.**

- Lower the JTAG setting speed.
- Check the physical JTAG connection.

**Q3: Hardware connection failure prompts.**

- Ensure the target development board is **powered on** and the network connection is normal.
- Ensure no other Vivado Hardware Manager instances on the same computer are occupying this hardware port.

**Q4: Log: Are there similar errors during flashing?**
> Vivado [STDERR]: ERROR: [Labtools 27-2223] Unable to connect to hw_server with URL "TCP:localhost:3123".

> Vivado [STDERR]: ERROR: [Labtools 27-2269] No devices detected on target localhost:3123/xilinx_tcf/Xilinx/192.168.100.234:2540.

- **Conclusion**: It does not affect normal flashing, just ignore it!
- **Reason**: The error occurs because the TCL command speed is relatively fast, and connection and FPGA detection take time. If you manually establish a connection on the Vivado software page which is slower, this situation will not occur!

---
## 5. Known Issues and Explanations

### 5.1 Flash from Certain Brands Do Not Enable X4 Mode by Default
Flash chips from some manufacturers (such as GigaDevice, Puya, etc.) do not have Quad (X4) mode enabled by default when leaving the factory. If X4 mode is not enabled, the BIN file will only run in X1 (single-wire) mode.

**Our Solution:**
The flashing software has supported automatically enabling X4 mode for Flash, but currently it **only provides support for the following FPGA series**:

- Spartan-7 (S7)
- Artix-7 (A7)
- Kintex-7 (K7)
- Kintex UltraScale (KU)
- Kintex UltraScale+ (KUP)

> [!NOTE]
> If the FPGA model you are using is not in the above support list and you need to enable this feature, please contact the author for adaptation.

---
## 6. Technical Support

If you encounter problems that cannot be solved through the above steps, or need further technical support:

- Please keep the "All Logs" information output at the bottom of the interface.
- Contact the technical support team: sz_tech_sz (WeChat)
- For the latest software, please check and download on the System page.
