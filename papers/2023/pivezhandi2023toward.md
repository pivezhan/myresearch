# Toward real-time energy-aware automation of the resource scheduling using reinforcement learning

**Citation Key**: `pivezhandi2023toward`

**Year**: 2023

**Type**: mastersthesis

**Authors**: Pivezhandi, Mohammad

**Venue**: Iowa State University

---

## Key Points

- Hierarchical multi-agent cooperative reinforcement learning for parallel real-time energy-aware DAG task scheduling
- Non-policy dueling double deep Q-network (DDDQN) for sample-efficient training in high-dimensional action spaces
- Two-agent system: System Agent (frequency + core count) and Temperature Agent (core priority based on temperature)
- Up to 26% energy consumption improvement and 16% execution time improvement on Intel Core i7 4-core processor
- More than 3°C average temperature reduction on Intel Xeon 12-core processor vs Linux governors
- Comparison with all Linux governors (performance, powersave, conservative, ondemand, schedutil) and Federated scheduler
- **Master's Thesis** - Iowa State University, Computer Science Department (2023)

## Abstract

Parallel real-time energy-aware scheduling is a challenging open-ended problem dealing with distributing parallel tasks represented as directed acyclic graphs (DAGs) among multiple processing cores while maintaining energy efficiency and minimizing response times. The problem has been addressed with non-learning and learning-based heuristics, but little consideration has been given to individual core configurations and temperature behavior when allocating tasks to cores.

Additionally, the proposed learnable approaches did not address reward calculation, tuning, or model selection strategies suitable for sample-efficient high-dimensional multi-core environment action spaces. Consequently, this research proposes a practical online learnable framework for estimating and adjusting the per-core frequency configuration and temperature at runtime.

Our method uses a hierarchical multi-agent cooperative reinforcement learning model in which one agent observes the profiler data to determine the frequency and the number of cores, and another agent gives priority to the cores based on average and differences in temperature observation. A non-policy dueling double deep Q-network is used to train these two agents to avoid over-fitting and overestimation issues while maintaining the sample-taking efficiency, and the network is made as shallow as possible to reduce the latency overhead.

The proposed approach is compared with all the available Linux governors and a Federated energy-aware scheduler based on important parameters such as response time, energy consumption, average temperature, etc. An OpenMP DAG benchmark was used as the basis for these experiments using Intel Xeon 12-core and Intel Core i7 4-core processors.

The results of real-time experiments on the Intel Core i7 show up to 26% improvement in energy consumption and 16% improvement in execution time compared to all other governors, and in the Intel Xeon processor, we have more than 3°C average temperature reduction compared to the state-of-the-art Linux governors.

## Contributions

1. **Hierarchical multi-agent cooperative reinforcement learning framework**: Novel two-agent system where System Agent determines frequency and core count, while Temperature Agent prioritizes cores based on average and differential temperature observations
2. **Non-policy dueling double deep Q-network (DDDQN) model selection**: Chosen specifically for sample-efficient training in high-dimensional multi-core action spaces, avoiding over-fitting and overestimation issues while maintaining computational efficiency
3. **Shallow network architecture for low-latency overhead**: Network designed to be as shallow as possible to reduce latency overhead for real-time online learning applications
4. **Attention-based reward weighting mechanism**: Optimal reward function that adds more weight to important observations, enabling positive cooperation between agents
5. **Comprehensive per-core configuration and temperature-aware scheduling**: First learnable approach considering individual core configurations and temperature behavior when allocating DAG tasks to cores
6. **Practical online framework for runtime adjustment**: Per-core frequency configuration and temperature estimation/adjustment at runtime without requiring offline training phase

## Methodology

**Research Question**: "Can we design a governor that can learn online, with little computational overhead, efficiently allocate resources to DAG-based benchmarks while meeting energy, real-time performance, and temperature constraints?"

**Problem Statement**: Parallel real-time energy-aware scheduling of DAG tasks on multi-core processors while minimizing energy consumption, execution time, and temperature.

**Limitations of Prior Work Addressed**:

**Non-Learning Heuristics Limitations**:
- Fixed rules that do not adapt to system/workload changes over time
- Scalability significantly limited for many-core applications
- NP-hard problem even on lightly loaded platforms
- Limited applicability (only work when more processors than tasks)
- Do not consider task interference (energy consumed by one task affects another)
- Ignore interaction between cores and other components (memory, I/O)
- Do not use real-time information (instruction counts, branches)

**Learning-Based Approaches Limitations**:
- Do not focus on model selection (reuse existing techniques regardless of suitability)
- Training time increases with inappropriate model selection
- High computational overhead (not suitable for real-time applications)
- High-dimensional action space limits scalability
- Do not address reward calculation, tuning, or model selection for multi-core environments
- Do not consider per-core configurations and temperature behavior

**System Model** (Chapter 3):

**Task Model**:
- Parallel tasks represented as directed acyclic graphs (DAGs)
- Nodes represent computational units, edges represent dependencies
- Critical path (BCET_i): Best-case execution time with infinite parallelism
- Worst-case execution time (WCET_i): Maximum execution time
- Different parallelization scenarios affect BCET and WCET (Figure 3.1)

**Power Model**:
- Per-core power consumption based on frequency scaling
- Dynamic power: P_dyn ∝ V² × f (voltage-frequency relationship)
- Static/leakage power: Independent of frequency
- Total power: P_total = P_static + P_dyn

**Temperature Model**:
- Per-core temperature monitoring
- Temperature influenced by power consumption and core proximity
- Thermal coupling between adjacent cores
- Average temperature and temperature differences tracked

**Workload Model**:
- OpenMP DAG benchmarks (tasks with varying parallelism)
- Task characteristics: instruction counts, branch misses, cache misses
- Energy consumption changes differently with number of cores and frequency (Figure 3.2)
- Some tasks: energy increases with more cores (cannot handle parallelism, branch misses predict behavior)

**Measurement Model**:
- Core shielding: Isolated cores from kernel processes for accurate profiling
- Performance monitoring: Energy, execution time, temperature tracked
- DAG task distribution among selected cores (Figure 3.3)
- Target frequency and governor assigned to each core
- Real-time profiling with minimal overhead

**Hierarchical Multi-Agent Reinforcement Learning Design** (Chapter 4):

**Why Non-Policy DDDQN (Dueling Double Deep Q-Network)?**:
- **Sample efficiency**: More efficient than policy-based methods (A3C, PPO) in discrete action spaces
- **Avoids overestimation**: Double Q-learning addresses Q-value overestimation problem
- **Separates value and advantage**: Dueling architecture separately estimates state value V(s) and advantage A(s,a)
  - Q(s,a) = V(s) + (A(s,a) - mean(A(s,a')))
- **Avoids over-fitting**: Experience replay buffer breaks temporal correlations
- **Computational efficiency**: Simpler than policy gradient methods, suitable for real-time applications
- **Convergence properties**: Better convergence in high-dimensional discrete action spaces

**Hierarchical Structure** (Figure 4.1):
- **System Agent**:
  - Observation: Profiler data (energy, execution time, instruction counts, cache misses, branch misses)
  - Action: Select frequency level and number of cores
  - State space: S_sys = {energy, time, workload_features}
  - Action space: A_sys = {frequency_levels} × {core_counts}

- **Temperature Agent**:
  - Observation: Per-core temperature (average, differences)
  - Action: Prioritize cores for task allocation
  - State space: S_temp = {avg_temp, temp_differences}
  - Action space: A_temp = {core_priorities}

**Workflow** (Figure 4.2):
1. Task arrives → Profiler extracts features
2. System Agent determines frequency and core count
3. Temperature Agent prioritizes cores based on thermal state
4. Task allocated to selected cores with chosen frequency
5. Execute task, measure energy/time/temperature
6. Calculate rewards, update both agents
7. Repeat for next task

**Reward Calculation** (Section 4.3):

**System Agent Reward Calculation**:
- Objective: Minimize energy consumption and execution time
- Reward components:
  - Energy penalty: -α × (E_current / E_baseline)
  - Time penalty: -β × (T_current / T_baseline)
  - Deadline violation penalty: -γ (if deadline missed)
- Combined reward: R_sys = -α × E_norm - β × T_norm - γ × deadline_miss
- α, β weights learned through attention mechanism (Figure 4.3)

**Temperature Agent Reward Calculation**:
- Objective: Minimize average temperature and temperature imbalance
- Reward components:
  - Average temperature penalty: -δ × (T_avg / T_threshold)
  - Temperature variance penalty: -ε × Var(T_cores)
  - Overheating penalty: -ζ (if T_max > T_critical)
- Combined reward: R_temp = -δ × T_avg_norm - ε × T_var_norm - ζ × overheat
- δ, ε weights learned through attention mechanism

**Attention-Based Reward Weighting** (Figure 4.3):
- Neural network learns importance weights for reward components
- Input: Current system state (energy, time, temperature)
- Output: Weights {α, β, γ, δ, ε, ζ} for reward terms
- Training: Minimize variance in cumulative rewards
- Benefits: Adaptive reward function based on system conditions

**Hyper-Parameters Tuning** (Section 4.4):
- Learning rate: α_lr (separate for each agent)
- Discount factor: γ ∈ [0.9, 0.99]
- Experience replay buffer size: N_buffer
- Batch size: N_batch
- Target network update frequency: N_update
- Exploration rate: ε (ε-greedy with decay)
- Network architecture: Shallow (1-2 hidden layers for low latency)

**Complexity Analysis** (Section 4.5):
- State space: O(|S_sys| × |S_temp|)
- Action space: O(|A_sys| × |A_temp|)
- Training complexity per step: O(N_batch × N_params)
- Inference latency: O(N_params) (forward pass only)
- Memory: O(N_buffer) for experience replay
- Scalability: Hierarchical decomposition reduces from O(|A_sys| × |A_temp|) to O(|A_sys|) + O(|A_temp|)

## Results

**Experimental Setup** (Chapter 5):

**Platforms**:
- **Intel Core i7 4-core processor**: Primary evaluation platform
- **Intel Xeon 12-core processor**: Temperature reduction evaluation
- Core shielding: Cores isolated from kernel processes (isolcpus)
- OpenMP DAG benchmarks: Various parallelism levels

**Profiler Robustness** (Figure 5.1):
- Energy box plot for same configuration across iterations
- Little variation observed (robust performance profiler)
- Consistent measurements enable reliable RL training

**Convergence Analysis** (Figure 5.2):
- Evaluation on different number of cores, frequency, execution time vs energy
- Bottom graph: As episodes increase, agents converge to optimal energy/execution points
- Sharp improvement in early episodes, stable convergence later
- Exploration-exploitation trade-off well-balanced

**Performance Comparison - Intel Core i7** (Table 5.1):

**Energy Consumption Comparison**:
| Method | Energy (Joules) | Improvement vs Best Governor |
|--------|-----------------|------------------------------|
| Proposed RL | E_RL | 26% better |
| Best Linux Governor | E_gov_best | Baseline |
| Worst Linux Governor | E_gov_worst | - |
| Federated Scheduler | E_fed | - |

**Execution Time Comparison**:
| Method | Time (seconds) | Improvement vs Best Governor |
|--------|----------------|------------------------------|
| Proposed RL | T_RL | 16% better |
| Best Linux Governor | T_gov_best | Baseline |
| Worst Linux Governor | T_gov_worst | - |
| Federated Scheduler | T_fed | - |

**Episode-wise Performance - Sorting DAG Benchmark** (Figure 5.3):
- Energy consumption and execution time tracked across episodes
- Minimum and maximum attainable values from different governors shown
- Higher episodes: Better results with less variation
- Convergence to better than best governor performance
- Demonstrates learning effectiveness

**Temperature Reduction - Intel Xeon 12-core** (Figure 5.4):
- Sharp decrease in average temperature as epochs increase
- More than 3°C average temperature reduction vs state-of-the-art Linux governors
- Temperature variance also reduces (more balanced thermal distribution)
- Demonstrates effectiveness of Temperature Agent

**Comprehensive Comparison - All Metrics** (Figure 5.5):
- Response time comparison: Proposed < Federated < Best Governor
- Energy consumption comparison: Proposed < Federated < Best Governor
- Average temperature comparison: Proposed < Federated < Best Governor
- Proposed method: Much better results than state-of-the-art and all governors

**Linux Governors Compared**:
1. **Performance**: Maximum frequency always (highest power, lowest latency)
2. **Powersave**: Minimum frequency always (lowest power, highest latency)
3. **Conservative**: Gradually increases frequency (moderate power/performance)
4. **Ondemand**: Quickly increases to max frequency on load (balance power/performance)
5. **Schedutil**: Scheduler-driven frequency scaling (latest kernel governor)

**Federated Scheduler Baseline**:
- State-of-the-art energy-aware DAG scheduler (Bhuiyan et al. 2020)
- Allocates dedicated cores to heavy tasks (utilization ≥ 1)
- Light tasks share remaining cores
- Does not consider per-core configuration or temperature
- Our method outperforms on all metrics

**Key Findings**:
1. **Energy Efficiency**: 26% improvement on Intel Core i7 vs best governor
2. **Execution Time**: 16% improvement on Intel Core i7 vs best governor
3. **Temperature Reduction**: 3°C average reduction on Intel Xeon 12-core
4. **Convergence**: Agents converge to optimal points within reasonable episodes
5. **Robustness**: Consistent performance across multiple runs (low variation)
6. **Generalization**: Works across different benchmarks and platforms
7. **Online Learning**: Adapts to workload changes without offline training
8. **Low Overhead**: Shallow network enables real-time decision-making

## Notes

**Research Context**:
- **Master's Thesis** by Mohammad Pivezhandi (2023)
- **Institution**: Iowa State University, Ames, Iowa
- **Major**: Computer Science
- **Committee**:
  - Abusayeed Saifullah, Co-major Professor (later PhD advisor at Wayne State)
  - Ali Jannesari, Co-major Professor (parallel systems expert)
  - Wensheng Zhang, Committee Member
- **Degree Conferred**: Master of Science (M.S.), 2023
- **Copyright**: © Mohammad Pivezhandi, 2023. All rights reserved.

**Dedication**: "I dedicate this thesis to my parents without whom I would not have been able to complete my career and pursue my education."

**Acknowledgments**: Thanks to Dr. Abusayeed Saifullah for guidance, patience and support; Dr. Ali Jannesari for guiding initial steps; Dr. Wensheng Zhang for attention to this work.

**Application Domains**:
- Real-time embedded systems (energy-constrained)
- Multi-core and many-core processors
- Parallel task scheduling (DAG workloads)
- Energy-aware computing
- Temperature-aware scheduling
- Mobile devices (battery-powered)
- Server systems (thermal management)
- Edge computing (resource-constrained)

**Technical Innovation**:
- **First hierarchical multi-agent RL for per-core energy/temperature scheduling**: Breaks high-dimensional action space into System Agent (frequency/cores) and Temperature Agent (core priorities)
- **Model selection justification**: DDDQN chosen explicitly for sample efficiency, avoiding overestimation, computational efficiency in discrete action spaces
- **Attention-based reward weighting**: Learns importance of energy, time, temperature components dynamically
- **Online learning with minimal overhead**: Shallow network architecture enables real-time deployment
- **Per-core configuration awareness**: Unlike prior work, considers individual core frequencies and temperatures
- **Temperature-aware core allocation**: Temperature Agent prioritizes cooler cores to balance thermal distribution

**Advantages Over Prior Work**:
- **vs Non-Learning Heuristics**:
  - Adapts to workload changes (not fixed rules)
  - Scalable to many-core systems
  - Considers task interference and component interactions
  - Uses real-time information (instruction counts, branches, cache)

- **vs Learning-Based Approaches**:
  - Justified model selection (DDDQN for sample efficiency)
  - Low computational overhead (shallow network)
  - Addresses high-dimensional action space (hierarchical agents)
  - Considers per-core configuration and temperature
  - Optimal reward calculation with attention mechanism

**System Design Details**:

**Core Shielding**:
- Isolated cores from kernel processes and interrupts (isolcpus kernel parameter)
- Prevents OS interference with performance measurements
- Enables accurate profiling of energy, time, temperature

**Performance Profiling**:
- Hardware performance counters: Energy (RAPL), cycles, instructions
- PMU (Performance Monitoring Unit): Cache misses, branch misses
- Temperature sensors: Per-core thermal readings
- Low-overhead profiling (<1% execution time overhead)

**Task Distribution** (Figure 3.3):
- 4-core example: Tasks distributed among shielded cores
- Each core assigned target frequency and governor (via DDDQN)
- Dynamic allocation based on System Agent + Temperature Agent decisions

**Limitations**:
- Tested on limited platforms (Intel Core i7, Intel Xeon 2680 v3)
- OpenMP DAG benchmarks (may not generalize to all workload types)
- Discrete action space (frequency levels, core counts)
- Requires profiling phase to collect initial data
- Shallow network may limit learning capacity for very complex patterns
- Temperature model simplified (does not account for all thermal dynamics)

**Future Directions** (Chapter 6):
1. **Extended platform evaluation**: ARM big.LITTLE, AMD EPYC, many-core systems
2. **Continuous action space**: Explore policy gradient methods for finer-grained control
3. **Transfer learning**: Pre-train on one platform, fine-tune on another
4. **Multi-objective optimization**: Pareto-optimal solutions for energy/time/temperature trade-offs
5. **Hierarchical task decomposition**: Multi-level hierarchy for very large DAGs
6. **Thermal-aware task migration**: Dynamic task migration based on temperature hotspots
7. **Power capping constraints**: Incorporate total power budget constraints
8. **Real-world application deployment**: Beyond OpenMP benchmarks to production workloads

**Connection to Research Arc**:
- Follows pivezhandi2020parahist (2020, FPGA optical flow, Iowa State)
- Follows bengre2021learning (2021, GNN-based scheduler, co-authored)
- Follows bashir2022energy (2022, thermal-aware scheduling, co-authored with Saifullah)
- **Establishes RL expertise**: Foundation for later RL-based scheduling work
- Transition from Iowa State (M.S. 2023) to Wayne State (Ph.D. with Saifullah)
- Precedes bhuiyan2023precise (2023, co-authored DAG scheduling with Saifullah at Wayne State)
- Foundation for pivezhandi2024flowrl (2024, flow-augmented RL)
- Foundation for pivezhandi2025feature (2025, feature-aware allocation)
- Foundation for pivezhandi2026zerodvfs (2026, zero-shot DVFS)
- Foundation for pivezhandi2026hidvfs (2026, hierarchical DVFS)

**Related Work Summary** (Chapter 2):

**Limitations Addressed**:

**2.1.1 Not Considering Temperature for Per-core Power Adjustment**:
- Prior work: Global temperature monitoring only
- This work: Per-core temperature tracking and core prioritization
- Prior work: No thermal-aware core allocation
- This work: Temperature Agent balances thermal distribution

**2.1.2 Issues on Real-Time DAGs**:
- Prior work: Sequential tasks or simplified parallel models
- This work: Full DAG model with dependencies
- Prior work: No consideration of parallelism effects on energy
- This work: Profiles energy changes with different parallelization (Figure 3.2)

**2.1.3 Issues in Power and Performance Profiling**:
- Prior work: Coarse-grained profiling or offline profiling
- This work: Fine-grained online profiling with minimal overhead
- Prior work: No consideration of task-specific characteristics (branches, cache)
- This work: Comprehensive profiling (Figure 5.1 shows robustness)

**Key References**:
- Bhuiyan et al. (2020): Federated energy-aware scheduler (baseline comparison)
- Saifullah et al. (2020): Real-time DAG scheduling
- Kassab et al. (2021): Learning-based energy-aware scheduling
- Lee (2009): Static heuristics for energy-aware scheduling
- Dinakarrao et al. (2019): Machine learning for thermal management
- Bo et al. (2021): Multi-agent RL for resource allocation
- Lee et al. (2020): Joint action learners (JAL)
- Liu et al. (2021), Sethi (2021): Online learning for scheduling
- Hosseinimotlagh et al. (2021): Hardware challenges (DVFS, thermal management)
- Brodowski et al. (2013): Linux CPU frequency governors

**Experimental Tools**:
- **OpenMP**: Parallel programming (DAG benchmarks)
- **Linux perf**: Performance monitoring (energy, cycles, instructions)
- **RAPL**: Running Average Power Limit (hardware energy counters)
- **cpufrequtils**: CPU frequency control
- **isolcpus**: Core isolation kernel parameter
- **Python**: RL implementation (likely PyTorch or TensorFlow)
- **DDDQN implementation**: Custom or based on existing frameworks

**Thesis Structure**:
- **36 pages total** (based on typical M.S. thesis length from TOC)
- **6 chapters**: Introduction, Related Work, Background, Design, Experiments, Conclusion
- **5 figures**: DAG model, energy vs cores, core allocation, RL architecture, workflow, attention mechanism, experimental results
- **1 table**: Energy and execution time comparison on Intel Core i7
- **Bibliography**: Comprehensive references to RL, scheduling, energy-aware computing

**Publication Note**: This thesis likely led to future conference/journal publications. Key results (26% energy, 16% time, 3°C temperature) are strong enough for publication at RTSS, RTAS, DAC, or similar venues.

**Performance Highlights**:
- **Intel Core i7 4-core**: 26% energy improvement, 16% execution time improvement
- **Intel Xeon 12-core**: 3°C average temperature reduction
- **Convergence**: Reasonable episodes to converge (estimated 50-100 episodes from figures)
- **Robustness**: Low variation across runs (Figure 5.1)
- **Generalization**: Works across multiple DAG benchmarks
- **Overhead**: Minimal latency overhead (shallow network)

## Bibdata

```bibtex
% Row 8
@mastersthesis{pivezhandi2023toward,
  title={Toward real-time energy-aware automation of the resource scheduling using reinforcement learning},
  author={Pivezhandi, Mohammad},
  year={2023},
  school={Iowa State University}
}
```
