# Energy-and temperature-aware scheduling: From theory to an implementation on intel processor

**Citation Key**: `bashir2022energy`

**Year**: 2022

**Type**: inproceedings

**Authors**: Bashir, Qaisar and Pivezhandi, Mohammad and Saifullah, Abusayeed

**Venue**: 2022 IEEE 24th Int Conf on High Performance Computing & Communications; 8th Int Conf on Data Science & Systems; 20th Int Conf on Smart City; 8th Int Conf on Dependability in Sensor, Cloud & Big Data Systems & Application (HPCC/DSS/SmartCity/DependSys)

---

## Key Points

- Energy, performance, and temperature-aware semi-partitioned scheduling for real-time multicore
- 15% average power savings, 3°C average temperature reduction, 100% thermal emergency avoidance
- First work combining energy, performance, reliability, and temperature together in real-time scheduling
- Implemented and tested on Intel Xeon 2680 v3 multicore platform (12 cores)
- Hot and cold task classification (ζ power deviation factor)
- DVFS + DPM + chip floor-plan + reactive task migration
- **Mohammad Pivezhandi: Co-author** (2nd of 3 authors, Wayne State University)

## Abstract

Temperature, energy, and performance are key considerations of real-time multicore systems. Thermal hotspots and high temperatures not only degrade reliability and performance but also increase cooling costs and leakage current (accounts for half of total power when temperature exceeds 86°C). To address these issues, this paper proposes an energy, performance, and temperature-aware semi-partitioned scheduling technique for sporadic real-time tasks.

The proposed approach incorporates DVFS (Dynamic Voltage and Frequency Scaling), Dynamic Power Management (DPM), and chip floor-plan in classical partitioned scheduling. To further reduce temperature, energy, hotspots, and gradients, the proposed partitioned scheduling considers individual task behaviors in the form of hot and cold tasks and applies reactive thermal management techniques such as task migration (hence called semi-partitioned scheduling) for system safety.

To the authors' knowledge, this is the first paper that considers energy, performance, reliability, and temperature together in real-time multicore scheduling. The results are presented through experiments on the Intel Xeon 2680 v3 multicore platform. The results show that the proposed approach on average saves 15% power, reduces 3°C average temperature, improves task schedulability, and avoids 100% thermal emergencies compared to the state-of-the-art technique (TBP - Temperature-Based Partitioning).

The complex interdependence between temperature, performance, and energy makes temperature, performance, and energy-aware scheduling techniques challenging. Each core's temperature profile depends on its power profile, floor plan, neighboring cores' power profiles, and execution pattern of tasks. The approach regulates the voltage and frequency of each core by taking into account individual task behavior, thereby enabling control of energy consumption as well as temperature of each core.

## Contributions

1. **Temperature- and energy-aware semi-partitioned scheduling**: Observes that fixed partitioned scheduling may not ensure system reliability under adverse heat effects; proposes semi-partitioned scheduling allowing occasional task migration with negligible overhead
2. **Comprehensive thermal-aware design**: Takes into account temperature profile of each core, floor-plan, neighboring cores' power profiles, execution pattern of tasks based on characteristics, and individual behaviors in form of hot and cold tasks
3. **Hot and cold task classification**: Introduces power deviation factor ζ (0.01 to 1.0) to differentiate hot tasks (ζ > 0.5) from cold tasks (ζ ≤ 0.5) for effective thermal management
4. **Reactive thermal management with task migration**: Uses multiple temperature thresholds (Tthreshold, Tthreshold(1), Tthreshold(2)) and migrates tasks when cores approach thermal limits or have small workloads
5. **First implementation on commercial platform**: First implementation and systematic experimentation of such real-time scheduling using actual processor workload on Intel Xeon 2680 v3 commercial multicore platform
6. **Task dropping mechanism for thermal emergencies**: Assigns dropping priority χi (1-6) to tasks, drops low-priority tasks when all cores exceed Tthreshold(1) to avoid permanent failure

## Methodology

**Problem Context**:
Real-time scheduling of constrained deadline sporadic tasks on multicore platforms considering temperature, energy, reliability, and performance trade-offs. Power density doubling every three years, cooling costs $3 per watt of heat dissipation, leakage current accounting for 50% of power when temperature exceeds 86°C.

**System Model**:

**Task Model**:
- Task set τ = {τ1, τ2, ..., τn} consisting of n sporadic tasks with constrained deadlines
- Each task τi characterized by five-tuple: (Ci, Di, ρi, ζi, χi)
  - Ci: Worst-case execution time
  - Di: Deadline (Di ≤ ρi)
  - ρi: Time period (minimum duration before task repeats)
  - ζi: Power deviation factor (0.01 to 1.0, fraction of core power consumed by task)
    - Hot task: ζ > 0.5
    - Cold task: ζ ≤ 0.5
  - χi: Dropping priority level (1-6, lower value = higher priority to be dropped)
- Task utilization: ui = Ci/ρi
- Total utilization: usum = Σ(i=1 to n) ui

**Platform Model**:
- m identical DVFS-enabled cores {M1, M2, ..., Mm}
- Discrete frequency levels (Intel Xeon 2680: 14 levels from 1.2 to 2.5 GHz)
- Per-core P-states (frequency-voltage combinations)
- Uncore Frequency Scaling (UFS): Independent frequency control for uncore components (RAM, caches)
- Low energy modes: {C0 (Active/Running), C1 (Halt/Idle), C1E (Auto Halt), C2 (Stop Clock), C3 (Sleep), C6 (Power Gating)}
- Chip floor-plan considerations: distance between cores, thickness, width

**Power Model**:
```
P = β + αs³
```
Where:
- β: Static power (temperature-dependent leakage)
- α: Switching capacitance
- s: Speed/frequency

**Energy Model**:
```
E(C, s) = (β + αs) · (C/s) + αCs²
```

**Temperature-Dependent Static Power**:
```
β = IL · Vop
IL = I0 · (J²/T²ambient) · e^(α·Vop·(J-Tambient)/(J·Tambient))
```
Where:
- IL: Leakage current (increases with temperature)
- Vop: Operating voltage
- I0: Leakage current at ambient temperature
- J: Junction temperature (highest operating temperature)
- Tambient: Ambient temperature

**Frequency-Voltage Relationship**:
```
f = RC · (Vop - Vtt)²/Vop
```
Where:
- f: Processor frequency
- RC: Constant relating frequency to voltage
- Vtt: Transistor threshold voltage

**Thermal Model (Single Core)**:
```
Tj = p[t2-t1] · Rthermal + S2 + Tambient

S2 = [Tj(0) · p[(t2)-(t1)] · Rthermal - Tambient] · e^(-(t2-t1)/(Rthermal·C))
```
Where:
- Tj: Temperature of jth core
- p[t2-t1]: Power consumption during time interval [t2 - t1]
- Rthermal: Thermal coefficient
- Tj(0): Reference temperature at time 0

**Thermal Model (Multi-Core with Neighboring Effects)**:
```
⎡T1⎤   ⎡T1(initial)⎤   ⎡p1⎤
⎢T2⎥   ⎢T2(initial)⎥   ⎢p2⎥
⎢T3⎥ = ⎢T3(initial)⎥ + ⎢p3⎥ · [A]     (Equation 2)
⎢..⎥   ⎢    ...   ⎥   ⎢..⎥
⎣Tm⎦   ⎣Tm(initial)⎦   ⎣pm⎦
```

Where A is m×m matrix:
```
     ⎡p11  p12  p13  ...  p1m⎤
     ⎢p21  p22  p23  ...  p2m⎥
A =  ⎢p31  p32  p33  ...  p3m⎥
     ⎢ ...  ...  ...  ...  ...⎥
     ⎣pm1  pm2  pm3  ...  pmm⎦
```

Matrix element pij: Impact of power consumption of jth core on ith core
```
pij = (fi - fj) / (U(fi) - U(fj))

U(fi) = Σ(k=1 to n) bki · C^fi_k · ζk · αs
```
Where:
- bki: Boolean (1 if task τk runs on core i, 0 otherwise)
- C^fi_k: WCET of τk at frequency fi

**Request Bound Function (rbf)**:
```
rbf(τi, L) = (ξ · Ci) + (ξ·Ci/ρi) · L     (Equation 3)
```
Where ξ relates frequency to WCET

**Proposed Semi-Partitioned Scheduling**:

**A. Initial Partitioning**:

Uses Deadline Monotonic (DM) scheduling (optimal for sporadic tasks with constrained deadlines).

**Feasibility Condition**:
Task τi can be assigned to core Mk if:
```
(Di - Σ(j=1 to n) bjk · rbf(τj, Di)) ≥ ξ · Ci     (Condition 4)
```
Where bjk = 1 if task τj already assigned to core k

**Process**:
1. Assign tasks to first available core satisfying Condition (4)
2. If not satisfied, switch to next frequency level and adjust execution requirements
3. Continue increasing frequency (up to fmax) until tasks are feasible
4. If no frequency level works, return partitioning failed

**B. Energy Minimization**:

**Critical Frequency Range**:
- Define [fmin, fmax] as energy-efficient frequency range (near critical frequency)
- Frequencies much lower or higher than critical frequency increase leakage power
- Algorithm never chooses frequency < fmin, limits at fmax

**Core Utilization Strategy**:
- Minimize number of active cores
- Keep unused cores in low energy modes (consumes much less power than active/idle)
- Example: Intel PXA270 deep sleep (0.116W active vs 0.064W idle at 104 MHz)
- Continue assigning tasks to core up to fmax before activating new core
- Move core from low energy mode to running mode only when needed

**C. Handling Temperature Threshold and Reliability**:

**Multiple Temperature Thresholds**:
- Tthreshold: Maximum threshold temperature (system limit)
- Tthreshold(1): Just below maximum (for critical interventions)
- Tthreshold(2): Lower threshold (for proactive management)
- Hierarchy: Tthreshold > Tthreshold(1) > Tthreshold(2)

**Task Migration Policy** (Algorithm 1 & 2):

**Scenario 1: Core has Small Workload** (Algorithm 1):
- Let τd = set of tasks from core Mi that need to migrate
- Let m1 = number of active cores operating in [fmin, fmax] with Ti < Tthreshold(2)
- Sort m1 cores by increasing temperature
- For each task in τd, check Condition (4) on m1 cores
- If satisfied: assign task, update rbf
- If not satisfied and frequency < fmax: increase frequency, retry
- If τd empty: move core to low energy mode
- If τd not empty: continue executing remaining tasks

**Scenario 2: Operating Temperature Approaches Tthreshold** (Algorithm 2):
- If Ti between Tthreshold(2) and Tthreshold(1) and m1 ≠ 0:
  - Select task from τd with maximum ζi (hot task)
  - Check Condition (4) on m1 cores
  - If satisfied: assign task, repeat until Ti < Tthreshold(2)
  - If not satisfied and frequency < fmax: increase frequency
- If m1 = 0 and Ti > Tthreshold(2):
  - Select task with maximum χi (lowest priority) and drop current job
  - Continue dropping until Ti < Tthreshold(2)

**Migration Overhead**:
- Negligible: Instructions redistributed through program counter (PC) change
- Reconfiguration takes few clock cycles (flush/stall)
- Clock in gigahertz range, so latency negligible

**D. Switching Mechanism and Break-Even Time (BET)**:

**State Transition Times** (Intel PXA270 - Figure 1):
- Running ↔ Idle: 0 ms
- Running → Standby: 0.3 ms, Standby → Idle: 12 ms
- Running → Sleep: 3 ms, Sleep → Idle: 137 ms
- Running → Deep Sleep: 4 ms, Deep Sleep → Idle: 261 ms

**Break-Even Time**:
```
BET = tlow + twake
```
Where:
- tlow: Time to move to low energy mode
- twake: Wake-time (time to move from low energy to idle/active)

**Strategy**:
- Proposed algorithm moves core from low energy to idle mode proactively when running core approaches fmax or Tthreshold
- Newly arrived/migrated task can start execution instantly
- Moves core back to low energy mode only if workload feasibly assigned to other active cores with Ti < Tthreshold(2)

**E. Handling Power Deviation and Extreme Thermal Conditions**:

**Power Deviation Factor ζ**:
- Used to differentiate hot (ζ > 0.5) and cold (ζ ≤ 0.5) tasks
- Tracked for tasks assigned to each core
- Used for task assignment and migration decisions
- When migrating to reduce temperature: select task with maximum ζ (hot task) first
- Migrating hot task leads to sharp decrease in core temperature

**Spatial and Temporal Gradient Control**:
- Two counters per core: selection count, active time duration
- Ensures all cores equally used (avoids temporal gradients)
- Avoids spatial gradients (temperature imbalance across cores)

**Extreme Thermal Emergencies**:
- When no core available to migrate tasks and all cores near Tthreshold
- Drop tasks with lowest dropping priority χi
- Chosen dropping over halting:
  - Simpler implementation (get Linux process ID, assign priorities)
  - Halting requires saving current state, associated variables
  - Intel memory architecture doesn't provide memory unlocking
  - Execution must restart from beginning after halting

**Full Algorithm** (Algorithm 3):

**Step 1**: Get list of active cores (m1)
**Steps 2-4**: Task assignment when m1 < m and frequency in range and Ti < Tthreshold(2)
- Sort m1 by increasing Ti
- Assign task if Condition (4) satisfied
**Step 6**: Frequency scaling on individual core if Condition (4) not satisfied
**Step 7**: Move core from low energy to active if all active cores at fmax
**Steps 8-9**: Task migration mechanism
**Steps 10-13**: Task dropping process when Ti > Tthreshold(1)

**Implementation Details**:

**Platform**: Intel Xeon 2680 v3 (12 cores, 2.5 GHz base frequency)
- Per-core P-states (14 frequency levels: 1.2 to 2.5 GHz, see Table III)
- Hardware-controlled (Intel Speed Shift) or OS-controlled (Intel Speed Step)
- Uncore Frequency Scaling (UFS): Independent control of RAM, caches frequency
- Active Processor (AP) uncore frequencies: 1.2 to 2.2 GHz
- Passive Processor (PP) uncore frequencies: 1.2 to 2.1 GHz
- Per-core C-states: C0 (Active), C1 (Halt), C1E (Auto Halt), C2 (Stop Clock), C3 (Sleep), C6 (Power Gating)

**Workload Generation** (Algorithm 4):
- Custom program generating system workload with multiple processes per core
- Dynamic adjustment of number of processes and elapsed time (milliseconds)
- getchar() function blocks program without consuming time
- Generates stable utilization with ±2% variation

**Comparison Baselines**:
1. **TBP (Temperature-Based Partitioning)**: State-of-the-art technique
2. **DM (Deadline Monotonic)**: Without temperature threshold
3. **DM with threshold**: Basic thermal management

## Results

**Power Consumption Savings** (Figure 2, Table V):

**Time in Low Energy C-States** (Table V):

| Utilization | Technique | C0% (Active) | C1% | C2-C3% (Low Energy) |
|-------------|-----------|--------------|-----|---------------------|
| 25% | Proposed | 28.9 | 12.4 | **58.7** |
| 25% | TBP | 26.3 | 33.0 | 39.9 |
| 50% | Proposed | 53.5 | 12.3 | **34.1** |
| 50% | TBP | 52.8 | 30.6 | 16.5 |
| 75% | Proposed | 81.3 | 16.6 | **2.1** |
| 75% | TBP | 81.1 | 18.5 | 0.3 |

**Key Findings**:
- At 25% utilization: Proposed technique spent **19% more time** in low C-states vs TBP
- At 50% utilization: **17% more time** in low energy states
- At 75% utilization: **1.8% more time** in low energy states
- Proposed technique controls cores individually, only moves to C0/C1 when necessary

**Average Power Consumption** (Figure 2):
- **15% average power savings** compared to TBP across all utilization factors (25%, 50%, 75%, 100%)
- **20% more power savings** compared to DM (with threshold)
- Power consumption increases linearly with utilization
- Proposed technique maintains lowest power consumption curve

**Temperature Reduction** (Figures 3 & 4):

**Average System Temperature** (Figure 3 - 60% utilization):
- Proposed technique: Average temperature **below Tthreshold(2)** (~75°C)
- TBP: Average temperature **near Tthreshold** (~80°C)
- DM (no threshold): Temperature **exceeds Tthreshold** (continuous increase)
- **3°C average temperature reduction** compared to TBP

**Smooth Temperature Behavior**:
- Proposed technique shows smooth temperature curve (avoids large thermal cycles)
- TBP: Large variations in operating temperature due to high frequency operation
- Large thermal cycles directly reduce system reliability, may cause permanent damage

**Individual Core Temperatures** (Figure 4 - 60% utilization):
- All 12 cores maintained **below Tthreshold** (maximum threshold)
- Core temperatures range between **40-80°C**
- Three threshold levels visible: Tthreshold (~85°C), Tthreshold(1) (~82°C), Tthreshold(2) (~78°C)
- Smooth temperature profiles for each core (Core0 through Core11)
- Balanced temperature distribution (avoids spatial gradients)

**Thermal Emergency Avoidance**:
- **100% thermal emergency avoidance** compared to state-of-the-art (TBP)
- No cores exceeded Tthreshold during experiments
- DM (without threshold) experienced thermal emergencies (temperature violations)

**Task Schedulability Improvement**:
- Improved task schedulability compared to TBP
- More tasks successfully scheduled within deadline constraints
- Better utilization of frequency range [fmin, fmax]

**Detailed Core-by-Core Analysis** (Table V):

Example at 50% utilization:
- Core 01: Proposed (52.4% C0, 13.2% C1, 34.4% C2-C3) vs TBP (54.4% C0, 27.3% C1, 18.3% C2-C3)
- Core 02: Proposed (53.2% C0, 10.9% C1, 35.9% C2-C3) vs TBP (51.6% C0, 26.6% C1, 21.8% C2-C3)
- Core 11: Proposed (59.2% C0, 9.6% C1, 31.2% C2-C3) vs TBP (53.3% C0, 29.6% C1, 17.1% C2-C3)

**Key Observation**: Proposed technique maintains lower C0 (active) time while significantly increasing C2-C3 (low energy) time

**Temperature Behavior Under Proposed Technique** (Figure 4):
- Cores 0-3: Temperature range 40-75°C
- Cores 4-7: Temperature range 45-78°C
- Cores 8-11: Temperature range 42-80°C
- All cores remain well below Tthreshold (85°C)
- Smooth transitions, no sharp spikes

## Notes

**Research Context**:
- **CO-AUTHORED PAPER**: Mohammad Pivezhandi is **2nd author of 3** (not first author)
- **First author**: Qaisar Bashir (AMD, Inc., California)
- **Second author**: **Mohammad Pivezhandi** (Wayne State University, Michigan)
- **Third author**: Abusayeed Saifullah (Wayne State University, Michigan - Mohammad's advisor)

**Mohammad's Affiliation at Time of Publication**:
- Wayne State University, Michigan (PhD program)
- Working with Prof. Abusayeed Saifullah (real-time systems, embedded systems)

**Conference**: IEEE HPCC/DSS/SmartCity/DependSys 2022
- IEEE 24th International Conference on High Performance Computing & Communications
- 8th International Conference on Data Science & Systems
- 20th International Conference on Smart City
- 8th International Conference on Dependability in Sensor, Cloud & Big Data Systems & Application
- Pages: 1922-1930
- 9-page conference paper

**Application Domains**:
- Real-time embedded systems
- Multicore thermal management
- Energy-aware computing
- Reliability-critical systems
- High-performance computing clusters
- Temperature-sensitive applications
- Mission-critical systems requiring thermal safety

**Technical Innovation**:
- **First combined approach**: Energy + performance + reliability + temperature together
- **Hot/cold task classification**: Novel use of power deviation factor ζ
- **Multi-threshold thermal management**: Proactive (Tthreshold(2)) and reactive (Tthreshold(1)) thresholds
- **Neighboring core effects**: Comprehensive thermal model accounting for spatial heat transfer
- **Real implementation**: First systematic experimentation on commercial Intel platform with actual workload

**Comparison with Related Work**:

**Semi-Partitioned Scheduling** [Casini et al., IEEE TC 2020]:
- Existing work doesn't consider temperature or energy
- This work adds thermal and energy awareness

**Temperature-Aware Techniques**:
- **Prediction-based** [Hwang et al., TODAES 2000]: Act only at threshold
- **Model-based stochastic** [Zanini et al., ASP-DAC 2009]: Control theory for thermal balancing
- **Floor-planning** [Zhou et al., IEEE TPDS 2009]: 3D multicore thermal-aware scheduling
- **This work**: Combines multiple techniques with energy and performance

**Energy-Aware Techniques** [Bhuiyan et al., ACM TECS 2018, IEEE TPDS 2020]:
- Focus on energy under performance constraints
- Don't consider temperature
- This work addresses energy-temperature trade-off

**Reliability-Aware Techniques**:
- **Frequency elevation** [Zhou et al., ASP-DAC 2016]: High energy consumption
- **Rollback recovery** [Zhang et al., IEEE TCAD 2005]: Degrades performance
- **Replication** [Haque et al., IEEE TPDS 2016]: High overhead
- This work considers reliability through thermal management

**Thermal Impact on Reliability** [Lee et al., TECS 2019]:
- 6°C temperature reduction boosts reliability by 2×
- This work achieves 3°C reduction, significant reliability improvement

**DPM-Based Techniques** [Attia et al., J. Ain Shams Eng 2017]:
- Perform well in thermal emergencies but drastically reduce performance
- This work balances DPM with DVFS for better performance-energy-temperature trade-off

**Temperature-Energy Co-optimization** [Sheikh et al., IGCC 2012]:
- Genetic algorithms with high computational overhead
- This work uses efficient online scheduling algorithm

**Core Configuration Approaches** [Bashir et al., J Comp Elec Eng 2018]:
- Provide control on group of cores only
- This work regulates individual core frequencies and states (more effective)

**Implementation Advantages**:

**Intel Xeon 2680 v3 Features**:
- Per-core P-states (14 frequency levels)
- Uncore Frequency Scaling (UFS): Differentiate hot/cold tasks
- Per-core C-states (6 power-saving states)
- Model Specific Registers (MSR) for fine-grained control
- Hardware-controlled (Speed Shift) or OS-controlled (Speed Step)

**Workload Generation**:
- Dynamic program with multiple processes per core
- Adjustable utilization with ±2% stability
- getchar() for non-intrusive program control
- Realistic system workload generation

**Acknowledgments**:
- NSF grants: CNS-2301757, CAREER-2211523, CCF-2118202, CNS-2211642
- Supported research at Wayne State University

**Limitations**:
- Intel Xeon architecture-specific (may need adaptation for other platforms)
- Break-even time calculations based on PXA270 ratios (approximate for Xeon 2680)
- Task dropping requires manual priority assignment to Linux process IDs
- Memory architecture constraints prevent halting (must use dropping)
- Fixed dropping priorities (χi) may not suit all application scenarios

**Future Directions** (implied):
- Adaptive threshold levels based on workload characteristics
- Dynamic power deviation factor (ζ) learning
- Extension to heterogeneous multicore platforms
- Integration with thermal prediction models
- Automated priority assignment for task dropping
- Hardware support for fine-grained thermal management

**Connection to Mohammad's Research Arc**:
- Follows bengre2021learning (2021, GNN+RL scheduler - co-authored)
- Precedes bhuiyan2023precise (2023, DAG scheduling - co-authored)
- **Establishes expertise in**: Thermal-aware scheduling, DVFS, DPM, real-time systems
- **Wayne State University collaboration**: Abusayeed Saifullah (advisor)
- **Foundation for later DVFS work**: Thermal management + energy awareness
- **Real-time systems focus**: Sporadic tasks, deadline constraints, schedulability

**Related Work**:
- Aydin & Yang (ISPDP 2003): Energy-aware partitioning
- Bhuiyan et al. (ACM TECS 2018, IEEE TPDS 2020): Energy-efficient DAG scheduling
- Chantem et al. (IEEE TVLSI 2010): Temperature-aware scheduling on MPSoCs
- Quan et al. (ECRTS 2009): Leakage-aware feasibility for temperature-constrained tasks
- Wang et al. (IPDPS 2010): Simple thermal model for multi-core processors
- Skadron et al. (ACM SIGARCH 2003): Temperature-aware microarchitecture
- Pagani et al. (RTSS 2013): Single frequency approximation scheme
- Hackenberg et al. (IPDPS 2015): Intel Haswell energy efficiency features
- Schöne et al. (arXiv 2019): Intel Skylake-SP energy efficiency

**Code/Data Availability**:
- Not mentioned in paper
- Custom workload generation code (Algorithm 4)
- Intel-specific implementation using MSRs

**Performance Highlights**:
- **15% power savings** vs TBP
- **20% power savings** vs DM (with threshold)
- **3°C temperature reduction** vs TBP
- **100% thermal emergency avoidance**
- **19% more time in low energy states** at 25% utilization
- **17% more time in low energy states** at 50% utilization
- **Improved schedulability** compared to state-of-the-art

## Bibdata

```bibtex
% Row 6
@inproceedings{bashir2022energy,
  title={Energy-and temperature-aware scheduling: From theory to an implementation on intel processor},
  author={Bashir, Qaisar and Pivezhandi, Mohammad and Saifullah, Abusayeed},
  booktitle={2022 IEEE 24th Int Conf on High Performance Computing \& Communications; 8th Int Conf on Data Science \& Systems; 20th Int Conf on Smart City; 8th Int Conf on Dependability in Sensor, Cloud \& Big Data Systems \& Application (HPCC/DSS/SmartCity/DependSys)},
  pages={1922--1930},
  year={2022},
  organization={IEEE}
}
```
