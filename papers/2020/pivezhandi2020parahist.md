# Parahist: Fpga implementation of parallel event-based histogram for optical flow calculation

**Citation Key**: `pivezhandi2020parahist`

**Year**: 2020

**Type**: inproceedings

**Authors**: Pivezhandi, Mohammad and Jones, Phillip H and Zambreno, Joseph

**Venue**: 2020 IEEE 31st International Conference on Application-specific Systems, Architectures and Processors (ASAP)

---

## Key Points

- FPGA-based architecture for event-based camera optical flow histogram generation
- Time compression: stores time difference between consecutive events (not absolute time)
- Reduces timestamp encoding from 32 to 7 bits with only ~3% histogram accuracy loss
- Throughput: >200 million events/sec, 100-2700 Gbps on-chip bandwidth
- 1000× speedup over Java (1627.5×), ~800× speedup over C++ (791.9×) for r=3
- Platform: Xilinx ZCU106 FPGA with UltraRAM and BRAM hybrid memory

## Abstract

This paper presents an FPGA-based architecture for histogram generation to support event-based camera optical flow calculation. Event-based cameras use biologically inspired sensors that only transmit information of pixels that have changed, overcoming throughput limitations of traditional frame-based cameras. This behavior allows event-based cameras to achieve much lower latency and greater effective frame update rates.

The proposed histogram generation mechanism reduces memory and logic resources by storing the time difference between consecutive events, instead of the absolute time of each event. This compression approach concatenates consecutive timestamps for a pixel, allowing storage of time differences rather than absolute timestamps. For full time precision, the size of timestamps reduces from 32 bits to 18 bits when moving from storing absolute time to time differences.

Additionally, the architecture explores the trade-off between system resource usage and histogram accuracy as a function of the precision at which time is encoded. Results show that across three event-based camera benchmarks (rotating disk, translating sinusoid), the encoding of time can be reduced from 32 to 7 bits with a loss of only approximately 3% in histogram accuracy.

The architecture can process over 200 million events/sec and shows significant speedup compared to software implementations. The design shows promise for being integrated as part of a larger event-based camera-driven computer vision system for optical flow or object tracking in applications such as blurred video reconstruction, high-speed object tracking, and real-time collision avoidance for autonomous vehicles or unmanned air vehicles.

## Contributions

1. **Pipelined 6-stage FPGA architecture** for event-based histogram generation supporting optical flow calculation with parallel processing capabilities
2. **Novel time compression technique**: Stores time differences between consecutive events instead of absolute timestamps, reducing memory footprint from 32 bits to 18 bits (full precision) or down to 7 bits with minimal accuracy loss
3. **Flexible precision-accuracy trade-off**: Design supports adjustable time precision (Wdt) allowing trade-off between resource usage and histogram accuracy (3% loss with 32→7 bit reduction)
4. **Background activity filter for noise removal**: Time threshold (Δtn) to identify and remove temporally distant events likely caused by random glitches
5. **Parallelized timestamp decompression**: Radix4-Sklansky prefix adder for concurrent summation of time differences, reducing steps from 2log₂(n) to log₂(n) compared to Brent-Kung approach
6. **Resource-efficient 2-D addressing scheme**: Lowest 3 bits of x and y addresses index into 8×8 grid of RAMs, enabling parallel access to 64 pixel regions simultaneously

## Methodology

**Overall Approach**:
The architecture implements a fully pipelined design for generating histograms from event-based camera data. Event-based cameras use asynchronous temporal contrast vision sensors that only transmit pixel changes, enabling sub-microsecond processing times with sufficient parallelism.

**6-Stage Pipeline Architecture**:

**Stage 1: Event Memory Mapping**
- Streams events from event-based camera into input FIFO
- Event format: AEDAT 2.0 (Address-Event Representation)
  - 32 bits: timestamp
  - 32 bits: polarity (type), x/y address, pixel intensity (ADC), select part (Read APS)
- Compresses events and stores in 2-D array of Block RAMs (BRAM) and UltraRAMs (RAMU)
- Compression: Concatenates consecutive timestamps for a pixel, storing differences instead of absolute time
- Ring buffer structure with variable element width (Wts)
- Timestamp width formula: Wts = Wdata × PN − (Hs × Wdt) − ⌊log(Hs)⌋
  - Wdata: bits of precision required
  - Wdt: bits to store time differences
  - Hs: histogram capacity
  - PN: data precision factor
- 2-D addressing: Lowest 3 bits of x address and lowest 3 bits of y address index into 8×8 RAM array
- Enables parallel access to 64 pixel regions simultaneously

**Stage 2: Noise Removal**
- Background activity filter concept [Linares-Barranco et al., ISCAS 2015]
- Moving objects cause events temporally close in spatial neighborhood
- Time threshold (Δtn) identifies noise events
- Filter condition: tsc − tsp₋₁:₁,₋₁:₁ < Δtn
  - tsc: current timestamp
  - tsp₋₁:₁,₋₁:₁: previous timestamps in neighboring regions
- Events outside threshold removed as random glitches

**Stage 3: Timestamp Decompression**
- Parallelized prefix adder sums time differences concurrently
- Implementation: Radix4-Sklansky approach [Merrill & Garland, NVIDIA 2016]
- Advantage: log₂(n) steps vs 2log₂(n) for Brent-Kung [Brent & Kung 1982]
- Trade-off: 36 operators (vs 26 for Brent-Kung) but fewer pipeline stages
- Subtracts previous timestamp to obtain decompressed timestamps
- Outlier removal: timestamps greater than tcurr − threshold are zeroed
- Ring buffer shifted right by one position

**Stage 4: Histogram Update**
- Counts remaining valid timestamps (after outlier removal)
- Increments appropriate histogram bins
- Shifts event ring buffer by one position to the right

**Stage 5: Write Back**
- Updates previous timestamp of compressed events in Event Mapping stage
- Uses current timestamp from Histogram Update stage

**Stage 6: Gradient Calculation Output**
- Updated histograms placed into FIFOs for application use
- Supports gradient calculations for optical flow

**Data Representation**:
Compressed event format stored in each memory line:
- size field: ⌊log(Hs)⌋ bits (histogram element count)
- tsp field: Wts bits (previous timestamp)
- dt₀...dtₙ fields: Wdt bits each (time differences in ring buffer)

**Implementation**:
- HDL: VHDL
- Synthesis tool: Vivado HLx 2018.1
- Platform: Xilinx ZCU106 FPGA (UltraScale+)
- Memory: Hybrid UltraRAM (RAMU) and BRAM (RAMB) depending on data width
- UltraRAM configuration: 72-bit width fixed modules
- Data width: 72 bits (precision=1) or 144 bits (precision=2)

**Benchmarks**:
1. **Rotating disk**: Disk with 8 compartments, camera moves around z dimension
2. **Translating sinusoid**: Pattern varies sinusoidally in horizontal direction, camera panned clockwise around y-axis (shifts left)
   - Up to 1396 pixels spiking simultaneously
   - Required processing time: 10⁻⁶/1396 ≈ 0.71 nanoseconds to keep up
3. Event packet size: 90,000 events for runtime analysis

**Search Distance (Radius)**:
- r=1: 3×3 neighborhood (9 regions)
- r=2: 5×5 neighborhood (25 regions)
- r=3: 7×7 neighborhood (49 regions)
- Exponentially increases parallel processing regions

## Results

**Throughput Performance**:

**Off-chip Throughput**:
- >200 million events/sec (equals target frequency)
- Processing time: constant 1/Freq regardless of search distance (fully parallel)

**On-chip Throughput**:
- Formula: Frequency × DataWidth × SearchDistance
- Range: 100 Gbps to 2700 Gbps
- Scales with search distance and data width at constant frequency

**Speedup Comparison** (Table II - Rotating Disk Benchmark):

| Radius (r) | FPGA (µsec) | JAER Java (µsec) | C++ (µsec) | Speedup vs C++ | Speedup vs Java |
|------------|-------------|------------------|------------|----------------|------------------|
| 1 (3×3) | 0.00493 | 6.4261 | 1.1581 | 234.9× | 1303.5× |
| 2 (5×5) | 0.00482 | 6.7020 | 2.2346 | 463.6× | 1390.5× |
| 3 (7×7) | 0.00499 | 8.1021 | 3.9516 | 791.9× | 1627.5× |

**Key Findings**:
- FPGA shows ~1000× speedup over Java implementation (JAER)
- FPGA shows ~500-800× speedup over C++ implementation
- Fully parallel design maintains constant runtime of 1/Freq across different search distances
- Software runtime increases linearly with search distance

**Resource Utilization** (Table I - Xilinx ZCU106 FPGA):

**72-bit Data Width**:

| Config | RAMB | RAMU | FF | LUT | RAML |
|--------|------|------|------|--------|------|
| 3×3 | 0 (0.00%) | 64 (66.66%) | 10,969 (2.38%) | 25,245 (10.95%) | 442 (0.43%) |
| 5×5 | 0 (0.00%) | 64 (66.66%) | 12,421 (2.69%) | 68,899 (29.90%) | 450 (0.44%) |
| 7×7 | 0 (0.00%) | 64 (66.66%) | 13,470 (2.92%) | 144,857 (62.86%) | 463 (0.45%) |

**144-bit Data Width** (Precision=2):

| Config | RAMB | RAMU | FF | LUT | RAML |
|--------|------|------|------|--------|------|
| 3×3 | 104 (33.33%) | 51 (53.12%) | 16,716 (3.62%) | 24,737 (10.73%) | 442 (0.43%) |
| 5×5 | 112 (35.89%) | 50 (52.08%) | 16,910 (3.66%) | 42,510 (18.45%) | 450 (0.44%) |
| 7×7 | 152 (48.72%) | 45 (46.87%) | 17,853 (3.87%) | 79,634 (34.56%) | 465 (0.45%) |

**Resource Trends**:
- UltraRAM usage: Fixed at 72-bit width multiples (precision=1 uses 64 RAMU, precision=2 uses hybrid BRAM+RAMU)
- Search distance exponentially increases LUT usage (10.95% → 29.90% → 62.86% for 72-bit)
- Flip-flop usage remains low (<4% across all configurations)
- BRAM required when data width exceeds 72 bits (precision > 1)

**Time Precision vs Accuracy Trade-off**:
- Baseline: 32-bit absolute timestamps
- Full precision compression: 18-bit time differences
- Reduced precision: 7-bit time differences
- Accuracy loss: ~3% when reducing from 32 to 7 bits
- Example: Histogram capacity Hs=4 bits, Wdt=4 bits, precision=1 → 72−(4+16×4)=4 bits per timestamp

**Power and Throughput Analysis** (Figure 5):
- Histogram sizes tested: 8, 12, 16
- Radius values: r=1, 2, 3
- Data widths: 72, 144, 216, 288 bits
- On-chip throughput scales linearly with data width and search distance

**Processing Time Analysis**:
- Per-frame latency equivalent: ~1 microsecond (event-based cameras)
- Translating boxes benchmark: 1396 pixels simultaneously
- Required processing time: 0.71 nanoseconds to keep up
- FPGA architecture meets real-time requirements with parallel processing

**Benchmark Details**:
- Rotating disk: 8 compartments, 90,000 events per packet
- Translating sinusoid: Horizontal sinusoidal pattern, camera pans clockwise
- Mean runtime per event measured across benchmarks

## Notes

**Research Context**:
- **First FPGA publication** by Mohammad Pivezhandi (2020)
- **Affiliation**: Electrical and Computer Engineering, Iowa State University, Ames, Iowa 50011
- **Co-authors**: Phillip H. Jones (ECE faculty), Joseph Zambreno (ECE faculty)
- **Conference**: IEEE ASAP 2020 (31st International Conference on Application-specific Systems, Architectures and Processors)
- **Pages**: 185-188 (4-page conference paper)

**Application Domains**:
- Event-based vision systems for optical flow calculation
- High-speed object tracking
- Real-time collision avoidance for autonomous vehicles
- Unmanned air vehicles (UAVs)
- Blurred video reconstruction
- Computer vision systems requiring sub-microsecond latency

**Event-Based Camera Technology**:
- Biologically inspired sensors (neuromorphic cameras)
- Only transmit pixel changes (not full frames)
- Asynchronous sparse events
- Advantages: Lower latency, higher effective frame rates, reduced data throughput
- Challenge: Processing asynchronous events and managing per-pixel metadata

**Technical Innovation**:
- **Time compression**: Key contribution reducing memory from 32-bit absolute to 18-bit (or 7-bit) differences
- **Ring buffer structure**: Concatenates consecutive timestamps for efficient storage
- **Parallel prefix adder**: Radix4-Sklansky vs Brent-Kung trade-off (fewer stages, more operators)
- **2-D addressing scheme**: 8×8 grid enables 64-way parallel access to pixel regions
- **Hybrid memory**: UltraRAM (72-bit fixed width) + BRAM for larger data widths

**Comparison with Prior Art**:
- JAER (Java): jAER toolkit for event-based vision processing (reference software)
- C++ implementation: Optimized software version
- FPGA shows 1000× speedup over Java, ~800× over C++
- Prior FPGA work: Linares-Barranco et al. (ISCAS 2015) - USB3.0 FPGA event-based filtering

**Design Trade-offs**:
- **Precision vs Accuracy**: 32→7 bits saves 78% memory with 3% accuracy loss
- **Resources vs Search Distance**: Larger neighborhoods increase LUT usage exponentially
- **Operators vs Stages**: Radix4-Sklansky (36 operators, log₂(n) stages) vs Brent-Kung (26 operators, 2log₂(n) stages)
- **Memory Type**: UltraRAM for 72-bit width, hybrid BRAM+RAMU for wider data

**Hardware Specifications**:
- **Platform**: Xilinx ZCU106 Evaluation Board (UltraScale+ MPSoC)
- **Memory**: UltraRAM (RAMU, 72-bit fixed width), Block RAM (RAMB, 36Kb)
- **Logic**: Flip-flops (FF), Look-up Tables (LUT), Distributed RAM (RAML)
- **Data Width**: 72 bits (precision=1) or 144 bits (precision=2)
- **Frequency**: Target frequency determines off-chip throughput

**Event Format**:
- **AEDAT 2.0**: Address-Event Representation standard
- **64 bits total**: 32-bit timestamp + 32-bit event data
- **Event data fields**: type (polarity), xaddr, yaddr, ADC sample (pixel intensity), Read APS select

**Limitations**:
- Fixed UltraRAM width (72 bits) constrains design space
- Larger search distances significantly increase LUT usage
- 3% accuracy loss with maximum compression (may be unacceptable for some applications)
- Limited to event-based camera inputs (not applicable to traditional frame-based cameras)

**Future Directions** (implied):
- Integration into complete optical flow calculation pipeline
- Extension to other event-based vision tasks (object detection, classification)
- Multi-scale histogram processing
- Adaptive precision based on scene dynamics

**Connection to Research Arc**:
- Follows pivezhandi2017accuracy (2017, hardware modeling expertise)
- Precedes pivezhandi2023toward (2023, master's thesis on RL for resource scheduling)
- Establishes FPGA implementation expertise at Iowa State University
- Foundation for later embedded systems and real-time scheduling work
- Demonstrates hardware acceleration and parallel processing skills

**Related Work**:
- Amir et al. (CVPR 2017): Low-power event-based gesture recognition
- Lichtsteiner et al. (IEEE J-SSC 2008): 128×128 asynchronous temporal contrast vision sensor
- Linares-Barranco et al. (ISCAS 2015): USB3.0 FPGA event-based filtering and tracking
- Merrill & Garland (NVIDIA 2016): Single-pass parallel prefix scan with decoupled look-back
- Brent & Kung (IEEE TC 1982): Regular layout for parallel adders

**Code Availability**:
- Not mentioned in paper
- VHDL implementation synthesized with Vivado HLx 2018.1

**Performance Highlights**:
- **Constant-time processing**: 1/Freq regardless of search distance (fully parallel)
- **Real-time capable**: Processes 200M events/sec, handles 1396 simultaneous pixels
- **Memory efficient**: 78% reduction in timestamp storage (32→7 bits)
- **Massive speedup**: 1627.5× faster than Java, 791.9× faster than C++ (r=3)

## Bibdata

```bibtex
% Row 4
@inproceedings{pivezhandi2020parahist,
  title={Parahist: Fpga implementation of parallel event-based histogram for optical flow calculation},
  author={Pivezhandi, Mohammad and Jones, Phillip H and Zambreno, Joseph},
  booktitle={2020 IEEE 31st International Conference on Application-specific Systems, Architectures and Processors (ASAP)},
  pages={185--188},
  year={2020},
  organization={IEEE}
}
```
