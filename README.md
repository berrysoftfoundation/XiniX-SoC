![CHIPYARD](https://github.com/ucb-bar/chipyard/raw/main/docs/_static/images/chipyard-logo-full.png)

# XiniX SoC – A Complete, Open‑Source, Heterogeneous RISC‑V System‑on‑Chip

## What is XiniX SoC

XiniX is a production‑ready, fully synthesizable RISC‑V SoC designed within the Chipyard framework. It integrates a powerful heterogeneous CPU complex, dedicated AI/ML accelerators, high‑speed I/O, multimedia engines, advanced security features, and comprehensive power management – all in a single, coherent design.

The project follows a layered PHY‑Core‑Frontend architecture, includes conditional instantiation for external dependencies, built‑in self‑test (BIST) for every module, and a presence register at offset 0xFFC for seamless software discovery. All modules are licensed under the BSD‑3‑Clause license, making XiniX a truly open foundation for next‑generation computing.

🏛️ Architecture Overview
XiniX is built on the PHY‑Core‑Frontend pattern, which separates physical interfaces from protocol logic and data movement. This design ensures maintainability, testability, and portability across different technology nodes.

https://docs/images/block_diagram.png (Placeholder; actual diagram can be added later)

Key Components
Category	Modules
CPU Complex	4× BOOM (out‑of‑order, 2‑way SMT)
4× Shuttle (dual‑issue in‑order)
Saturn Vector (RVV 1.0, 512‑bit VLEN)
SentryCore (triple‑core lockstep real‑time)
AI / ML	Gemmini NPU (systolic array, 16×16, 256 KB scratchpad)
MoCA dynamic accelerator scheduler
High‑Speed I/O	PCIe Gen5 digital controller
100G Ethernet MAC/PCS
USB 3.0 XHCI, USB 2.0 OTG, USB‑C/PD 3.0
Storage	eMMC 5.1, SDIO/UHS‑II, QSPI NOR, UFS 3.1, SATA 3.0 AHCI, NVMe (NVMeCHA), SDCard
Display & Video	DPU (Display Processing Unit), HDMI 2.0, MIPI DSI, DisplayPort 1.4, VPU (H.264/H.265)
Camera	MIPI CSI‑2, ISP (Image Signal Processor)
Wireless	Wi‑Fi 6E (SDIO), Bluetooth 5.4 (UART)
Audio	Audio DSP, I2S, Headphone DAC
Security	HSM (TRNG, AES‑256, SHA‑3, RSA‑4096, ECC, PUF), HDFI (data‑flow isolation), QARMA‑64 memory encryption, OTP, Secure Boot
Power Management	FIVR (10‑phase), DVFS (9 domains), power gates, thermal sensors, always‑on wakeup controller
Infrastructure	Constellation NoC (mesh/torus), L3 cache (16 MB inclusive), DMA engine
GNSS	Multi‑constellation baseband (GPS, Galileo, BeiDou, GLONASS, QZSS, NavIC)
LTE Modem	JESD204B interface, FFT engine, turbo decoder, PDSCH processor
All modules are conditionally instantiated based on the availability of external sources (RTL, hard IP). If a source is missing, the module can be disabled or replaced by a simple fallback model, making XiniX suitable for both ASIC and FPGA flows.

📁 Repository Structure
The repository is organised as a Chipyard generator. Place it under generators/chipyard/src/main/scala/ in your Chipyard workspace.

text
chipyard/
└── generators/
    └── chipyard/
        └── src/main/scala/
            ├── config/
            │   └── XiniX.scala                  # Main SoC configuration
            └── xinix/                            # All XiniX modules
                ├── package.scala                  # Common utilities
                ├── pmu/                            # Power Management Unit
                ├── thermal/                         # Thermal sensors
                ├── aon/                             # Always‑on domain
                ├── pcie/                            # PCIe Gen5
                ├── ethernet/                        # 100G Ethernet
                ├── security/                        # Hardware security
                ├── display/                         # Display engines
                ├── video/                           # Video codec
                ├── camera/                          # Camera interfaces
                ├── wireless/                        # Wi‑Fi / Bluetooth
                ├── storage/                         # Storage controllers
                ├── usb/                             # USB subsystem
                ├── audio/                           # Audio DSP
                ├── misc/                            # Peripherals (RTC, PWM, etc.)
                ├── realtime/                         # SentryCore
                ├── moca/                             # MoCA scheduler
                ├── gnss/                             # GNSS receiver
                └── lte/                              # LTE modem
Verilog blackboxes (for external RTL cores) are located in:

text
generators/chipyard/src/main/resources/vsrc/
    ├── vpu_blackboxes.v
    ├── nvme_blackboxes.v
    ├── gnss_rf_blackboxes.v
    └── jesd204b_phy_blackbox.v
🚀 Getting Started
Prerequisites
Chipyard (version ≥ 1.13.0) – follow the official Chipyard setup guide.

RISC‑V toolchain (esp‑tools for Gemmini, or the standard GNU toolchain).

Mixed‑language simulator (VCS, Questa, or Xcelium) if you plan to use the VHDL NVMeCHA core. For pure Verilog simulation, Verilator works.

Cloning the Repository
bash
cd chipyard/generators/chipyard/src/main/scala/
git clone https://github.com/yourname/xinix.git xinix
# or manually copy the source tree
Obtaining External RTL Cores
Some modules depend on external open‑source RTL:

NVMe – NVMeCHA (VHDL/Verilog)

VPU – xk264 and xk265 (Verilog)

Clone them into chipyard/generators/:

bash
cd chipyard/generators
git clone https://github.com/yhqiu16/NVMeCHA.git
git clone https://github.com/openasic-org/xk264.git
git clone https://github.com/openasic-org/xk265.git
Building the Simulator
Add the Verilog blackbox sources to your simulation Makefrag (e.g., in sims/verilator/Makefrag):

makefile
VSRCS += $(base_dir)/generators/NVMeCHA/hw/NVMe_Controller/NVMe_Controller.srcs/sources_1/NVMe/*.vhd
VSRCS += $(base_dir)/generators/NVMeCHA/hw/NVMe_Controller/NVMe_Controller.srcs/sources_1/NVMe/*.v
VSRCS += $(base_dir)/generators/xk264/rtl/*.v
VSRCS += $(base_dir)/generators/xk265/rtl/*.v
VSRCS += $(base_dir)/generators/chipyard/src/main/resources/vsrc/vpu_blackboxes.v
VSRCS += $(base_dir)/generators/chipyard/src/main/resources/vsrc/nvme_blackboxes.v
VSRCS += $(base_dir)/generators/chipyard/src/main/resources/vsrc/gnss_rf_blackboxes.v
VSRCS += $(base_dir)/generators/chipyard/src/main/resources/vsrc/jesd204b_phy_blackbox.v
Then build and run:

bash
cd sims/verilator   # or sims/vcs for mixed‑language
make CONFIG=XiniX -j8
./simulator-chipyard-XiniX ../tests/hello.riscv
🧪 Design Philosophy
Conditional Instantiation: Each module checks a parameter externalSourceAvailable; if false, the module is either omitted or replaced by a simple fallback model.

Presence Register: Every module exposes a read‑only register at offset 0xFFC so software can discover which accelerators are actually present.

Built‑in Self‑Test (BIST): Most modules include a BIST engine that can be triggered via a control register, verifying functionality at runtime.

Layered Architecture: PHY, Core, and Frontend layers are cleanly separated, enabling independent development and verification.

Standardised Interfaces: All modules use TileLink for control and AXI4‑Stream / AXI4 for high‑speed data, ensuring plug‑and‑play compatibility with Chipyard.

📄 License
All original XiniX code is provided under the BSD‑3‑Clause license. See the LICENSE file for details.

External components (NVMeCHA, xk264, xk265) retain their own licenses (Apache‑2.0, MIT, etc.). Please refer to their respective repositories.

🙏 Acknowledgements
XiniX builds upon the excellent work of many open‑source projects:

Chipyard – the SoC generation framework from UC Berkeley.

BOOM, Shuttle, Saturn, Gemmini – the core RISC‑V processors and accelerators.

SentryCore – the triple‑lockstep real‑time core.

MoCA – the dynamic accelerator scheduler.

NVMeCHA – the high‑performance NVMe controller.

xk264 / xk265 – the H.264/H.265 video encoders.

We thank the developers of these projects for their contributions to the open‑source hardware community.

📞 Contact & Contributions
For questions, bug reports, or contributions, please open an issue on the GitHub repository. Pull requests are welcome!

## Need help?

* If you find a bug or would like propose a feature, post an issue on this repo: https://github.com/berrysoftfoundation/XiniX-SoC/issues

## Contributing

* See [CONTRIBUTING.md](/CONTRIBUTING.md)
