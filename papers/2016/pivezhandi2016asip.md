# ASIP design for two dimensional cordic based DFT and DCT algorithms

**Citation Key**: `pivezhandi2016asip`

**Year**: 2016

**Type**: inproceedings

**Authors**: Pivezhandi, Mohammad and Eshghi, Mohammad

**Venue**: 2016 6th International Conference on Computer and Knowledge Engineering (ICCKE)

---

## Key Points

- CORDIC-based Application-Specific Instruction-set Processor (ASIP) for 2D DFT and DCT algorithms
- 84% reduction in control unit gates for DFT, 86% for DCT compared to baseline Mano design
- Clock cycle reduction to 3.6% (DFT) and 3.86% (DCT) of Mano design execution time
- Three-stage optimization: improved registers/data bus, added RAM/cache, pipeline architecture
- DFT: 94,339 clocks (vs 2,549,760 in Mano), DCT: 159,744 clocks (vs 4,000,000 in Mano)
- Fixed-point arithmetic with 32-bit registers, three parallel functional units for CORDIC

## Abstract

This paper proposes a CORDIC-based Application-Specific Instruction-set Processor (ASIP) for Two dimensional Discrete Fourier Transform (DFT) and Discrete Cosine Transform (DCT). DFT performs an important role in digital signal processing, and DCT algorithm is suitable for image compression applications.

Using CORDIC method inside these two signal processing algorithms reduces the number of memory accesses. ASIP can act as an alternative to ASIC and GPP design if it satisfies the critical points of power consumption, total delay, manufacturing cost and productivity.

Two stages of optimization are performed on the basic Mano model. Temporary registers and data bus are improved, RAM and cache memory are added, and the number of Functional units is increased. As a result, the number of gates in the control unit is decreased about 84% in the DFT and 86% in the DCT in comparison to the Mano design.

Moreover, the DFT and the DCT instructions in this design consume 3.6% and 3.86% as the number of clocks as the Mano design does. Optimization in the proposed pipeline architecture is evaluated with Register Transfer Language (RTL) codes.

## Contributions

1. **CORDIC-based ASIP architecture**: Novel application-specific instruction-set processor design using CORDIC algorithm to reduce memory accesses in 2D DFT and DCT implementations for 8×8 blocks
2. **Three-stage optimization methodology**: Systematic progression from baseline Mano model (ASIP 0) through improved registers/ALU (ASIP 1) to final pipelined architecture with cache/RAM (ASIP 2)
3. **Significant hardware reduction**: 84% decrease in control unit gates for DFT and 86% for DCT compared to Mano design, while reducing RTL code lines from 1,425 to 261 (DFT) and 342 to 100 (DCT)
4. **Dramatic clock cycle reduction**: DFT execution reduced to 94,339 clocks (from 2,549,760 in Mano, 27× speedup), DCT reduced to 159,744 clocks (from 4,000,000 in Mano, 25× speedup)
5. **Parallel CORDIC implementation**: Three functional units (A, B, C) enabling single-clock-cycle completion of each CORDIC iteration through parallel execution of X, Y, Z computations
6. **Register Transfer Language implementation**: Complete manual RTL design providing higher degree of freedom compared to automatic LISA-based ASIP generation

## Methodology

**ASIP Design Philosophy**:
- Alternative to ASIC and GPP satisfying four critical parameters: power, delay, cost, productivity
- Advantages over GPP: Lower power consumption, higher speed, more specific instructions
- Advantages over ASIC: Lower manufacturing cost, shorter time-to-market, reprogrammable
- Manual RTL design approach (vs automatic LISA toolkit) for maximum design freedom

**Implemented Algorithms**:

**1. Two-Dimensional DFT (Discrete Fourier Transform)**:
- Equation: X(k₁, k₂) = Σ(n₁=0 to N₁) Σ(n₂=0 to N₂) x(n₁,n₂) × [cos(2π(n₁k₁/N₁ + n₂k₂/N₂)) + j·sin(2π(n₁k₁/N₁ + n₂k₂/N₂))]
- Complexity: O(N²) with basic implementation, O(N log N) with FFT
- Block size: 8×8 DFT window
- Applications: Digital signal processing, orthogonal frequency division modulation, image watermarking, fingerprint recognition
- Implementation: Separate 1D DFT on rows and columns

**2. Two-Dimensional DCT (Discrete Cosine Transform)**:
- Equation: DCT(l,k) = C(l)C(k)/4 × Σ(m=0 to 7) Σ(n=0 to 7) x(n,m) × cos((2n+1)kπ/16) × cos((2m+1)lπ/16)
- Block size: 8×8 DCT window
- Applications: JPEG image compression, MPEG video compression
- Advantage over DFT: High frequency values concentrated in one corner (better for compression)
- Implementation: Separate 1D DCT on rows and columns

**CORDIC Algorithm** (COordinate Rotational Digital Computer, Volder 1959):
- Purpose: Compute trigonometric functions (sin, cos) using only addition, subtraction, shift operations
- Equations:
  - X(i+1) = X(i) - Y(i) × d(i) × 2^(-i)
  - Y(i+1) = Y(i) + X(i) × d(i) × 2^(-i)
  - Z(i+1) = Z(i) - d(i) × arctan(2^(-i))
  - Direction: d(i) = -1 if Z(i) < 0, +1 if Z(i) ≥ 0
- Advantage: Eliminates multiplication operations, reduces memory accesses (stores angles instead of twiddle factors)
- Parallel structure: Three simultaneous operations (X, Y, Z) per iteration
- Quarter decoding: Handles all four quadrants (0-90°, 90-180°, 180-270°, 270-360°)

**Architecture Evolution**:

**ASIP 0 (Baseline - Mano Model)**:
- Basic Mano architecture with limited functional units
- Single ALU, minimal temporary registers
- DFT: 2,549,760 clock cycles, 1,425 RTL lines, 601 control unit decoder gates
- DCT: 4,000,000 clock cycles
- Result: Not hardware efficient, low productivity

**ASIP 1 (Improved Registers/ALU)**:
- Increased temporary registers
- Upgraded ALU unit
- DFT: 302,400 clock cycles (11.9% of ASIP 0), 475 RTL lines
- DCT: 614,400 clock cycles (15.4% of ASIP 0), 192 RTL lines
- Significant clock cycle reduction but still inefficient

**ASIP 2 (Final - Pipelined with Cache/RAM)**:
- **Three functional units**: A, B, C for parallel CORDIC execution
  - A and B: Arithmetic, Logic, Shift Unit (ALSU) with accumulator registers
  - C: Extra unit for third parallel CORDIC section
- **Memory hierarchy**:
  - RAM: 64×16-bit (read), 64×32-bit (write)
  - ROM: Two 16×16-bit blocks for CORDIC angle storage
  - Cache memory: Embedded to reduce main memory accesses
- **Register file**: 32-bit registers split (16-bit real, 16-bit imaginary for twiddle factors)
- **Address generator unit**: For RAM, ROM, cache addressing
- **Sequence counters**: 6 counters for loop operations (4 for internal loops)
- **Pipeline data bus**: Improved data transfer
- **Fixed-point arithmetic**: For high speed, low power
- **Results**:
  - DFT: 94,339 clocks (3.6% of ASIP 0), 261 RTL lines, 137 decoder gates
  - DCT: 159,744 clocks (3.86% of ASIP 0), 100 RTL lines, 38 decoder gates

**Instruction Set**:

**General Instructions (1 clock cycle each)**:
- Arithmetic: ADD, SUB, INC, DEC, MOD, MUL, MAC (Multiply-Accumulate)
- Logic: AND, OR, XOR, NOT
- Shift: Barrel Shift Right/Left, Shift Right/Left Logical

**Specific Instructions**:
- DFT02 (OPCODE: 010): Two Dimensional DFT
- DCT02 (OPCODE: 011): Two Dimensional DCT

**Register Set** (Table IV):
- AR (32-bit): Main flash memory address register
- AR1, AR2, AR3 (6/6/4-bit): RAM/ROM address registers
- R0-R3, D0-D8 (32-bit): Data registers for A/B functional units
- PC, AC, IR, INPR, OUTR: Standard control/data registers
- IC, LC, DC, RC, JC (4-bit): Control unit counters
- SC (5-bit): Sequence counter

## Results

**Clock Cycle Comparison** (Table V):

| Instruction | Mano (ASIP 0) | ASIP 1 | ASIP 2 |
|-------------|---------------|--------|--------|
| DFT | 2,549,760 | 302,400 | 94,339 |
| DCT | 4,000,000 | 614,400 | 159,744 |

**Speedup**:
- DFT: 27× faster than Mano (3.6% of original clocks)
- DCT: 25× faster than Mano (3.86% of original clocks)

**Hardware Metrics for DFT** (Figure 4):

| Design Stage | RTL Lines | Total Gates | Control Unit Decoder Gates |
|--------------|-----------|-------------|---------------------------|
| ASIP 0 | 1,425 | ~4,275 | 601 |
| ASIP 1 | 475 | ~1,425 | 137 |
| ASIP 2 | 261 | ~783 | 100 |

- Control unit gates reduced by 84% (from 601 to 100)
- RTL lines reduced by 82% (from 1,425 to 261)

**Hardware Metrics for DCT** (Figure 5):

| Design Stage | RTL Lines | Total Gates | Control Unit Decoder Gates |
|--------------|-----------|-------------|---------------------------|
| ASIP 1 | 192 | ~576 | 48 |
| ASIP 2 | 100 | ~300 | 38 |

- Control unit gates reduced by 86% (compared to ASIP 0)
- RTL lines reduced by 71% (from 342 to 100, ASIP 0 not shown in figure)

**Design Trade-offs**:
- Increasing model complexity reduces RTL code lines and clock cycles
- More sequence counters decrease decoder gates in control unit
- Critical point: Total gate count increase must not exceed decoder gate reduction
- ASIP 2 achieves optimal balance between hardware cost and performance

**Comparison with Literature**:
- Muhammad et al. (2008) with LISA: 1,008 clock cycles for 8×8 DCT block
- This work: 159,744 clocks for full 2D DCT but with manual RTL flexibility
- CORDIC approach reduces memory accesses compared to LUT-based methods

**Key Achievement**:
- 3 gates per RTL code line (average)
- Productivity significantly improved through reduced instruction complexity
- Real-time capability for 8×8 block processing

## Notes

**Research Context**:
- **Second publication** by Mohammad Pivezhandi (2016)
- **Affiliation**: Electrical and Computer Engineering, Shahid Beheshti University, Tehran, Iran
- **Co-author**: Mohammad Eshghi (Faculty advisor)
- **Conference**: ICCKE 2016 (6th International Conference on Computer and Knowledge Engineering), October 20-21 2016, Ferdowsi University of Mashhad

**Application Domains**:
- Digital signal processing: DFT for OFDM (Orthogonal Frequency Division Multiplexing)
- Image processing: 2D DFT for watermarking, fingerprint recognition
- Image/video compression: JPEG (8×8 DCT blocks), MPEG video formats
- Real-time embedded systems requiring low power and area efficiency

**Technical Innovation**:
- **CORDIC advantages**: Eliminates multiplications, reduces memory (stores angles not twiddle factors), lower power
- **Memory reduction**: CORDIC stores only arctan(2^(-i)) angles vs full twiddle factor tables
- **Parallel execution**: Three functional units complete one CORDIC iteration per clock cycle
- **Fixed-point design**: Suitable for high-speed, low-power implementations (16-bit real + 16-bit imaginary)

**Design Methodology**:
- Manual RTL (Register Transfer Language) design vs automatic LISA toolkit
- LISA: Faster time-to-market but limited flexibility
- RTL: Longer development but satisfies all ASIP design parameters (power, delay, cost, productivity)
- Three-stage iterative optimization approach

**ASIP vs ASIC vs GPP**:
- **ASIP advantages over GPP**: Lower power, higher speed, application-specific instructions
- **ASIP advantages over ASIC**: Lower cost, reprogrammable, faster time-to-market
- **ASIP challenges**: Must satisfy power, delay, cost, AND productivity simultaneously

**CORDIC Historical Context**:
- Volder (1959): Original CORDIC algorithm for coordinate rotation
- Despain (1974): First CORDIC for DFT computation
- Widely used in FPGA architectures for trigonometric functions

**Limitations**:
- 8×8 block size only (not scalable to larger blocks without modification)
- Fixed-point arithmetic (limited precision compared to floating-point)
- Manual RTL design requires significant development time
- Control unit gate count still increases with model complexity (trade-off)

**Future Directions** (implied):
- Extension to larger block sizes (16×16, 32×32)
- FPGA implementation and synthesis results
- Power consumption analysis and measurements
- Comparison with modern CORDIC-based accelerators

**Connection to Research Arc**:
- Follows pivezhandi2015statistical (ML/pattern recognition)
- Establishes hardware design expertise (ASIP, RTL, digital design)
- Precursor to FPGA work in pivezhandi2020parahist (FPGA optical flow)
- Foundation for hardware-efficient implementations in later embedded systems research

**Related Work**:
- Yu et al. (2011): 2D DFT on FPGA using 2D decomposition
- Chidambaram (2005): Scalable FFT processor for UWB-OFDM
- Oruklu et al. (2012): Reduced memory CORDIC-based FFT
- Huang & Xiao (2014): CORDIC-based fast DCT for VLSI
- Hassan et al. (2012): Reconfigurable ASIP for DFT/DCT/FIR engine

**Hardware Specifications**:
- Data type: 32-bit fixed-point (split: 16-bit real + 16-bit imaginary)
- Frequency: Not specified (RTL implementation, synthesis dependent)
- Memory: RAM (64×16 + 64×32), ROM (2×16×16), Cache (embedded)
- Functional units: 3 (A, B, C) with ALSU capabilities
- Registers: Multiple 32-bit data registers, address registers, control registers

## Bibdata

```bibtex
@inproceedings{pivezhandi2016asip,
  title={ASIP design for two dimensional cordic based DFT and DCT algorithms},
  author={Pivezhandi, Mohammad and Eshghi, Mohammad},
  booktitle={2016 6th International Conference on Computer and Knowledge Engineering (ICCKE)},
  pages={269--273},
  year={2016},
  organization={IEEE}
}
```
