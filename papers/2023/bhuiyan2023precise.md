# Precise scheduling of dag tasks with dynamic power management

**Citation Key**: `bhuiyan2023precise`

**Year**: 2023

**Type**: article

**Authors**: Bhuiyan, Ashikahmed and Pivezhandi, Mohammad and Guo, Zhishan and Li, Jing and Modekurthy, Venkata Prashant and Saifullah, Abusayeed

**Venue**: Leibniz international proceedings in informatics

---

## Key Points

- Real-time scheduling of parallel DAG tasks on clustered multicore platforms minimizing typical-case CPU energy
- Dynamic Power Management (DPM) policy to reduce static power consumption (up to 50% of total power)
- Dual execution time estimates: typical-case (lo-criticality) and worst-case (hi-criticality) for each task
- Hungarian Algorithm-based task-to-cluster partitioning for multi-speed clustered platforms
- 29.23% minimum power consumption reduction vs state-of-the-art MCFS_Li [39] (simulations)
- Up to 30% energy savings on Intel Xeon 2680 v3 12-core platform (experiments)
- **Mohammad Pivezhandi: Co-author** (2nd of 6 authors, Wayne State University)

## Abstract

The rigid timing requirement of real-time applications biases the analysis to focus on the worst-case performances. Such a focus cannot provide enough information to optimize the system's typical resource and energy consumption. In this work, we study the real-time scheduling of parallel tasks on a multi-speed heterogeneous platform while minimizing their typical-case CPU energy consumption.

Dynamic power management (DPM) policy is integrated to determine the minimum number of cores required for each task while guaranteeing worst-case execution requirements (under all circumstances). A Hungarian Algorithm-based task partitioning technique is proposed for clustered multi-core platforms, where all cores within the same cluster run at the same speed at any time, while different clusters may run at different speeds.

To our knowledge, this is the first work aiming to minimize typical-case CPU energy consumption (while ensuring the worst-case timing correctness for all tasks under any execution condition) through DPM for parallel tasks in a clustered platform. We demonstrate the effectiveness of the proposed approach with existing power management techniques using experimental results and simulations.

The experimental results conducted on the Intel Xeon 2680 v3 12-core platform show around 7%-30% additional energy savings.

## Contributions

1. **DPM-based energy-aware federated scheduling for dual-speed platforms**: Given both typical and worst-case execution time estimations, determines required minimum number of low-speed processors to minimize typical CPU energy consumption while guaranteeing worst-case timing correctness for all tasks
2. **Energy-efficient task-cluster partitioning for multi-speed clustered platforms**: Hungarian Algorithm-based approach for task-to-cluster allocation minimizing energy consumption without violating schedulability guarantees
3. **Three task categories for core allocation**: Category 0 (light tasks on low-speed cores), Category 1 (CN_i/sL > CO_i/sH - LO_i/sH), Category 2 (CN_i/sL ≤ CO_i/sH - LO_i/sH with virtual deadlines)
4. **Comprehensive simulation evaluation**: Schedulability ratio and power consumption analysis for different speed settings (sL = 0.45 to 0.6), demonstrating minimum 29.23% power savings vs MCFS_Li baseline
5. **Real-time platform experiments on Intel Xeon 2680 v3**: Proof-of-concept using Barcelona OpenMP Tasks Suite (BOTS) benchmark with 43 DAG tasks, achieving up to 30% energy savings compared to Linux governors
6. **First work combining typical-case energy optimization with worst-case guarantees for parallel DAG tasks**: Unlike existing works that optimize for WCET (rare events), this approach optimizes for typical-case scenarios while ensuring all tasks receive full-service guarantees under any circumstances

## Methodology

**Problem Statement**: Minimize typical-case CPU energy consumption for sporadic parallel DAG tasks on clustered multicore platforms while guaranteeing worst-case real-time schedulability.

**Task Model**:
Each task τ_i ∈ τ is characterized by 5-tuple (CN_i, CO_i, LN_i, LO_i, T_i):
- **CN_i**: Typical-case execution time (lo-criticality)
- **CO_i**: Overload execution time (hi-criticality, WCET)
- **LN_i**: Typical critical-path length (lo-criticality)
- **LO_i**: Overload critical-path length (hi-criticality)
- **T_i**: Period (implicit deadline D_i = T_i)

**Utilization**:
- Typical utilization: u^N_i = CN_i / T_i
- Overload utilization: u^O_i = CO_i / T_i

**System Behavior**: Tasks start execution using typical execution budget. If a job's total work or critical-path length exceeds typical estimates, the job executes according to hi-criticality budget. Once a job finishes overload execution, the next job starts with typical execution requirement.

**Power/Energy Model**:
P(s) = P_sta + P_dyn(s) = β + αs^γ   (Equation 1)

Where:
- P_sta (β): Static/leakage power consumption
- P_dyn(s) (αs^γ): Dynamic power consumption (frequency-dependent)
- α: Effective switching capacitance (α > 0)
- γ: Hardware parameter (ranges [2, 3])
- s: Main frequency (speed) of processor

**Energy consumption for workload C on speed-s processor**:
E(C, s) = (β + αs^γ) × (C/s) = βC/s + αCs^(γ-1)

**Dynamic Power Management (DPM)**:
- Idle intervals ≥ break-even time → processor enters low-power sleep mode
- Reduces static energy consumption by powering down CPU components (cores, caches, TLB)
- Strategy: Allocate low-speed cores to all tasks, leaving high-speed cores idle
- High-speed cores used only when jobs exceed typical workload estimates

**Platform Model**:
- Intel Xeon 2680 V3 homogeneous multi-core (12 cores total)
- ξ clusters (each with n cores)
- All cores in same cluster execute at same speed
- Different clusters can operate at different speeds
- Maximum speed: s^L (low-speed clusters), s^H (high-speed clusters), where s^L ≤ s^H
- Core sleep: Individual cores can be put to sleep (processor clock off)
- Cluster deep sleep: Entire cluster can enter deep sleep (processor + L2 clock off)

**Task Categories**:

**Category 0**: CO_i ≤ s^L × D_i (Light tasks)
- Workload C^O_i / s^L and deadline D_i
- Low utilization even on low-speed cores
- Scheduled using traditional multiprocessor scheduling (partitioned EDF)

**Category 1**: CO_i > s^L × D_i and CN_i/s^L > CO_i/s^H - LO_i/s^H
- Allocate m^L_i low-speed cores for both lo- and hi-criticality modes
- Core allocation formula:
  m^L_i = ⌈(CO_i - LO_i) / (s^L × D_i - LO_i)⌉
- Based on makespan analysis: (CO_i - LO_i)/m + LO_i ≤ D_i (on speed s^L)

**Category 2**: CO_i/(s^L × D_i) > 1 and CN_i/s^L ≤ CO_i/s^H - LO_i/s^H
- Allocate ⌈(CO_i - LO_i)/(s^L × D_i - LO_i)⌉ low-speed cores if sufficient available
- Otherwise, allocate fewer low-speed cores (m^L_i) and set virtual deadline D'_i
- Virtual deadline: D'_i = CN_i / (m^L_i × s^L)
- Reduced low-speed core allocation:
  m^L_i = ⌈CN_i/s^L / (D_i - LO_i/s^H - (CO_i - CN_i - LO_i)/(m^H_i × s^H))⌉
- Allocate m^H_i high-speed cores during hi-criticality mode:
  m^H_i = max{m^L_i × s^L/s^H, ⌈(CO_i - m^L_i × D'_i × s^L - LO_i) / ((D_i - D'_i) × s^H - LO_i)⌉}

**Algorithm 1: greedyAlloc(τ) - Dual-Speed Platform**:
1. Classify tasks into Category 0, 1, 2
2. Allocate low-speed cores to Category 0 and Category 1 tasks
3. Calculate remaining low-speed cores m̃^L
4. For Category 2 tasks in τ̃ (unscheduled):
   - Set m^L_i = ⌈(CO_i - LO_i)/(D_i × s^L - LO_i)⌉ and m^H_i = 0
5. If insufficient low-speed cores:
   - Calculate relative core saving ratio: (m^L_i - m̄^L_i)/(m̄^H_i - m^H_i)
   - Pick task with highest core saving ratio
   - Allocate additional high-speed cores to that task
   - Reduce low-speed cores allocated to that task
6. Repeat until all tasks scheduled or failure
Time complexity: O(K) for K tasks

**Algorithm 2: createTable(τ_heavy, K) - Energy Consumption Table**:
1. Create table E[size(τ_heavy)][size(K)] storing energy consumption
2. For each heavy DAG task τ_i and each sub-cluster K^n_m:
   - Check if sub-cluster satisfies minimum allocation requirement
   - If yes: Calculate E^y_x (energy consumed by task x in sub-cluster y)
   - If no: Set E[x][y] = ∞ (arbitrarily large value)
3. Return energy consumption table E

**Algorithm 3: taskToClusterAllocation(τ_heavy, K) - Hungarian Assignment**:
1. Determine number of sub-clusters M_K per cluster:
   M_K = {⌊M/m^K⌋ + 1, if M mod m^K ≠ 0; M/m^K, otherwise}
   where m^K = max{m^K_i} for all tasks τ_i ∈ τ
2. Create energy consumption table using Algorithm 2
3. If |τ_heavy| < |K|, add dummy rows with ∞ values (make table square)
4. Solve using Hungarian Algorithm [36] for optimal assignment
5. For each allocated task:
   - Remove task from τ_heavy
   - Update sub-cluster size if underutilized
6. If 0 < size(τ_heavy) < original size: Repeat allocation
7. If size(τ_heavy) == 0: Return optimal allocation
8. Else: Return FAILURE

**Sub-Cluster Partitioning**:
- Each cluster K_K divided into M_K sub-clusters
- Each sub-cluster contains m^K cores (if M mod m^K = 0)
- Otherwise: First M_K - 1 sub-clusters contain m^K cores, remaining contains M - (M_K - 1) × m^K cores
- Sub-cluster notation: K^n_m (nth sub-cluster inside mth cluster)

**Federated Scheduling**:
- Heavy tasks (utilization ≥ 1): Receive dedicated cores (m_i cores per task)
- Light tasks (utilization < 1): Share remaining cores using multiprocessor scheduler
- Runtime scheduling: Greedy/work-conserving parallel scheduler for heavy tasks

**Virtual Deadline Concept**:
- Proposed by Baruah et al. [6] for mixed-criticality systems
- High-criticality tasks assigned virtual deadline D'_i < D_i (higher priority)
- Ensures sufficient slack for overload workload completion after mode switch

## Results

**Simulation Setup**:
- Task sets: 200-1000 random task sets
- Tasks per set: 5-10
- Deadline: D_i = x × CO_i, where x ∈ [0.9, 1.0]
- Ratio of typical to overload execution: 1 ≤ Z_up/Z_down ≤ 8
- Low-speed cluster speed s^L: 0.45 to 0.6 (normalized to max speed)
- Power model validation (Table 1): Error rate -3.7% to 2.67% vs experimental data [32]

**Schedulability Ratio (Figure 1a)**:
| s^L Value | Schedulability Ratio (1000 task sets) |
|-----------|---------------------------------------|
| 0.45 | ~40% |
| 0.50 | ~70% |
| 0.55 | ~95% |
| 0.60 | ~100% |

- Schedulability ratio directly proportional to s^L
- Not strongly correlated with task-set size
- Higher low-speed cluster speed → better schedulability

**Power Consumption (Figure 1b)**:
| s^L Value | Average Power (watts) |
|-----------|-----------------------|
| 0.45 | ~18 W |
| 0.50 | ~19 W |
| 0.55 | ~20 W |
| 0.60 | ~22 W |

- Power consumption increases with higher s^L
- Range: 18-22 watts across different s^L values
- Trade-off: Higher schedulability vs higher power

**Comparison with MCFS_Li Baseline (Figure 2)**:
| Task Sets | Our Approach (watts) | MCFS_Li (watts) | Power Saving |
|-----------|----------------------|-----------------|--------------|
| 200 | ~19 W | ~27 W | 29.63% |
| 400 | ~19 W | ~27 W | 29.63% |
| 600 | ~19 W | ~26 W | 26.92% |
| 800 | ~19 W | ~26 W | 26.92% |
| 1000 | ~19 W | ~26 W | 26.92% |

- Minimum 29.23% less power consumption than MCFS_Li [39]
- MCFS_Li: All cores execute at maximum speed s^H
- Our approach: Utilizes low-speed cores (s^L = 0.55) when possible

**Experimental Platform**:
- **Hardware**: Intel Xeon 2680 v3 (12-core platform)
- **Operating System**: Ubuntu 20.04
- **Scheduler**: Completely Fair Scheduler (CFS) from Linux kernel 2.6.23 [49]
- **Isolated Cores**: 11 cores (6 low-speed, 5 high-speed)
- **Frequency Range**: 1.2 GHz (min) to 2.5 GHz (max), 20µs transition time
- **Tools**: perf profiler, RAPL (Reduced Average Power Limit) for energy monitoring [30]
- **Benchmark**: Barcelona OpenMP Tasks Suite (BOTS) [21] with 43 DAG tasks
- **Power Control**: ACPI (Advanced Configuration and Power Interface)
- **Configuration**: Hyper-threading OFF, Turbo mode OFF, Intel p-state/c-state OFF

**Execution Time Estimation**:
- Typical execution time (CN_i): Average of 20 iterations on single core
- Overload execution time (CO_i): Maximum of 20 iterations on single core
- Critical-path length: Determined when all cores assigned to DAG

**Correlation between Response Time and Energy (Figure 3)**:
- Intel Xeon 2680 v3: Energy consumption decreases as number of cores increases
- Intel Core i7: Similar trend observed
- Reason: Increased parallelism reduces execution time, amortizes static power overhead
- Single core allocation: Highest energy consumption (longest time, inactive cores waste energy)

**Energy Consumption vs Number of Cores (Figure 3a/3b)**:
| Cores | Energy (Joules) - Task Example |
|-------|--------------------------------|
| 1 | ~1×10^7 J |
| 3 | ~5×10^6 J |
| 6 | ~3×10^6 J |
| 9 | ~2.5×10^6 J |
| 11 | ~2×10^6 J |

**Virtual Deadline and Makespan (Figure 4)**:
- Virtual Deadline: ~0.55 sec
- Typical Execution Budget: ~0.65 sec
- Typical Critical Path: ~0.35 sec
- Overload Execution Budget: ~0.7 sec
- Overload Critical Path: ~0.45 sec
- Virtual deadline calculated using: VD < L + (C - L)/m

**Task Category Distribution (Figure 5)**:
- Example with s^L = 0.1 (low-speed) and s^H = 0.7 (high-speed):
  - Task 2, 9, 42, 43: Category 1 (low-speed cores only)
  - Task 10: Category 2 (additional high-speed cores required)
- With s^L = 0.8:
  - All tasks schedulable with low-speed cores only (higher energy cost)

**Energy Comparison with Linux Governors (Figure 6)**:
| Task ID | Federated (Joules) | Conservative | Performance | Powersave | Schedutil |
|---------|-------------------|--------------|-------------|-----------|-----------|
| 10 | ~120 J | ~130 J | ~130 J | ~135 J | ~135 J |
| 18 | ~175 J | ~250 J | ~250 J | ~250 J | ~250 J |
| 36 | ~190 J | ~250 J | ~250 J | ~260 J | ~260 J |

- Task 18: 30% better energy efficiency than Linux governors
- Advantage: Considers schedulability conditions (governors cannot)
- Response time similar or better across all tasks

**Schedulability Analysis (Figure 7a)**:
- 43 BOTS benchmark tasks evaluated
- Search table: 192 elements (task × sub-cluster combinations)
- Result: At least one schedulable configuration for each task
- **100% schedulability** maintained across all evaluated tasks
- Virtual deadline and schedulability conditions always satisfied

**Aggregated Energy Consumption (Figure 7b)**:
| Method | Energy Consumption (Joules) |
|--------|----------------------------|
| Federated | ~2000 J |
| Conservative | ~2050 J |
| Ondemand | ~2050 J |
| Performance | ~2100 J |
| Powersave | ~2100 J |
| Schedutil | ~2100 J |

- **~7% improvement** in aggregated energy consumption over all 43 tasks
- Federated approach consistently outperforms Linux governors
- Benefits from task-aware cluster allocation and DPM policy

**Key Experimental Findings**:
1. Energy consumption decreases with increased parallelism (more cores)
2. Single-core allocation = worst energy efficiency (long execution + idle core waste)
3. Virtual deadline mechanism successfully balances typical and worst-case requirements
4. Category 2 tasks benefit most from high-speed core allocation during overload
5. Hungarian Algorithm finds optimal energy-minimizing task-cluster assignments
6. 100% schedulability maintained across all benchmark tasks
7. Up to 30% energy savings on individual tasks vs Linux governors
8. 7% aggregated energy improvement across entire benchmark suite

## Notes

**Research Context**:
- **Co-authored publication** by Mohammad Pivezhandi (2023)
- **Affiliation**: Mohammad Pivezhandi - Department of Computer Science, Wayne State University, Detroit, MI, USA
- **Author Position**: 2nd of 6 authors
- **All Authors**: Ashikahmed Bhuiyan (West Chester University), Mohammad Pivezhandi (Wayne State), Zhishan Guo (NC State), Jing Li (NJIT), Venkata Prashant Modekurthy (UNLV), Abusayeed Saifullah (Wayne State)
- **Conference**: ECRTS 2023 (35th Euromicro Conference on Real-Time Systems)
- **Publication**: LIPIcs Volume 262, Article No. 8, pp. 8:1-8:24
- **Editor**: Alessandro V. Papadopoulos
- **DOI**: 10.4230/LIPIcs.ECRTS.2023.8
- **License**: Creative Commons CC-BY 4.0

**Funding Acknowledgments**:
- Ashikahmed Bhuiyan: Provost's Research Grant, West Chester University
- Zhishan Guo: NSF CCF-2028481, Startup Grant at NC State University
- Jing Li: NSF CNS-1948457
- Venkata Prashant Modekurthy: UNLV Troesh Center, CNS-2211640
- Abusayeed Saifullah: CNS-2301757, CAREER-2306486, CNS-2306745

**Application Domains**:
- Embedded systems (mobile phones, tablets, drones, CNC machines)
- Self-driving cars (computation-intensive with stringent timing requirements)
- Real-time parallel task scheduling
- Energy-constrained battery-powered systems
- Energy harvesting devices

**Technical Innovation**:
- **First work combining typical-case energy optimization with worst-case guarantees**: Unlike prior work that optimizes for WCET (rare events), this approach optimizes for typical-case scenarios while ensuring worst-case timing correctness
- **DPM policy for parallel DAG tasks**: Static power reduction by utilizing idle intervals (50% or more of total power consumption [33])
- **Dual execution time estimates**: System designers provide typical and worst-case estimates, enabling energy minimization under typical scenarios with worst-case guarantees
- **Hungarian Algorithm for task-cluster allocation**: Polynomial time optimal assignment minimizing total energy consumption
- **Virtual deadline mechanism**: Ensures sufficient slack for overload workload after mode switch
- **Three task categories**: Systematic classification enabling targeted core allocation strategies

**Comparison with Related Work**:
- **MCFS_Li [39]**: Mixed-criticality federated scheduling for parallel real-time tasks (baseline)
  - Limitation: All cores execute at maximum speed s^H
  - Our improvement: 29.23% power savings by utilizing low-speed cores
- **Energy-aware DAG scheduling [10, 26]**: DVFS-based approaches
  - Limitation: Not effective for static power (50%+ of total), simplified models, table-driven
  - Our improvement: DPM policy, clustered platform, typical-case optimization
- **MC task scheduling [1, 5, 7, 12-14, 22, 27, 39]**: Mixed-criticality frameworks
  - Limitation: No energy-aware scheduling, execute up to WCET at criticality levels
  - Our improvement: Typical-case energy optimization with full-service guarantees
- **Energy-efficient clustered scheduling [11, 20, 25, 41, 45]**: Recent clustered platform work
  - Limitation: Focus on worst-case energy, not typical-case
  - Our improvement: Typical-case energy minimization with worst-case timing guarantees

**Key Challenges Addressed**:
1. **Non-clairvoyance**: Scheduler unaware of exact task behavior before runtime
2. **Full-service assurance**: All tasks receive guarantees under any circumstances (not degraded service)
3. **Intra-task parallelism**: Existing MC energy work did not consider parallel tasks
4. **Clustered platform constraints**: All cores in same cluster must execute at same speed
5. **Static power consumption**: DVFS ineffective (50%+ of total power), requires DPM approach

**Design Trade-offs**:
- **Low-speed vs High-speed cores**: Lower energy (low-speed) vs higher schedulability (high-speed)
- **Virtual deadline tightness**: Earlier deadlines (more slack for overload) vs reduced typical-case utilization
- **Number of clusters**: More clusters (finer granularity) vs complexity and overhead
- **Category 2 task allocation**: Fewer low-speed cores + high-speed backup vs all low-speed cores

**Power Model Validation (Table 1)**:
| Frequency | Experimental P_exp | Equation P_equ | Error Rate |
|-----------|-------------------|----------------|------------|
| 0.24 GHz | 0.54 W | 0.52 W | -3.7% |
| 0.46 GHz | 0.70 W | 0.67 W | -4.28% |
| 0.68 GHz | 1.04 W | 1.05 W | 0.96% |
| 0.84 GHz | 1.5 W | 1.54 W | 2.67% |
| 0.92 GHz | 1.96 W | 1.88 W | -4.07% |
| 1.02 GHz | 2.30 W | 2.35 W | 2.17% |

- Power model P(s) = β + αs^γ highly realistic
- Validation against experimental data from [32]
- Error rate: -4.28% to 2.67% (very accurate)

**Limitations**:
- Single DAG profiling due to Linux profiler limitations (ring buffers, socket hardware registers)
- Break-even time for DPM requires platform-specific calibration
- Execution time estimation requires dual runs (typical and worst-case)
- BOTS benchmark limited to OpenMP tasks (not all DAG models)
- Xeon 2680 v3 platform-specific (not tested on ARM big.LITTLE)

**Future Directions** (from paper):
1. **Multiple execution thresholds**: Beyond two thresholds, with probability information
2. **Other power components**: Cache misses, context switches, bus accesses, I/O usage
3. **Extended platform evaluation**: ARM big.LITTLE, 12th gen Intel Core i7 (4 high-speed + 8 low-speed cores)
4. **Probabilistic workload models**: Distribution of execution times beyond typical/worst-case
5. **Adaptive threshold adjustment**: Runtime learning of typical vs overload boundaries

**Connection to Research Arc**:
- Follows bashir2022energy (2022, co-authored thermal-aware scheduling)
- Precedes pivezhandi2023toward (2023, master's thesis on RL for resource scheduling)
- Continues Wayne State University collaboration (Saifullah research group)
- Extends energy-aware scheduling from thermal management to DPM policy
- Establishes expertise in parallel DAG task scheduling and energy optimization
- Foundation for later mixed-criticality and real-time scheduling work

**Related Work Summary**:
- **Sequential task energy-aware**: [15, 17, 19, 35, 47, 50, 51] - not applicable to parallel tasks
- **Parallel task scheduling**: [1, 5, 8, 9, 23, 55, 56] - no energy awareness
- **Energy-aware parallel**: [40, 52, 61, 62] - fixed threads, gang model, AND/OR graphs
- **DAG energy-aware**: [10, 26] - simplified model, table-driven, not clustered
- **Clustered platform**: [11, 20, 25, 41, 45] - recent work, but worst-case focus
- **Mixed-criticality**: [1, 5, 7, 12-14, 22, 27, 39] - no energy-aware scheduling for DAG

**Experimental Tools**:
- **perf**: Linux profiler for performance analysis
- **RAPL**: Reduced Average Power Limit (hardware energy counter) [30]
- **cpuset**: Core isolation and task pinning (cset command)
- **cpufrequtils**: CPU frequency control tool
- **ACPI-Freq**: Advanced Configuration and Power Interface for DVFS
- **BOTS**: Barcelona OpenMP Tasks Suite [21] (43 DAG benchmarks)

**Implementation Details**:
- **Language**: Python (task-cluster allocation algorithm)
- **Kernel Configuration**: isolcpus for 11 of 12 cores (isolated from OS)
- **Frequency Control**: scaling_max_freq parameter, Turbo mode OFF
- **Hyper-threading**: Disabled (avoid unwanted frequency adjustments)
- **Governor Configuration**: Userspace mode with ACPI (software control)
- **Benchmark Execution**: Periodic execution via cset with default nice value
- **Energy Monitoring**: RAPL during execution of each DAG task

**Platform Specifications**:
- **Processor**: Intel Xeon 2680 v3 (Haswell architecture)
- **Total Cores**: 12 cores
- **Isolated Cores**: 11 (6 low-speed cluster, 5 high-speed cluster)
- **Frequency Range**: 1.2 GHz - 2.5 GHz
- **Frequency Transition**: 20 microseconds
- **Cache**: L2 cache shared within cluster
- **Power Management**: Per-core sleep, per-cluster deep sleep
- **Operating System**: Ubuntu 20.04, Linux kernel 2.6.23+ (CFS scheduler)

**Benchmark Characteristics**:
- **BOTS Suite**: 43 OpenMP tasks with DAG structure
- **Task Directive**: Creates nodes within DAG
- **Depend/Taskwait Directive**: Creates edges (dependencies)
- **Node Scheduling**: Greedy algorithm from Li et al. [37]
- **Data Format**: AEDAT 2.0 compatible (though not event-based cameras)
- **Execution Modes**: Typical (CN_i) and Overload (CO_i) estimates from 20 iterations

**Performance Highlights**:
- **Simulation**: 29.23% minimum power reduction vs MCFS_Li baseline
- **Experiments**: Up to 30% energy savings vs Linux governors (individual tasks)
- **Aggregated**: 7% energy improvement across all 43 BOTS benchmarks
- **Schedulability**: 100% across all configurations tested
- **Response Time**: Comparable or better than Linux governors
- **Typical Case Optimization**: First work optimizing typical-case energy (not worst-case)

## Bibdata

```bibtex
% Row 7
@article{bhuiyan2023precise,
  title={Precise scheduling of dag tasks with dynamic power management},
  author={Bhuiyan, Ashikahmed},
  journal={Leibniz international proceedings in informatics},
  volume={262},
  year={2023}
}
```
