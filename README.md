![CHIPYARD](https://github.com/ucb-bar/chipyard/raw/main/docs/_static/images/chipyard-logo-full.png)

# XiniX SoC – A Complete, Open-Source, Heterogeneous RISC-V System-on-Chip

## What is XiniX SoC

XiniX is a production-ready, fully synthesizable RISC-V SoC designed within the Chipyard framework. It integrates a powerful heterogeneous CPU complex, dedicated AI/ML accelerators, high-speed I/O, multimedia engines, advanced security features, and comprehensive power management – all in a single, coherent design.

The project follows a layered PHY-Core-Frontend architecture, includes conditional instantiation for external dependencies, built-in self-test (BIST) for every module, and a presence register at offset `0xFFC` for seamless software discovery.

All modules are licensed under the BSD-3-Clause license, making XiniX a truly open foundation for next-generation computing.

---

## 🏛️ Architecture Overview

XiniX is built on the PHY-Core-Frontend pattern, which separates physical interfaces from protocol logic and data movement. This design ensures maintainability, testability, and portability across different technology nodes.

> Block Diagram (Placeholder)  
> `docs/images/block_diagram.png`

---

## 🔑 Key Components

| Category | Modules |
|----------|----------|
| **CPU Complex** | 4× BOOM (OoO, 2-way SMT)<br>4× Shuttle (Dual-issue)<br>Saturn Vector (RVV 1.0, 512-bit)<br>SentryCore (Lockstep) |
| **AI / ML** | Gemmini NPU (16×16, 256 KB)<br>MoCA Scheduler |
| **High-Speed I/O** | PCIe Gen5<br>100G Ethernet<br>USB 3.0 / 2.0 / Type-C |
| **Storage** | eMMC 5.1, SDIO, QSPI, UFS, SATA, NVMe |
| **Display & Video** | DPU, HDMI 2.0, MIPI DSI, DP 1.4, VPU |
| **Camera** | MIPI CSI-2, ISP |
| **Wireless** | Wi-Fi 6E, Bluetooth 5.4 |
| **Audio** | Audio DSP, I2S, DAC |
| **Security** | HSM, AES-256, SHA-3, RSA, ECC, PUF, Secure Boot |
| **Power** | FIVR, DVFS, Power Gates, Thermal Sensors |
| **Infrastructure** | NoC, L3 Cache (16 MB), DMA |
| **GNSS** | GPS, Galileo, BeiDou, GLONASS, NavIC |
| **LTE** | JESD204B, FFT, Turbo Decoder |

All modules are conditionally instantiated based on the availability of external RTL or hard IP.

---

## 📁 Repository Structure

Place this repository under:

```text
generators/chipyard/src/main/scala/

Directory Layout
XiniX-SoC/                                   # Root of your XiniX-SoC repository
└── generators/
    └── chipyard/
        └── src/main/scala/
            ├── config/
            │   └── XiniX.scala              # Main SoC configuration (ties all modules together)
            └── xinix/                        # Custom XiniX modules
                ├── package.scala              # Common utilities (VoltageOps, FrequencyOps, TimeOps)
                │
                ├── pmu/                       # Power Management Unit
                │   ├── PMUBlock.scala         # Top‑level PMU with FIVR, DVFS, power gates
                │   ├── FIVR.scala             # Fully Integrated Voltage Regulator (10‑phase)
                │   ├── DVFSController.scala   # DVFS controller for 9 domains
                │   └── PowerGate.scala        # Power gate with retention and wakeup
                │
                ├── thermal/                    # Thermal sensors and throttling
                │   ├── ThermalSensor.scala     # Distributed thermal sensor (12‑bit, threshold)
                │   └── ThermalThrottle.scala   # Thermal throttling controller
                │
                ├── aon/                        # Always‑on domain
                │   └── WakeupController.scala   # Wakeup logic, sleep modes, RTC glue
                │
                ├── pcie/                       # PCIe Gen5 Controller (LitePCIe‑inspired)
                │   └── PCIeGen5.scala           # PHY‑Core‑Frontend layers, QDMA, MSI‑X, crossbar
                │
                ├── ethernet/                   # 100G Ethernet Controller (LiteEth‑inspired)
                │   └── Ethernet100G.scala       # PHY‑MAC‑Frontend layers, Etherbone, SFP support
                │
                ├── security/                    # Hardware Security Modules
                │   ├── HSM.scala                # TRNG, AES‑256, SHA‑3, RSA‑4096, ECC, PUF, KeyStore
                │   ├── MemoryEncryption.scala   # QARMA‑64 memory encryption (RoCC accelerator)
                │   ├── HDFI.scala               # Hardware Data‑Flow Isolation (tagging, Biba, Bell‑LaPadula)
                │   ├── TRNG.scala               # True Random Number Generator (ring oscillator)
                │   ├── OTP.scala                # One‑Time Programmable memory
                │   └── SecureBoot.scala         # Secure boot controller (RSA signature verification)
                │
                ├── display/                     # Display Processing and Interfaces
                │   ├── DPU.scala                # Display Processing Unit (layers, composition, CSC)
                │   ├── HDMI.scala               # HDMI 2.0 transmitter (4K@60Hz, TMDS)
                │   ├── MIPIDSI.scala            # MIPI DSI transmitter (4‑lane, 1080P@60Hz)
                │   └── DisplayPortSubsystem.scala # Complete DisplayPort 1.4 transmitter
                │
                ├── video/                       # Video Codec Unit (VPU)
                │   └── VPU.scala                # TileLink wrapper for openasic‑org xk264/xk265 cores
                │
                ├── camera/                      # Camera Interfaces and ISP
                │   ├── MIPICSI.scala            # MIPI CSI‑2 receiver (4‑lane, 4K@30fps)
                │   └── ISP.scala                # Image Signal Processor (dual‑channel, HDR)
                │
                ├── wireless/                    # Wireless Connectivity
                │   ├── WiFi.scala               # Wi‑Fi 6E controller (SDIO interface)
                │   └── Bluetooth.scala          # Bluetooth 5.4 controller (UART transport)
                │
                ├── storage/                     # Storage Controllers
                │   ├── eMMC.scala               # eMMC 5.1 controller (HS400, DMA)
                │   ├── SDIO.scala               # SDIO/UHS‑II controller
                │   ├── QSPI.scala               # QSPI NOR flash controller
                │   ├── UFS.scala                # UFS 3.1 controller (UniPro, M‑PHY)
                │   ├── SATA.scala               # SATA 3.0 AHCI controller (port multiplier, NCQ, RAID)
                │   ├── NVMe.scala               # NVMe controller (NVMeCHA core, 7 GB/s, BIST)
                │   └── SDCard.scala             # SDCard host controller (SD/SDHC/SDXC, 4‑bit, DMA)
                │
                ├── usb/                         # USB Controllers
                │   ├── USB30.scala              # USB 3.0 XHCI controller (SuperSpeed, isochronous)
                │   ├── USB20.scala              # USB 2.0 OTG controller
                │   ├── USBTypeC.scala           # USB Type‑C and Power Delivery 3.0 controller
                │   └── USBSubsystem.scala       # Top‑level USB subsystem (aggregates the above)
                │
                ├── audio/                       # Audio Subsystem
                │   ├── AudioDSP.scala           # Audio DSP core (I2S, headphone DAC)
                │   ├── I2S.scala                # I2S transmitter/receiver
                │   └── HeadphoneDAC.scala       # Headphone DAC with 3.5mm jack
                │
                ├── misc/                        # Miscellaneous Peripherals
                │   ├── RTC.scala                # Real‑Time Clock with battery backup
                │   ├── EEPROM.scala             # I2C EEPROM interface (emulated)
                │   ├── PWM.scala                # PWM controller (8 channels)
                │   ├── UART.scala               # Custom UART controller
                │   ├── I2C.scala                # Custom I2C controller
                │   ├── SPI.scala                # Custom SPI controller
                │   └── GPIO.scala               # Custom GPIO controller (64 pins)
                │
                ├── realtime/                    # Real‑Time Core (SentryCore)
                │   └── RealTimeCore.scala       # Triple‑core lockstep RISC‑V, ECC TCM, CLIC, DMA
                │
                ├── moca/                        # MoCA dynamic accelerator management
                │   ├── MOCAConfig.scala         # Configuration parameters for MoCA
                │   ├── MOCARuntime.scala        # Runtime interface (contention detection, backpressure)
                │   └── MOCAScheduler.scala      # Scheduler for dynamic workload distribution
                │
                ├── gnss/                        # GNSS Subsystem
                │   ├── GNSSParams.scala         # GNSS signal parameters (GPS, Galileo, BeiDou, etc.)
                │   ├── GNSSRFInterface.scala    # RF front‑end (PHY layer)
                │   ├── GNSSChannel.scala        # Single GNSS channel (code generator, NCO, correlator)
                │   ├── GNSSBaseband.scala       # Multi‑channel baseband processor (core layer)
                │   ├── GNSSNavigator.scala      # Navigation processor (frontend layer, PVT)
                │   └── GNSS.scala               # Top‑level GNSS controller
                │
                └── lte/                         # LTE Modem
                    ├── LTEParams.scala          # LTE system parameters (bandwidth, FFT size, etc.)
                    ├── JESD204BInterface.scala  # JESD204B PHY interface
                    ├── FFTEngine.scala          # Runtime‑reconfigurable FFT engine
                    ├── TurboDecoder.scala       # LTE turbo decoder accelerator
                    ├── PDSCHEngine.scala        # PDSCH receive chain processor
                    └── LTEModem.scala           # Top‑level LTE modem controller
```
---

```text
📦 Verilog Blackboxes Files (placed in generators/chipyard/src/main/resources/vsrc/)
**vpu_blackboxes.v**          # Blackbox for xk264 (H.264) and xk265 (H.265)
**nvme_blackboxes.v**         # Blackbox for NVMeCHA (NVMe controller)
**gnss_rf_blackboxes.v**      # Simulation model for GNSS RF front‑end
**jesd204b_phy_blackbox.v**   # Simulation model for JESD204B PHY

---

🚀 Getting Started
Prerequisites
Chipyard ≥ 1.13.0

RISC-V Toolchain

VCS / Questa / Xcelium (optional)

Verilator (for Verilog simulation)

---

📥 Cloning the Repository
cd chipyard/generators/chipyard/src/main/scala/
git clone https://github.com/yourname/xinix.git xinix

---

📚 External RTL Repositories (to be cloned into generators/)
**NVMeCHA** – https://github.com/yhqiu16/NVMeCHA (VHDL/Verilog)
**xk264** – https://github.com/openasic-org/xk264 (Verilog)
**xk265** – https://github.com/openasic-org/xk265 (Verilog)

---

🛠️ Build System Integration (example for sims/verilator/Makefrag)
VSRCS += $(base_dir)/generators/NVMeCHA/hw/NVMe_Controller/NVMe_Controller.srcs/sources_1/NVMe/*.vhd
VSRCS += $(base_dir)/generators/NVMeCHA/hw/NVMe_Controller/NVMe_Controller.srcs/sources_1/NVMe/*.v
VSRCS += $(base_dir)/generators/xk264/rtl/*.v
VSRCS += $(base_dir)/generators/xk265/rtl/*.v
VSRCS += $(base_dir)/generators/chipyard/src/main/resources/vsrc/vpu_blackboxes.v
VSRCS += $(base_dir)/generators/chipyard/src/main/resources/vsrc/nvme_blackboxes.v
VSRCS += $(base_dir)/generators/chipyard/src/main/resources/vsrc/gnss_rf_blackboxes.v
VSRCS += $(base_dir)/generators/chipyard/src/main/resources/vsrc/jesd204b_phy_blackbox.v

---

Build and run:
cd sims/verilator
make CONFIG=XiniX -j8
./simulator-chipyard-XiniX ../tests/hello.riscv

---

🧪 Design Philosophy
Conditional Instantiation
Modules check externalSourceAvailable and fallback if unavailable.

Presence Register
Each module exposes a register at 0xFFC.

Built-in Self-Test
Runtime verification logic.

Layered Architecture
PHY → Core → Frontend separation.

Standard Interfaces
TileLink, AXI4, AXI4-Stream.

---

##All files are complete, synthesizable, and ready for simulation. Each module includes:

Conditional instantiation based on externalSourceAvailable
Presence register at 0xFFC
Built‑in self‑test (BIST) where appropriate
Standardised interfaces (TileLink control, AXI4‑Stream data, AXI4 DMA)

---

📄 License
All original code is released under the BSD-3-Clause License.

See LICENSE for details.

External components retain their original licenses.

---

🙏 Acknowledgements
Chipyard

BOOM, Shuttle, Saturn, Gemmini

SentryCore

MoCA

NVMeCHA

xk264 / xk265

Thanks to all contributors to open-source hardware.

---

📞 Contact & Contributions
Open issues for bugs or questions

Pull requests are welcome

---

❓ Need Help?
* If you find a bug or would like propose a feature, post an issue on this repo: https://github.com/berrysoftfoundation/XiniX-SoC/issues

---

🤝 Contributing
* See [CONTRIBUTING.md](/CONTRIBUTING.md)
