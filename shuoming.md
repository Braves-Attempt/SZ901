# SZ901 Software User Manual

> **Latest Version**: `V3.2.6`  
> **Update Highlights**: Performance Optimization & Expanded Flash Chip Support  

---

### 📌 Release Notes (V3.2.6)

#### 1. Performance & Usability Optimizations
- **Accelerated Burn Speed**: Re-architected link establishment and status detection routines, eliminated hardcoded delays, significantly speeding up programming workflows.
- **One-Click Concurrent Programming**: Added the "ALL One-Click Programming" panel, supporting batch execution across individual port configs or syncing all ports to a designated reference JTAG port.
- **Cleaned Logging Hierarchy**: Optimized log output tiers for a clean, professional console experience.

#### 2. Expanded Flash Chip Support *(Note: Applicable to this host utility; does not affect standard Vivado programming with SZ901)*
- **Hardware Verified Manufacturers (Pass)**:
    - 🟢 **Winbond**
    - 🟢 **GigaDevice**
    - 🟢 **Micron**
    - 🟢 **Puya**
- **Driver-Supported / Pending Hardware Verification**:
    - 🟡 **Infineon / Cypress** *(Newly added in this release; unsupported in previous versions)*
    - 🟡 **Macronix (MXIC)**
    - 🟡 **ISSI**
    - 🟡 **Fudan Microelectronics (FM)**
    - 🟡 **ESMT**, **Microchip / SST**, etc.

> 💡 **Pending Verification Note**:  
> The underlying driver has fully implemented Manufacturer ID detection and QE configuration logic for the above vendors. Full-chain hardware programming verification is pending due to lack of physical board/chip samples. Feedback and co-debugging from users of these chips are warmly welcomed.

---

## Table of Contents

- [Release Notes (V3.2.6)](#-release-notes-v326)
- [1. Troubleshooting & FAQ](#1-troubleshooting--faq)
- [2. Key Features & Operation Guide](#2-key-features--operation-guide)
    - [2.1 Flash Quad (X4) Mode Auto-Enablement](#21-flash-quad-x4-mode-auto-enablement)
    - [2.2 Multi-Port Parallel Burn Isolation](#22-multi-port-parallel-burn-isolation)
    - [2.3 ALL One-Click Concurrent Burn Guide](#23-all-one-click-concurrent-burn-guide)
- [3. Technical Support](#3-technical-support)

---

## 1. Troubleshooting & FAQ

> ⚠️ **Troubleshooting Notice**:  
> If you encounter any unexpected behavior during operation, please refer to the following troubleshooting steps first.

**Q1: The device scan discovers devices on the network, but specific details (IP, current JTAG frequency, etc.) are not displayed.**
- Please verify that the target device and the host PC are on the same physical subnet and local area network (LAN).

**Q2: Vivado opens the Hardware Manager window normally, but cannot recognize the FPGA.**
- Try reducing the configured JTAG clock frequency in settings (e.g., lower to 15 MHz or 6 MHz).
- Check whether the JTAG physical ribbon cable is firmly connected or if excessive cable length is causing signal integrity attenuation.

**Q3: Error: Failed to open hardware target.**
- Ensure that the target board is **powered on normally** and the physical network connection is stable (try pinging the target IP).
- Verify that no other Vivado Hardware Manager instance is running on the host PC and monopolizing the hardware port.

**Q4: Log message: `Failed to detect FPGA device after 10 attempts`.**
- Indicates that the XVC network target opened, but the JTAG chain scan failed to detect any FPGA devices. Check: board power supply, JTAG adapter header orientation, and loose cabling.

**Q5: Log warning: `WARNING: 'open_hw' is deprecated...`.**
- **Conclusion**: Completely harmless to programming; ignore safely.
- **Cause**: Newer Vivado versions recommend `open_hw_manager`. The utility maintains bidirectional command compatibility across legacy and modern Vivado toolchains.

---

## 2. Key Features & Operation Guide

### 2.1 Flash Quad (X4) Mode Auto-Enablement
Flash memories from certain manufacturers (e.g., GigaDevice, Puya, Macronix) ship with Quad (X4) mode disabled by default. If X4 mode remains unasserted, the FPGA will fall back to single-line (X1) low-speed mode when loading the BIN image, severely dragging down system boot time.

**Automated Processing & Benefits:**
- After downloading the initialization bitstream, the utility **automatically reads the Flash ID and identifies the manufacturer**;
- For chips requiring configuration, **the Quad Enable bit (QE=1) is automatically written to the status registers** without manual intervention;
- The log line `QE Enable Done...` confirms successful enablement.

FPGA hardware architecture compatibility matrix:

| FPGA Architecture Series | Identifier Code | Supported Chip Models | Flash X4 Mode Enablement |
| :--- | :---: | :--- | :---: |
| **Spartan-7** | `S7` | XC7S25T, XC7S50T, XC7S75T, XC7S100T | 🟢 Supported |
| **Artix-7** | `A7` | XC7A12T, XC7A15T, XC7A25T, XC7A35T, XC7A50T, XC7A75T, XC7A100T, XC7A200T | 🟢 Supported |
| **Kintex-7** | `K7` | XC7K70T, XC7K160T, XC7K325T, XC7K355T, XC7K410T, XC7K420T, XC7K480T | 🟢 Supported |
| **Virtex-7** | `V7` | XC7VX330T, XC7VX415T, XC7VX485T, XC7VX550T, XC7VX585T, XC7VX690T, etc. | 🟢 Supported |
| **Kintex UltraScale** | `KU` | XCKU025, XCKU035, XCKU040, XCKU060, XCKU085, XCKU095, XCKU115 | 🟢 Supported |
| **Spartan UltraScale+** | `SUP` | XCSU10P, XCSU25P, XCSU35P | 🟢 Supported |
| **Artix UltraScale+** | `AUP` | XCAU7P, XCAU10P, XCAU15P, XCAU20P, XCAU25P | 🟢 Supported |
| **Kintex UltraScale+** | `KUP` | XCKU3P, XCKU5P, XCKU9P, XCKU11P, XCKU13P, XCKU15P, XCKU19P | 🟢 Supported |
| **Virtex UltraScale+** | `VUP` | XCVU3P, XCVU5P, XCVU7P, XCVU9P, XCVU11P, XCVU13P | 🟢 Supported |
| **Fudan Micro (FMK)** | `FMK` | Selected proprietary models | 🟠 Unsupported |

> 💡 **Note**: If your FPGA model is not listed in the matrix above, please contact the developer for custom adaptation.

### 2.2 Multi-Port Parallel Burn Isolation
- The utility supports 4 concurrent JTAG channels. The background subsystem automatically allocates dedicated service ports (`localhost:3121~3124`) for each channel, achieving true multi-process concurrency isolation;
- Concurrent channels operate independently without cross-interference or resource contention. Ensure valid Vivado path configurations and correct FPGA family selections for each port prior to programming.

### 2.3 ALL One-Click Concurrent Burn Guide
- Switch to the top **"ALL"** tab to monitor configuration, status, and burn progress across all 4 JTAG channels from a unified dashboard;
- Click **"One-Click Burn"** to simultaneously trigger programming on all enabled/checked ports;
- Use the **"Reference Port"** dropdown (e.g., JTAG1) in the upper-right corner to instantly copy the reference port's BIN path and device parameters to all other ports, greatly streamlining batch production setups.

---

## 3. Technical Support

If you encounter issues that cannot be resolved through the steps above, or require further technical assistance:

- Use the **"Copy"** or **"Export"** buttons on the log toolbar at the bottom of the interface to extract complete log output.
- Contact Technical Support WeChat: **`sz_tech_sz`**
- Check and download the latest software releases on the System tab.
