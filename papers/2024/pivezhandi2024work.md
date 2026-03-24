# Work-In-Progress: Energy and Thermal-Aware Scheduling based on HMARL for OpenMP DAG Workloads

**Citation Key**: `pivezhandi2024work`

**Year**: 2024

**Type**: inproceedings

**Authors**: Pivezhandi, Mohammad and Jain, Aakriti and Saifullah, Abusayeed and Jannesari, Ali

**Venue**: 2024 IEEE Real-Time Systems Symposium (RTSS)

---

## Key Points

- Hierarchical Multi-Agent Reinforcement Learning (HMARL) for energy and thermal-aware scheduling of OpenMP DAG workloads on multicore platforms
- Two-agent cooperative framework: Profiler Agent (selects cores + frequency based on makespan, branch misses, etc.) and Temperature Agent (prioritizes cores based on per-core temperature)
- D3QN (Double Deep Dueling Q-Network) off-policy RL reduces sample requirements and mitigates overestimation issues compared to on-policy methods
- Action space decomposition from exponential complexity (k × 2^m for m cores, k frequencies) to linear complexity (m² + m×k + 1) using HMARL
- N-Queens benchmark experiments on Intel Core i7 (4 cores) and Intel Xeon 2680 v2 (12 cores) show increasing cores leads to higher branch misses, makespan, and energy consumption for tied tasks
- Work-In-Progress paper (4 pages, RTSS 2024 WIP track): Presents approach, motivation, and preliminary design without full experimental evaluation
- **Multi-institution collaboration**: Mohammad Pivezhandi, Aakriti Jain, Abusayeed Saifullah (Wayne State University), Ali Jannesari (Iowa State University)

## Abstract

With technological advancement, the leakage power of multicore platforms which correlates exponentially with chip temperature becomes more than dynamic power. Energy-aware hardware solutions use dynamic voltage and frequency scaling (DVFS) to avoid overheating in aggressive performance-boosting scenarios, and software solutions assign high-utilization tasks to different core configurations to conserve processor package power.

Since the current energy-aware heuristics are not based on core-by-core frequency monitoring, they do not address overheating when some cores are more active than others. Also, assigning tasks to cores without comprehensive profiling does not address irregular task execution. Static leakage power increases from roughly 22% to over 63% of the total power with a halving of the technology scale [18], making thermal management critical for modern multicore systems.

The current hardware solutions for processors equipped with DVFS do not adequately address the need for per-core frequency tracking and adjustment based on thermal behavior [7]. Similarly, existing software solutions fail to effectively handle runtime features like branch misses and memory accesses in parallel tasks, resulting in irregularities and unpredictable execution times within subtasks of Directed Acyclic Graph (DAG) workloads, leading to inefficient core allocation. The assignment of tasks to cores in available Linux kernel governors [6] and developed policies [4] is agnostic to each core's temperature, often resulting in high-utilization tasks being allocated to hot cores.

In this article, we investigate potential of applying an online reinforcement learning method to efficiently allocate tasks based on their profiling data, including makespan, to appropriate core combinations according to their temperature status, aiming to reduce energy consumption. We propose using a hierarchical multi-agent reinforcement learning (HMARL) approach, where one agent selects cores and frequency scaling as a function of profiler results, while another agent selects core combinations as a function of temperature sensor results.

## Contributions

1. **Hierarchical Multi-Agent RL (HMARL) framework for energy-thermal-aware scheduling**: Novel two-agent cooperative approach where Profiler Agent selects cores and frequency based on profiling data (makespan, branch misses, CPU cycles, etc.) and Temperature Agent prioritizes cores based on per-core temperature sensors, addressing limitations of current DVFS heuristics

2. **Action space decomposition for scalability**: Reduces exponential action space complexity from k × 2^m (k frequencies, m cores) to linear complexity m² + m×k + 1 by decomposing into three sub-spaces: core prioritization (m² dimensions), frequency assignment (m×k dimensions), and total core allocation (1 dimension), enabling scalability to many-core platforms

3. **Off-policy D3QN (Double Deep Dueling Q-Network) for sample efficiency**: Employs off-policy RL to reduce sample requirements compared to on-policy methods, with D3QN mitigating overestimation issues stemming from training data, addressing challenge of time-consuming experiments and computation overhead

4. **Empirical analysis of inefficient task-to-core allocations**: Comprehensive evaluation on Intel Core i7 (4 cores) and Xeon 2680 v2 (12 cores) showing increasing core count leads to higher branch misses, makespan, and energy consumption for tied tasks (N-Queens benchmark), motivating temperature-aware and profiling-aware scheduling

5. **Nine scheduling scenarios illustrating HMARL benefits**: Detailed comparison across scenarios 1-9 (Figure 2) demonstrating how HMARL approach ensures predictable makespan, conserves energy, creates idle cores through efficient allocation, and prevents thermal throttling by dynamically swapping performance/powersave modes among cores based on temperature

## Methodology

**Problem Formulation**:

**Challenge 1: Static Leakage Power Dominance**:
- Static leakage power increases from ~22% to >63% of total power with halving of technology scale [18]
- Leakage power correlates exponentially with chip temperature
- Hardware DVFS solutions scale voltages/frequencies based on temperature limits and core utilization
- Software solutions assign high-utilization tasks to different core configurations

**Challenge 2: Inadequate Per-Core Thermal Management**:
- Current DVFS hardware lacks per-core frequency tracking and adjustment based on thermal behavior
- Software solutions (Linux governors, existing policies) agnostic to per-core temperature
- High-utilization tasks allocated to hot cores, exacerbating thermal issues

**Challenge 3: Irregular Task Execution in OpenMP DAGs**:
- Runtime features (branch misses, memory accesses) cause irregularities
- Unpredictable execution times within DAG subtasks
- Inefficient core allocation without comprehensive profiling

**Challenge 4: Scalability and Generalizability**:
- Existing energy-aware and thermal-aware schedulers limited scalability as cores and frequency levels increase
- Lack generalizability across processor types (CPU, GPU) and vendors (Intel, AMD, ARM)
- Learning-based algorithms require many samples (longer training times)
- Training/inference significant computation overhead (offline functionality only)
- Client-server setup required for runtime decision-making

**Challenge 5: High-Dimensional Action Spaces**:
- RL scalability limited by high-dimensional action spaces
- Most studies focus on RL algorithms with demanding sampling, overlooking model/reward specifications
- Coarse frequency scaling hinders accurate energy-aware scheduling

**Approach: HMARL with Cooperative Agents**:

Our solution uses hierarchical multi-agent reinforcement learning (HMARL) with two cooperative agents known as Joint Action Learners (JAL) [9], working together when taking joint actions based on observations:

1. **Profiler Agent**: Decides frequency and number of cores based on profiling data
2. **Temperature Agent**: Gives priority to cores based on per-core temperature to select core combinations

Both agents use D3QN (Double Deep Dueling Q-Network) off-policy RL, which does not necessitate learning the transition policy, reducing required samples contrary to on-policy RL. D3QN mitigates overestimation issues stemming from training data.

**State Observations**:

**1. Profiler State (S_p)**: Obtained from Linux kernel's perf (performance analysis tool) and RAPL feature (msr - model-specific register):
- Branch misses (Bf_i)
- Total branches (Br_i)
- CPU cycles (Cc_i)
- Average frequency (Fr_i)
- Total instructions (Ic_i)
- Total context switches (Cs_i)
- Average utilization (Ut_i)
- Makespan (Ex_i)
- Energy consumption (En_i)

**2. Temperature State (S_θ)**: Obtained from hardware thermal sensors:
- Absolute temperature readings: θ_0, θ_1, ..., θ_n
- Temperature changes: Δθ_0, Δθ_1, Δθ_2, ..., Δθ_n

**Action Space Complexity Analysis**:

**Observation 1**: Without core combination selection, action space has linear complexity m × k (m cores, k discrete frequency levels f_1, f_2, ..., f_k).

However, frequency assignment to core combinations follows binomial coefficient. For l cores selected from m homogeneous cores, there are (m choose l) possible combinations.

**Example**: 3-core processor: C = {(c_0), (c_1), (c_2), (c_0,c_1), (c_0,c_2), (c_1,c_2), (c_0,c_1,c_2)} = 7 combinations

Total state options for each frequency range: 2^m when summing over all combinations. In extreme cases, k × 2^m action combinations must be evaluated.

**Formula**: Total actions a_T = Σ^m_{i=1} (m choose i)(k choose i)

**Example 1**: 3-core processor, k=10 frequency levels:
```
a_T = (3 choose 1)(10 choose 1) + (3 choose 2)(10 choose 2) + (3 choose 3)(10 choose 3)
    = 3×10 + 3×45 + 1×120
    = 30 + 135 + 120 = 285 actions
```

For 12-core processor (Xeon 2680 v2), this becomes 356,716 actions - impractical for single-agent Q-learning.

**HMARL Action Space Decomposition**:

Individual agents manage different action categories, decomposing exponential space into linear space:

1. **Core Selection based on Temperature**: Action space dimension = m² (each core ranked 1 to m)
2. **Frequency Assignment based on Profiling**: Action space dimension = m × k (assign frequency to each core)
3. **Total Core Allocation**: Action space dimension = 1 (single integer specifying cores for DAG task)

**Total HMARL Action Space**: m² + m×k + 1 (linear complexity)

**Example**: For 3-core processor with k=10:
- Single-agent: 285 actions
- HMARL: 3² + 3×10 + 1 = 9 + 30 + 1 = 40 actions (7.1× reduction)

For 12-core processor:
- Single-agent: 356,716 actions
- HMARL: 12² + 12×10 + 1 = 144 + 120 + 1 = 265 actions (1,346× reduction!)

**Empirical Analysis: Inefficiency of Current Task-to-Core Allocations**:

**Section II: Temperature-Aware Task-to-Core Assignment Strategy**:

We explore temperature-aware allocation using OpenMP workloads as DAG benchmarks.

**Figure 1: N-Queens Benchmark Evaluation**:

**Intel Xeon 2680 v2 (12 cores)**:
- Branch Misses: Tied tasks show highest branch misses (~6×10^9 vs ~2×10^9 for manual)
- Makespan: Tied tasks increase makespan from ~200s (manual) to ~600s (tied) when using 12 cores
- Energy Consumption: Tied tasks consume ~2.5×10^10 J vs ~1×10^10 J (manual) at 12 cores

**Intel Core i7 (4 cores)**:
- Branch Misses: Tied tasks show ~1.5×10^9 vs ~0.5×10^9 (manual)
- Makespan: Tied tasks increase makespan from ~50s to ~125s
- Energy Consumption: Tied tasks consume ~1×10^9 J vs ~0.4×10^9 J

**Key Finding**: Increasing number of cores leads to higher energy consumption and makespan, mainly due to branch misses. This motivates temperature-aware and profiling-aware allocation.

**Figure 2: Nine Scheduling Scenarios** (4-core processor simulation):

**Scenarios 1-2: Performance Mode (P)**:
- Blue tasks: High-frequency (performance governor)
- Scenario 1: Tied task execution with predictable execution time for τ*_{i,1}, risking thermal throttling
- Scenario 2: Tied task execution with unpredictability in execution time

**Scenarios 3-4: Powersave Mode (S)**:
- Green tasks: Low-frequency (powersave governor)
- Scenario 3: Predictable execution times
- Scenario 4: Unpredictable execution times

**Scenarios 5-6: Multi-Speed Policy (Mixed Frequencies)**:
- Mixed core frequencies (performance + powersave)
- In all cases: Ignoring workload characteristics leads to unpredictable makespan and potential thermal throttling

**Scenarios 7-8-9: Proposed HMARL Approach**:

**Scenario 7 (First Round)**:
- Profiler agent assigns τ_{i,2} to performance cores to prevent makespan extension
- Temperature agent adjusts core assignment priorities

**Scenario 8 (Second Round)**:
- Temperature agent swaps performance and powersave modes among cores based on thermal status

**Scenario 9 (Third Round - Key Innovation)**:
- Temperature agent gives high priority to cold cores c_2 and c_3 to run in performance mode
- Sets c_0 and c_1 to minimum frequency to cool down
- Profiler agent allocates only ONE core to tied task to make it predictable in makespan
- **Result**: Predictable behavior for τ*_{i,1}, achieving lower DAG makespan, conserving energy, creating idle cores through efficient allocation

**Algorithm 1: Proposed Method based on HMARL**:

**Inputs**:
- DAG: τ_i
- Environment states: s_p (profiler), s_θ (temperature)

**Procedure**:

```
1: Input DAG: τ_i
2: Environment states: s_p, s_θ
3: a_p ← ProfilerAgentAct(κ_g, s_p)  # profiler agent inference
4: a_θ ← TemperatureAgentAct(κ_g, s_θ)  # temperature agent inference
5: a_o ← SelectCores(a_p, a_θ)  # combining actions
6: s'_p, s'_θ ← Environment(κ_g, a_o)  # feeding environment configurations
7: κ_G, A^t_p, A^t_θ, S^t_p, S^t_θ ← SampleBuffer(mem_cntr)  # getting samples to train reward function
8: r_p, r_θ, d_p, d_θ ← RewardStep(κ_g, a_p, a_θ, s'_p, s'_θ, κ_G, A^t_p, A^t_θ, S^t_p, S^t_θ)  # getting reward from reward function
9: StoreTransition(s_p, s_θ, s'_p, s'_θ, a_p, a_θ, r_p, r_θ, d_p, d_θ, mem_cntr++)  # store transitions
10: S'_p, S'_θ, A_p, A_θ, R_p, R_θ, S_p, S_θ, D_p, D_θ ← SampleBuffer(BS)  # getting samples to train agents
11: ProfilerAgentTrain(S'_P, S_p, R_p, A_p, D_p)  # training profiler agent
12: TemperatureAgentTrain(S'_θ, S_θ, R_θ, A_θ, D_θ)  # training temperature agent
```

**Key Steps**:

1. **Lines 3-4**: Profiler agent acts on profiling data (makespan, branch misses), Temperature agent acts on per-core temperature
2. **Line 5**: SelectCores merges actions into a_o, delineating chosen core combinations and frequencies, adjusting priority queue for core priorities and thermal states
3. **Line 6**: Environment invoked with combined action a_o, returns updated profiler and temperature states (s'_p, s'_θ)
4. **Lines 7-8**: Upon surpassing sample threshold (mem_cntr > thr), RewardStep trains reward function to estimate rewards based on anticipated future states
5. **Line 9**: Collected done flag and reward value stored in buffer via StoreTransition
6. **Lines 10-12**: Randomly sampled batch size (BS) of transitions fed to agents to enhance decision-making

**Iterative Learning**: By repeating these steps, algorithm learns optimal policy for selecting core combinations and frequencies that maximize system performance and reduce average temperature.

**Future Work Directions** (Section IV):

1. **Custom Reward Function**: Enhance approach using custom reward function that estimates future states to maximize cumulative rewards
2. **Comprehensive Complexity Analysis**: Provide detailed complexity analysis of HMARL approach
3. **Physical Testbed Implementation**: Implement and assess on physical testbed with extensive simulation
4. **Heterogeneous Platform Extension**: Extend framework to heterogeneous platforms (CPU+GPU)
5. **Advanced RL Techniques**: Explore advanced RL techniques for more complex, higher-dimensional action spaces

## Results

**Note**: This is a Work-In-Progress paper (4 pages, RTSS 2024 WIP track). Full experimental evaluation is planned for future work. The paper presents:
1. Motivation and problem analysis
2. Proposed HMARL framework design
3. Preliminary experimental observations
4. Algorithm pseudocode

**Figure 1: N-Queens Benchmark Preliminary Results**:

**Intel Xeon 2680 v2 (12 cores)**:

| Metric | Serial | Manual | Tied |
|--------|--------|--------|------|
| Branch Misses (5 cores) | ~2×10^9 | ~2×10^9 | ~4×10^9 |
| Branch Misses (10 cores) | ~2×10^9 | ~2×10^9 | ~6×10^9 |
| Makespan (5 cores) | ~200s | ~200s | ~400s |
| Makespan (10 cores) | ~200s | ~200s | ~600s |
| Energy (5 cores) | ~1×10^10 J | ~1×10^10 J | ~1.5×10^10 J |
| Energy (10 cores) | ~1×10^10 J | ~1×10^10 J | ~2.5×10^10 J |

**Intel Core i7 (4 cores)**:

| Metric | Serial | Manual | Tied |
|--------|--------|--------|------|
| Branch Misses (1 core) | ~0.5×10^9 | ~0.5×10^9 | ~0.5×10^9 |
| Branch Misses (3 cores) | ~0.5×10^9 | ~0.75×10^9 | ~1.5×10^9 |
| Makespan (1 core) | ~50s | ~50s | ~50s |
| Makespan (3 cores) | ~50s | ~75s | ~125s |
| Energy (1 core) | ~0.4×10^9 J | ~0.4×10^9 J | ~0.4×10^9 J |
| Energy (3 cores) | ~0.4×10^9 J | ~0.6×10^9 J | ~1×10^9 J |

**Key Observations**:
- **Tied tasks** (unbounded parallel execution) show highest branch misses, makespan, and energy consumption
- **Effect scales with core count**: 12-core Xeon shows more dramatic degradation than 4-core i7
- **Shadow lines** in figures show result variation over 10 iterations, indicating reproducibility

**Interpretation**: Increasing core count for tied tasks leads to:
1. Higher branch misses (poor branch prediction due to parallel execution patterns)
2. Extended makespan (despite parallelism, overhead dominates)
3. Increased energy consumption (inefficient utilization + thermal effects)

This empirical analysis motivates the need for profiling-aware and temperature-aware task-to-core allocation.

**Figure 2: Scenario Analysis**:

Nine scenarios illustrate DAG makespan differences on 4-core architecture:
- Performance governor (P): Blue tasks, high frequency
- Powersave governor (S): Green tasks, low frequency
- PP/PS: Unbounded parallel execution of tied task
- SP/SS: Bounded serial execution
- Numbers: Hypothetical task execution time
- Green dash line: DAG makespan

**Scenarios 1-6**: Demonstrate limitations of fixed-frequency approaches and tied task unpredictability

**Scenarios 7-9 (HMARL)**: Show how profiler agent and temperature agent collaborate to:
- Assign critical tasks (τ_{i,2}) to performance cores
- Swap performance/powersave modes based on per-core temperature
- Allocate only one core to tied tasks for makespan predictability
- Cool down hot cores (c_0, c_1) while utilizing cold cores (c_2, c_3)

**Expected Benefits** (to be validated in future work):
- Lower DAG makespan through predictable execution
- Energy conservation via efficient core-frequency mapping
- Idle core creation through selective allocation
- Prevention of thermal throttling via temperature-aware swapping

## Notes

**Research Context**:
- **Publication Type**: Work-In-Progress (WIP) paper at RTSS 2024 (4 pages, pages 455-458)
- **Venue**: IEEE Real-Time Systems Symposium (RTSS) - premier conference for real-time systems research
- **WIP Track**: Preliminary work presenting approach design, motivation, and initial observations without full experimental evaluation
- **First Author**: Mohammad Pivezhandi (Wayne State University)
- **Co-Authors**: Aakriti Jain (Wayne State), Abusayeed Saifullah (advisor, Wayne State), Ali Jannesari (Iowa State, M.S. thesis co-advisor)
- **Multi-Institution Collaboration**: Wayne State University + Iowa State University

**Connection to Other Work**:

This WIP paper is closely related to and expands upon pivezhandi2023toward (M.S. thesis):
- M.S. thesis (2023): Hierarchical RL for DVFS scheduling, DDDQN-based two-agent system
- This WIP (2024): Extends to OpenMP DAG workloads, explicit temperature-aware scheduling, action space decomposition analysis

**PhD Research Arc**:
- 2023: M.S. thesis on hierarchical multi-agent RL (pivezhandi2023toward)
- 2024: FlowRL for few-shot RL (pivezhandi2024flowrl)
- **2024: This WIP on HMARL for DAG workloads** (extends M.S. thesis to OpenMP DAGs)
- 2025-2026: Feature-aware allocation, graph-driven models, zero-shot LLM (likely full papers building on this WIP)

**Technical Innovation**:

**1. HMARL for DAG Workloads**:
- Extends hierarchical RL from independent tasks to OpenMP DAG workloads
- Critical path scheduling combined with energy-thermal awareness
- DAG structure (dependencies) requires makespan-aware allocation

**2. Action Space Decomposition Analysis**:
- Formal complexity analysis: Exponential (k × 2^m) → Linear (m² + m×k + 1)
- Example calculations for 3-core (285 → 40) and 12-core (356,716 → 265)
- 1,346× reduction for 12-core Xeon platform

**3. Dual-Agent Cooperative Framework**:
- **Profiler Agent**: Workload-aware (makespan, branch misses, CPU cycles, instructions, context switches, utilization, energy)
- **Temperature Agent**: Thermal-aware (absolute temperatures, temperature changes Δθ)
- Joint Action Learners (JAL) [9]: Coordinate actions via SelectCores function

**4. D3QN Off-Policy RL**:
- Double Deep Dueling Q-Network reduces sample requirements vs on-policy
- Mitigates overestimation issues in Q-learning
- Suitable for time-consuming experiments (DAG scheduling)

**5. Empirical Analysis of Tied Tasks**:
- Novel observation: Tied tasks (unbounded parallelism) cause higher branch misses, makespan, energy
- Motivates profiler-based allocation to limit cores for tied tasks
- N-Queens benchmark demonstrates effect on Intel Core i7 and Xeon

**Application Domain**:
- **Primary**: Energy and thermal-aware scheduling for multicore embedded systems
- **Workload**: OpenMP DAG tasks (directed acyclic graphs with dependencies)
- **Platforms**: Intel Core i7 (4 cores), Intel Xeon 2680 v2 (12 cores)
- **Real-Time Systems**: RTSS venue indicates applicability to real-time scheduling scenarios

**Comparison with Related Work**:

**Energy-Aware Schedulers**:
- Ahmed et al. [1]: Thermal schedulability under fluid scheduling model
- Aydin & Yang [2]: Energy-aware partitioning (NP-hard problem)
- Lee [10]: DVFS for lightly loaded multicores (more cores than tasks)
- **This work**: Online RL approach generalizable across platforms and core counts

**RL-Based Schedulers**:
- Liu et al. [11]: CARTAD with compiler-assisted RL for thermal-aware task scheduling
- Sethi [14]: Learning energy-aware transaction scheduling in databases
- Bo et al. [5]: Deep RL scheduling policy (high sampling requirements, offline only)
- Wang et al. [16]: Online power management (coarse frequency scaling)
- **This work**: HMARL with action space decomposition for scalability, per-core frequency tracking

**Hierarchical RL**:
- Barto & Mahadevan [3]: Recent advances in hierarchical RL
- Nachum et al. [13]: Data-efficient hierarchical RL
- **This work**: First application of HMARL to energy-thermal-aware DAG scheduling with formal action space analysis

**Thermal Management**:
- Dinakarrao et al. [7]: Thermal-reliability-aware RL multi-core power management
- Maity et al. [12]: Future aware dynamic thermal management in CPU-GPU embedded platforms
- **This work**: Per-core temperature tracking with dedicated Temperature Agent, OpenMP DAG workloads

**DVFS and Frequency Scaling**:
- Brodowski et al. [6]: Linux CPU frequency and voltage scaling (governors: performance, powersave, conservative, ondemand, schedutil)
- Yan et al. [18]: Dynamic voltage scaling + adaptive body biasing for heterogeneous real-time systems
- **This work**: Per-core frequency assignment based on profiling data, avoiding coarse frequency scaling limitations

**Limitations**:

1. **Work-In-Progress Status**: Full experimental evaluation not provided, only preliminary N-Queens results
2. **Limited Benchmark Scope**: Only N-Queens benchmark shown, no comprehensive DAG benchmark suite
3. **No Training Convergence Data**: No learning curves, sample complexity, or convergence time reported
4. **No Baseline Comparison**: No comparison with existing schedulers (Linux governors, federated scheduling, etc.)
5. **Reward Function Not Specified**: RewardStep mentioned but reward function design not detailed
6. **Hyperparameter Details Missing**: D3QN network architecture, learning rates, batch sizes not specified
7. **Platform Generalization Unvalidated**: Claims generalizability but only Intel platforms tested
8. **Scalability Claims Unverified**: Action space analysis formal but empirical validation on 12-core Xeon not shown

**Future Work** (Section IV):

1. **Custom Reward Function**: Design reward function estimating future states to maximize cumulative rewards (currently not specified)
2. **Comprehensive Complexity Analysis**: Formal computational complexity analysis beyond action space dimensions
3. **Physical Testbed Implementation**: Deploy on actual Intel Core i7 and Xeon 2680 v2 platforms with extensive simulation
4. **Heterogeneous Platform Extension**: Extend to CPU+GPU platforms (ARM, AMD, NVIDIA)
5. **Advanced RL Techniques**: Explore PPO, SAC, or other advanced RL algorithms for complex action spaces
6. **Benchmark Suite Evaluation**: Test on comprehensive OpenMP DAG benchmarks beyond N-Queens (BOTS, Parsec, etc.)
7. **Baseline Comparisons**: Compare with Linux governors, federated scheduling, existing RL schedulers

**Figures Summary**:
- **Figure 1** (2 rows × 3 columns): N-Queens benchmark evaluation on Intel Xeon (12 cores) and Intel Core i7 (4 cores)
  - (a, d) Branch Misses vs Cores (serial, manual, tied)
  - (b, e) Makespan vs Cores
  - (c, f) Energy Consumption vs Cores
  - Shadow lines: Result variation over 10 iterations
- **Figure 2** (Complex multi-scenario diagram): 9 scheduling scenarios illustrating makespan differences on 4-core architecture
  - Scenarios 1-2: Performance mode (tied task execution)
  - Scenarios 3-4: Powersave mode
  - Scenarios 5-6: Multi-speed policy
  - Scenarios 7-9: Proposed HMARL approach (three rounds)
  - Color coding: Blue (P - performance), Green (S - powersave)
  - Numbers: Hypothetical task execution time
  - Green dash line: DAG makespan

**Tables Summary**:
- **No tables provided** (WIP paper format)

**Algorithm Summary**:
- **Algorithm 1**: Proposed Method based on HMARL (12-line pseudocode)
  - Inputs: DAG τ_i, states s_p, s_θ
  - Key functions: ProfilerAgentAct, TemperatureAgentAct, SelectCores, Environment, SampleBuffer, RewardStep, StoreTransition, ProfilerAgentTrain, TemperatureAgentTrain

**Mathematical Notation**:
- κ_g: DAG graph
- s_p, s_θ: Profiler and temperature states
- a_p, a_θ: Profiler and temperature actions
- a_o: Combined action (core combinations + frequencies)
- s'_p, s'_θ: Next profiler and temperature states
- r_p, r_θ: Profiler and temperature rewards
- d_p, d_θ: Done flags
- mem_cntr: Memory counter (sample buffer threshold)
- BS: Batch size for training
- Bf_i, Br_i, Cc_i, Fr_i, Ic_i, Cs_i, Ut_i, Ex_i, En_i: Profiling features
- θ_0...θ_n: Per-core absolute temperatures
- Δθ_0...Δθ_n: Per-core temperature changes
- m: Number of cores
- k: Number of frequency levels
- l: Cores selected from m
- (m choose l): Binomial coefficient
- a_T: Total actions
- τ_i: DAG task
- τ*_{i,1}: Tied task (specific parallel task type)
- c_0, c_1, c_2, c_3: Core identifiers

**Connection to Research Arc**:

Timeline:
1. 2015: ML/pattern recognition (pivezhandi2015statistical)
2. 2016: ASIP/CORDIC design (pivezhandi2016asip)
3. 2020: FPGA event-based vision (pivezhandi2020parahist)
4. 2023: Hierarchical RL for DVFS (pivezhandi2023toward - M.S. thesis)
5. 2024: Flow-augmented few-shot RL (pivezhandi2024flowrl)
6. **2024: HMARL for OpenMP DAG workloads (this WIP paper)**
7. 2025-2026: Feature-aware allocation, graph-driven models, zero-shot LLM

This WIP paper extends M.S. thesis (pivezhandi2023toward) by:
- Focusing on OpenMP DAG workloads instead of independent tasks
- Providing formal action space decomposition analysis (exponential → linear)
- Introducing explicit temperature-aware agent with per-core thermal tracking
- Analyzing tied task inefficiency (novel empirical observation)
- Collaborating with Aakriti Jain (co-author) and Ali Jannesari (M.S. co-advisor)

**Advisor and Co-Author Roles**:
- **Abusayeed Saifullah**: PhD advisor at Wayne State (formerly at Iowa State), M.S. thesis co-major professor
- **Ali Jannesari**: M.S. thesis co-major professor at Iowa State, continues collaboration
- **Aakriti Jain**: PhD student at Wayne State (co-author, likely contributed to experimental setup or analysis)

**Publication Strategy**:
- WIP paper at RTSS 2024 (4 pages) presents preliminary design and motivation
- Likely full paper submission to major conference (RTAS, RTCSA, ICCAD, DAC) or journal (IEEE TCAD, IEEE TECS, ACM TECS) planned with complete experimental evaluation
- Full paper would include:
  - Comprehensive benchmark suite (BOTS, Parsec, OpenMP DAG benchmarks)
  - Baseline comparisons (Linux governors, federated scheduling, RL schedulers)
  - Training convergence analysis
  - Hyperparameter details and ablation studies
  - Heterogeneous platform evaluation (ARM, AMD, GPU)

**Code Availability**: Not mentioned (WIP paper, code likely under development)

**Reproducibility**:
- Algorithm 1 pseudocode provided
- Platforms specified (Intel Core i7 4-core, Xeon 2680 v2 12-core)
- Linux governors mentioned (performance, powersave)
- Profiling tools specified (perf, RAPL msr)
- N-Queens benchmark used (standard OpenMP benchmark)
- However, D3QN hyperparameters, network architecture, reward function not detailed

**Related References in Paper** (18 citations):

Key citations:
- [1] Ahmed et al. (TECS 2016): Thermal schedulability
- [2] Aydin & Yang (IPDPS 2003): Energy-aware partitioning (NP-hard)
- [3] Barto & Mahadevan (2003): Hierarchical RL advances
- [4] Bhuiyan et al. (ECRTS 2023): Precise scheduling with DPM (co-authored with Mohammad)
- [5] Bo et al. (RTAS 2021): Deep RL scheduling policy
- [6] Brodowski et al. (2013): Linux CPU frequency scaling documentation
- [7] Dinakarrao et al. (JETC 2019): Thermal-reliability-aware RL
- [9] Lee et al. (IEEE Signal Processing 2020): Optimization for RL cooperative agents
- [13] Nachum et al. (NeurIPS 2018): Data-efficient hierarchical RL
- [18] Yan et al. (ICCAD 2003): Combined DVFS and adaptive body biasing

## Bibdata

```bibtex
% Row 10
@inproceedings{pivezhandi2024work,
  title={Work-In-Progress: Energy and Thermal-Aware Scheduling based on HMARL for OpenMP DAG Workloads},
  author={Pivezhandi, Mohammad and Jain, Aakriti and Saifullah, Abusayeed and Jannesari, Ali},
  booktitle={2024 IEEE Real-Time Systems Symposium (RTSS)},
  pages={455--458},
  year={2024},
  organization={IEEE}
}
```
