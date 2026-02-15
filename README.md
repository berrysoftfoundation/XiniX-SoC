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
text
Copy code
chipyard/
└── generators/
    └── chipyard/
        └── src/main/scala/
            ├── config/
            │   └── XiniX.scala
            └── xinix/
                ├── package.scala
                ├── pmu/
                ├── thermal/
                ├── aon/
                ├── pcie/
                ├── ethernet/
                ├── security/
                ├── display/
                ├── video/
                ├── camera/
                ├── wireless/
                ├── storage/
                ├── usb/
                ├── audio/
                ├── misc/
                ├── realtime/
                ├── moca/
                ├── gnss/
                └── lte/
📦 Verilog Blackboxes
Located in:

generators/chipyard/src/main/resources/vsrc/
text
Copy code
vpu_blackboxes.v
nvme_blackboxes.v
gnss_rf_blackboxes.v
jesd204b_phy_blackbox.v

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

📚 Obtaining External RTL Cores
cd chipyard/generators

git clone https://github.com/yhqiu16/NVMeCHA.git
git clone https://github.com/openasic-org/xk264.git
git clone https://github.com/openasic-org/xk265.git

---

🛠️ Building the Simulator
Add sources to Makefrag:
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
If you find a bug or want to propose a feature:

https://github.com/berrysoftfoundation/XiniX-SoC/issues

---

🤝 Contributing
See: CONTRIBUTING.md
