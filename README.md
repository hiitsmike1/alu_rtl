# 4-bit ALU from Discrete Transistors (RTL)
 
A 4-bit arithmetic logic unit built entirely from discrete NPN transistors and resistors using resistor-transistor logic (RTL), wired on breadboards. ICs were only used to implement the tri-state buffers.
 
**Website:** https://hiitsmike1.github.io/michaelcrandall.github.io/
 
![The completed ALU on breadboards](images/alu_photo.png)
 
## Overview
 
The ALU performs seven operations on two 4-bit inputs and places the selected result onto a shared 4-bit output bus. I built it to understand what sits underneath a logic gate: voltage levels, transistor sizing, signal regeneration, and how an output stage can share a bus safely.
 
- **160+ NPN transistors and about 270 resistors** *(confirm the final counts, and add the PNP count if you used totem-pole output stages)*
- **7 operations:** ADD, SUB, AND, OR, NOT, shift left (SHL), shift right (SHR)
- **Tri-state output stage** so all seven operations can drive one 4-bit bus without contention
- **5 V logic levels**, breadboard construction, 2N3904 (NPN) transistors
- **Flags:** Zero (4-input NOR across the output bus) and Carry-out from the most significant adder
- **External carry-in** for future chaining of ALUs
## Operations
 
| Select | Operation | Description |
|--------|-----------|-------------|
| [000]  | OR  | bitwise OR  |
| [001]  | AND | bitwise AND|
| [010]  | NOT | bitwise NOT of `A` |
| [011]  | SHL  | shift `A` left |
| [100]  | SHR | shift `A` right |
| [101]  | ADD | `A + B` |
| [110]  | SUB | `A - B` (two's complement: `A + ~B + 1`)  |
 
## Design
 
### Gate library
 
Everything is built up from a small set of gates, with transistor counts kept as low as I could get them:
 
| Gate | Transistors |
|------|-------------|
| NOT | 1 |
| NAND | 2 |
| NOR | 2 |
| XOR (four NANDs) | 8 |
| Full adder | 23 |
 
Key choices:
- **NAND-based XOR** instead of an AND/OR construction, to avoid  voltage drops through several gates.
- **AND and OR built as NAND/NOR followed by a NOT**, which restores a clean logic level at each stage (signal regeneration).
- **A separate 10 kΩ base resistor for every transistor.** Sharing a resistor let the base voltage sag when several transistors were driven from one node.
### Arithmetic
 
Four full adders form a ripple-carry adder. Subtraction reuses the same adders by inverting `B` and setting the carry-in to 1 (`A + ~B + 1`). The `B` inversion uses one NAND-based XOR per bit, and the carry-in control uses a small 2-transistor OR gate.
 
### Output stage and bus sharing
 
Each operation produces its own 4-bit result, and exactly one is allowed onto the output bus at a time:
 
- A **3-to-8 active-low decoder** (27 NPN transistors) turns the 3-bit select into one-cold enable lines.
- Each operation's result passes through a **tri-state buffer** which gives a true high-impedance output when disabled.

### Flags
 
- **Zero:** a 4-input NOR on the output bus.
- **Carry-out:** taken from the most significant full adder.
## Testing

 
## Photos and Schematics
 

 
![Block diagram](docs/block_diagram.png)
 
## What I Learned
 

 
## Repository Structure
 
```
alu_rtl/
├── images/              # photos
└── README.md
```
 
## Related Projects
 
- [8-bit CPU in Verilog](https://github.com/hiitsmike1/8bit-cpu)
- [ALU in Verilog with Verilator](https://github.com/hiitsmike1/alu_verilator)
## Author
 
Michael Crandall, B.S. Electrical Engineering, University of Maryland (expected December 2028)
[LinkedIn](https://linkedin.com/in/crandall0327) · [Portfolio](https://hiitsmike1.github.io/michaelcrandall.github.io/)