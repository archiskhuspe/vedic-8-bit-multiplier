# Vedic 8-Bit Multiplier

![Verilog](https://img.shields.io/badge/Verilog-HDL-blue)
![Toolchain](https://img.shields.io/badge/Toolchain-Xilinx%20ISim%20%2F%20Icarus-orange)
![License](https://img.shields.io/badge/license-MIT-green)

A hierarchical 8-bit digital multiplier implemented in Verilog HDL using the **Urdhva Tiryagbhyam (UT)** sutra from Vedic mathematics. The design decomposes 8×8 multiplication into four 4×4 sub-multipliers, each built from four 2×2 sub-multipliers and half adders — reducing gate delay and area compared to conventional ripple-carry multipliers.

## Table of Contents

- [How It Works](#how-it-works)
- [Module Hierarchy](#module-hierarchy)
- [Tech Stack](#tech-stack)
- [Prerequisites](#prerequisites)
- [Simulation](#simulation)
- [Project Structure](#project-structure)
- [Limitations](#limitations)
- [License](#license)

## How It Works

The **Urdhva Tiryagbhyam** (UT) sutra — meaning "vertically and crosswise" — multiplies two numbers by computing partial products simultaneously in a crosswise pattern and summing them. This parallelism reduces the critical path compared to a sequential multiplier.

The 8-bit inputs are split into two 4-bit halves. Four 4×4 sub-multipliers compute partial products:

```
P = (A_hi × B_hi) << 8 + (A_hi × B_lo + A_lo × B_hi) << 4 + (A_lo × B_lo)
```

Each 4×4 multiplier is itself built from four 2×2 UT multipliers. The 2×2 multiplier uses three AND gates and two half adders (XOR + AND).

**Verified simulation result:**  
`a = 8'b01011011` (91) × `b = 8'b00011110` (30) → `c = 0000101010101010` (2730) ✓

## Module Hierarchy

```
vedic_8X8                  ← top-level 8-bit multiplier
├── vedic_4_x_4 (×4)       ← 4-bit UT multiplier
│   ├── vedic_2_x_2 (×4)   ← 2-bit UT multiplier
│   │   └── ha (×2)        ← half adder
│   ├── add_4_bit
│   └── add_6_bit (×2)
├── add_8_bit
└── add_12_bit (×2)

test_vedic_8               ← testbench
```

## Tech Stack

- **Language:** Verilog HDL
- **Simulation:** Xilinx ISim (used in paper) or Icarus Verilog (open-source)
- **Synthesis:** Xilinx XST, targeting Spartan 6 FPGA

## Prerequisites

**Option A — Xilinx ISim (used in paper):**
- [Xilinx ISE 14.2](https://www.xilinx.com/support/download/index.html/content/xilinx/en/downloadNav/vivado-design-tools/archive-ise.html) or later

**Option B — Icarus Verilog (open-source):**
```bash
# macOS
brew install icarus-verilog

# Ubuntu/Debian
sudo apt install iverilog
```

## Simulation

**With Icarus Verilog:**
```bash
# Compile
iverilog -o vedic_sim vedic_8_bit_multiplier.v

# Run
vvp vedic_sim
```

Expected output (active test case `a=20, b=5`):
```
0 a= 00010100, b=00000101,  --- c= 0000000001100100
```
`0000000001100100` = 100 = 20 × 5 ✓

**With Xilinx ISim:**  
Open `vedic_8_bit_multiplier.v` in Xilinx ISE, set `test_vedic_8` as the top module, and run the ISim simulator.

## Project Structure

```
vedic-8-bit-multiplier/
├── vedic_8_bit_multiplier.v   # Verilog HDL source + testbench
├── project_report.pdf          # Paper: "8-Bit Vedic Multiplier"
├── .gitignore
├── LICENSE
└── README.md
```

## Limitations

- The testbench contains only one active test case (a=20, b=5); no exhaustive or randomised test suite.
- Synthesis timing, area, and power results are discussed in `project_report.pdf` but are not reproduced in this repo.
- The UT sutra offers the best area/delay trade-off at small bit-widths; for 16-bit and above, Wallace tree or Booth-encoded multipliers may be preferable.
- No simulation waveform files (`.vcd`) are committed.

## License

Released under the [MIT License](LICENSE).
