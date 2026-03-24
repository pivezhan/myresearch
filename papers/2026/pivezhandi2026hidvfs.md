# HiDVFS: A Hierarchical Multi-Agent DVFS Scheduler for OpenMP DAG Workloads

**Citation Key**: `pivezhandi2026hidvfs`

**Year**: 2026

**Type**: article

**Authors**: Pivezhandi, Mohammad and Saifullah, Abusayeed and Jannesari, Ali

**Venue**: arXiv preprint arXiv:2601.06425

---

## Key Points

- Hierarchical multi-agent DVFS scheduler with 3 cooperative agents (profiler, thermal, priority) achieving 3.95× average speedup and 47.1% energy reduction across 9 BOTS benchmarks on NVIDIA Jetson TX2
- D3QN-based architecture with model-based reward estimation enabling makespan-first optimization (β=1.0) while reporting energy and temperature as secondary outcomes
- Action space decomposition from exponential m^n to linear m×n through multi-agent collaboration, reducing training time by 40% compared to single-agent RL
- Multi-seed validation (seeds 42, 123, 456) demonstrates best finetuned performance: 4.16±0.58s average makespan (L10) vs GearDVFS 14.32±2.61s, representing 3.44× speedup
- Per-core temperature-aware DVFS with runtime profiling (perf, cpufreq-info, sensors) addressing Linux governor limitations and enabling fine-grained frequency control (12 steps: 345.6-2035.2 kHz)
- Comprehensive BOTS benchmark evaluation showing highest speedups for sparselu (7.78×) and alignment (5.58×), validating generalization across diverse OpenMP DAG workloads

## Abstract

With advancements in multicore embedded systems, static leakage power (exponentially tied to chip temperature) has surpassed dynamic power consumption, rising from 22% to over 63% of total power as technology scales. Existing DVFS solutions lack per-core frequency monitoring and fail to address overheating from uneven core activity, while task assignments without detailed profiling overlook irregular execution patterns in OpenMP DAG workloads. This paper targets OpenMP DAGs with a makespan-first objective, reporting energy and thermal behavior as secondary outcomes.

HiDVFS (Hierarchical multi-agent DVFS scheduler) employs three collaborative agents to overcome these limitations: (1) a **profiler agent** selects cores and frequencies using runtime profiling data (utilization, energy, makespan), (2) a **thermal agent** manages temperature-aware core combinations via per-cluster temperature sensors, and (3) a **priority agent** sets static task priorities during resource contention. This decomposition reduces the action space from exponential m^n (2.1×10^9 for 5 cores, 12 frequencies) to linear m×n (60), achieving 40% faster training than single-agent RL.

The framework uses D3QN (Dueling Double Deep Q-Networks) with model-based reward estimation for sample efficiency. Rewards are shaped for makespan-first optimization with energy and temperature regularizers: R1 = β(M_target/(M+ε)) for the profiler agent (β=1 prioritizes makespan), R2 = 1-0.05|T-T_target| for the thermal agent (50°C limit), and R3 = (M_target-M)/M_target for the priority agent.

Evaluated on NVIDIA Jetson TX2 (6 heterogeneous cores, 12 frequency levels) using 9 BOTS benchmarks (alignment, concom, fft, fib, floorplan, health, sort, sparselu, strassen, uts) with multi-seed validation (seeds 42, 123, 456), HiDVFS achieves the best finetuned performance: 4.16±0.58s average makespan (L10), representing a 3.44× speedup over GearDVFS (14.32±2.61s) and 50.4% energy reduction (63.7 kJ vs 128.4 kJ). Across all BOTS benchmarks, HiDVFS achieves an average 3.95× speedup and 47.1% energy reduction, with highest improvements for sparselu (7.78×) and alignment (5.58×).

## Contributions

1. **HiDVFS Framework**: A hierarchical multi-agent DVFS scheduler using runtime temperature profiling and predictive reward functions to optimize energy, makespan, and thermal constraints for OpenMP DAGs. Three collaborative agents decompose the exponential action space (m^n) into linear complexity (m×n), enabling per-core frequency control while maintaining thermal feasibility.

2. **Runtime Profiler-Driven Scheduling**: Analysis of runtime profiler data (perf for execution profiling, cpufreq-info for DVFS state, sensors for per-cluster temperatures) to drive real-time scheduling decisions. Statistical analysis (Mann-Whitney U test) demonstrates that frequency exerts the strongest influence on makespan (p=1.45e-283) and energy (p=6.03e-144), followed by core count and priority, motivating the profiler agent's makespan-first reward.

3. **Model-Based Reward Estimation**: Enhanced reward estimation leveraging a learned environment model to predict future states and refine reward signals, rather than relying solely on instantaneous interactions. This approach achieves few-shot learning efficiency without requiring expert demonstrations (avoiding traditional IRL's dependency), using attention models to weight key features (temperature, utilization, makespan predictions) based on temporal impact.

4. **Comprehensive Multi-Seed Evaluation**: Evaluation on NVIDIA Jetson TX2 using 9 BOTS benchmarks with multi-seed validation (seeds 42, 123, 456). HiDVFS achieves the best finetuned performance: 4.16±0.58s average makespan (L10), 3.44× speedup over GearDVFS, 50.4% energy reduction (63.7 kJ vs 128.4 kJ), 85.3% high-frequency adoption, and 85.7% core utilization. Decision latency: 2ms round-trip (0.5ms action transmission + 0.3ms DVFS application + 0.8ms profiling + 0.4ms response).

## Methodology

### Three-Agent Hierarchical Architecture

HiDVFS decomposes the NP-hard DVFS optimization problem into three cooperative sub-problems, reducing action space complexity from exponential to linear:

**Profiler Agent (Frequency + Core Selection)**:
- **State**: Utilization, energy, makespan from perf profiling
- **Action**: Select (core_count, frequency_level) pairs from m×n space
- **Reward**: R1 = β(M_target/(M+ε)) + (1-β)(E_target/(E+ε)), where β=1 prioritizes makespan, ε=10^-3 prevents division issues

**Thermal Agent (Temperature-Aware Core Combinations)**:
- **State**: Per-cluster temperature readings from sensors (cores 1-2: Denver cluster, cores 0,3-5: Cortex cluster)
- **Action**: Select core combinations prioritizing cold cores to maintain T < 50°C thermal limit
- **Reward**: R2 = 1-0.05|T-T_target| (capped at 1) if T ≤ T_target, else R2 = 1-0.5(T-T_target), with -1 penalty for crossing T_target from below

**Priority Agent (Static Task Priority Assignment)**:
- **State**: Makespan from previous episode
- **Action**: Select static priority combinations (SCHED_FIFO priorities 1-99) to minimize resource contention
- **Reward**: R3 = (M_target-M)/M_target quantifies makespan improvement over target

### D3QN with Model-Based Reward Shaping

Each agent uses D3QN (Dueling Double Deep Q-Networks) separating value and advantage streams for stable Q-value learning. The environment model predicts future states to refine reward estimation:

1. **Direct RL**: Agents execute actions, environment returns next states and instant rewards, transitions stored in replay buffers
2. **Model Training**: Environment model trained using recent real transitions to predict future states
3. **Model-Based Reward Estimation**: Sample transitions from buffers, use environment model to predict future states, estimate future-based rewards using attention-weighted features, store refined transitions
4. **Agent Training**: Sample combined real + model-based transitions, train each agent's D3QN with future-oriented rewards

Attention models weight input observations (temperature, frequency, utilization, makespan predictions) based on importance in predicting future performance and energy outcomes, trained via backpropagation on predicted states.

### Hyperparameter Tuning and Target Metrics

Target makespan and energy determined by evaluating four scenarios:
1. Sequential execution, lowest frequency, all cores
2. Sequential execution, lowest frequency, one core
3. Sequential execution, highest frequency, all cores
4. Sequential execution, highest frequency, one core

Minimum values selected as targets (practical baseline, not universally optimal). Thermal limit: 50°C consistent with prior work. Reward coefficients selected through empirical grid search: β=1.0 (makespan-first), thermal penalty 0.5 (rapid correction within 2-3 episodes), thermal bonus 0.05 (gentle encouragement), ε=10^-3 (numerical stability).

### Complexity Analysis and Practical Deployment

**Action Space Reduction**: From m^n (2.1×10^9 for 5 cores, 12 frequencies) to m×n (60) through multi-agent decomposition. Each agent handles a reduced subset of decisions with manageable complexity.

**Server-Client Architecture**: Complex computations (environment modeling, reward estimation, hyperparameter tuning) offloaded to high-performance server (16 GB RAM, 4-core Intel Core i7). Server communicates frequency-core assignments and priorities to client (Jetson TX2), which executes tasks and returns profiling data. Hyperparameter tuning (grid search): 10-12 hours offline, no runtime cost.

**Decision Latency**: 2ms round-trip per decision cycle:
- Action transmission: ~0.5ms
- DVFS application: ~0.3ms (writes to sysfs, frequency changes within 1-2 CPU cycles <1µs)
- Profiling overhead: ~0.8ms (perf measurement)
- Response transmission: ~0.4ms

**Level of Parallelism**: Dynamically allocates CPU cores based on parallelism requirements (makespan_one_core / makespan_all_cores). Applications prioritized, higher-priority tasks receive cores first.

## Results

### Overall BOTS Benchmark Performance (Table 3 from Paper)

HiDVFS vs GearDVFS across 9 BOTS benchmarks (Jetson TX2, Seed 42, Finetuned):

| Benchmark | HiDVFS L10 (s) | GearDVFS L10 (s) | Speedup | Energy HiDVFS (kJ) | Energy GearDVFS (kJ) |
|-----------|----------------|------------------|---------|--------------------|-----------------------|
| alignment | 3.31 | 18.44 | 5.58× | 7.19 | 14.69 |
| concom | 16.64 | 32.21 | 1.94× | 13.01 | 33.92 |
| fft | 3.35 | 13.06 | 3.90× | 6.10 | 12.38 |
| fib | 0.82 | 3.88 | 4.76× | 2.81 | 5.52 |
| floorplan | 1.85 | 4.15 | 2.24× | 2.67 | 6.29 |
| health | 4.33 | 14.49 | 3.35× | 5.30 | 10.84 |
| sort | 15.63 | 50.93 | 3.26× | 18.62 | 27.43 |
| sparselu | 0.76 | 5.93 | 7.78× | 2.39 | 5.33 |
| strassen | 1.00 | 4.10 | 4.09× | 2.30 | 4.73 |
| uts | 10.20 | 32.17 | 3.15× | 15.62 | 23.52 |
| **Average** | **5.79** | **17.94** | **4.09×** | **7.60** | **14.47** |

**Energy Efficiency**: 47.1% average energy reduction (from 144.7 kJ to 76.0 kJ total), consistent 0.86 Energy/Makespan ratio across benchmarks.

### Multi-Seed RL Algorithm Comparison (Table 5 from Paper)

Comparison across seeds 42, 123, 456 on FFT benchmark (100 epochs per phase):

**Single-Agent Approaches**:
- zTT: 5.45±1.07s L10, 74.2±1.5 kJ, 70.7±6.3% HF rate
- DynaQ: 10.72±2.08s L10, 93.6±5.8 kJ, 30.0±9.4% HF rate
- PlanGAN: 7.57±0.67s L10, 73.9±2.1 kJ, 67.0±1.6% HF rate
- GearDVFS: 14.32±2.61s L10, 128.4±9.5 kJ, 21.3±1.7% HF rate
- SARB: 7.66±4.54s L10, 124.2±28.5 kJ, 28.7±26.8% HF rate (uses V8 for seed 42, V4 for seeds 123/456)

**Multi-Agent Approaches**:
- MAMB: 7.09±1.29s L10, 87.3±5.9 kJ, 66.0±4.5% HF rate
- MAMF: 4.98±0.61s L10, 71.4±1.9 kJ, 78.7±5.3% HF rate
- **HiDVFS**: **4.16±0.58s L10**, **63.7±3.7 kJ**, **81.0±0.8% HF rate** (best overall)

**Speedup**: HiDVFS achieves 3.44× speedup over GearDVFS (14.32±2.61s → 4.16±0.58s) with 50.4% energy reduction (128.4 kJ → 63.7 kJ).

### Statistical Analysis (Table 4 from Paper)

Mann-Whitney U test on key features' influence (parallel FFT execution):

**Priority** (task priority combinations):
- Makespan: 4.60s (low) → 5.04s (high), p=1.89e-02 (significant)
- Energy: 23549.88 → 23035.29 mJ, p=1.42e-01 (non-significant)
- Temperature: p=3.68e-04, Branch Misses: p=1.19e-15, Cache Misses: p=1.22e-24 (all significant)

**Cores** (number of cores):
- Makespan: 5.28s (low) → 3.75s (high), p=6.69e-06 (highly significant)
- Energy: 27017.90 → 16013.48 mJ, p=3.26e-25 (highly significant)
- Temperature: p=4.00e-01 (non-significant)

**Frequency** (frequency level):
- Makespan: 6.92s (low) → 2.62s (high), p=1.45e-283 (strongest influence)
- Energy: 29615.61 → 17083.91 mJ, p=6.03e-144 (strongest influence)
- Temperature: p=3.40e-03, Cache Misses: p=8.37e-10 (significant)

**Insight**: Frequency dominates makespan and energy optimization, followed by core count, while priority drives system overhead. This motivates the makespan-first reward and profiler agent's focus on frequency selection.

### Convergence and Action Analysis

**Convergence Time**:
- Single-agent: SARB converges at 12 epochs, zTT at 16 epochs
- Multi-agent: HiDVFS converges at 84 epochs (slower but more stable), MAMF at 23 epochs

**Action Patterns** (Finetuned, Seed 42):
- HiDVFS: 85.3% high-frequency (≥9), consistent core selection (85.7% using ≥5 cores)
- GearDVFS: 21.7% high-frequency, conservative core allocation (24.3% using ≥5 cores)

**Hardware Counters**:
- Branch Misses: HiDVFS 0.68×10^9 (27% lower than GearDVFS 1.03×10^9), indicating more predictable execution
- Cache Misses: HiDVFS 1.40×10^10 (higher due to aggressive frequency, but net performance gain from reduced makespan)

### Illustrative Decision-Making Example (Section 4.8 from Paper)

**Initial State** (5-core Jetson TX2, FFT benchmark):
- Core temperatures: [42°C, 48°C, 43°C, 47°C, 41°C]
- Previous episode: makespan=4.2s, energy=18.5J

**Step 1 - Thermal Agent**: Selects cooler cores {c1, c3, c5} (temps 42°C, 43°C, 41°C), avoiding hot cores c2, c4 (48°C, 47°C) to stay below 50°C limit

**Step 2 - Profiler Agent**: Given 3-core config, assigns frequency level 8 (1.4 GHz) balancing makespan reduction with thermal headroom

**Step 3 - Priority Agent**: Sets SCHED_FIFO priorities [90, 80, 70] to three parallel tasks, ensuring critical-path task executes first

**Execution**: Benchmark runs on {c1, c3, c5} at 1.4 GHz with assigned priorities. Post-execution: makespan=3.1s, energy=15.2J, temperatures=[44°C, 46°C, 45°C, 45°C, 43°C]

**Rewards**:
- Profiler: R1 = M_target/M = 2.5/3.1 = 0.81
- Thermal: R2 = 1-0.05|44-50| = 0.70 (within safe limits)
- Priority: R3 = (M_target-M)/M_target = (2.5-3.1)/2.5 = -0.24

Agents update Q-networks using these rewards, gradually learning coordinated makespan-first optimization.

## Notes

### Real-Time Systems Considerations

HiDVFS targets **soft real-time** OpenMP DAGs (makespan-first objective) rather than hard real-time systems requiring worst-case execution time (WCET) guarantees. The framework assumes:
- Aperiodic parallel tasks with static priorities (SCHED_FIFO)
- Makespan optimization with energy/temperature as secondary metrics
- Thermal fail-safe: If T > 50°C, frequency throttled to prevent thermal violations

**Limitations for Hard Real-Time**:
- No WCET analysis or schedulability guarantees
- RL-based decisions introduce latency (2ms), unsuitable for sub-millisecond deadlines
- Model-based predictions focus on average-case performance, not worst-case scenarios

### Comparison with Complementary Approaches

HiDVFS is part of a research lineage addressing DVFS from different angles:

1. **ZeroDVFS (2026)**: Zero-shot LLM-guided allocation enabling deployment without workload-specific profiling. Uses LLM semantic features (13 features from OpenMP code) for immediate inference. HiDVFS complements this with few-shot RL requiring profiling but achieving higher performance after training.

2. **GraphPerf-RT (2025)**: GNN surrogate for performance prediction (2-7ms inference, R²>0.95). Provides fast performance estimates used by MAMBRL-D3QN scheduler for makespan reduction. HiDVFS uses runtime profiling instead of GNN predictions, trading inference speed for direct measurement accuracy.

3. **FlowRL (2024)**: Flow-augmented few-shot RL using continuous normalizing flows for synthetic data generation. Achieves 35% higher frame rates on Jetson TX2 with zero-shot generalization. HiDVFS extends this with hierarchical multi-agent decomposition and model-based reward estimation.

4. **Feature-Aware (2025)**: Statistical learning for task-to-core allocation using feature screening. Identifies most predictive features for DVFS decisions. HiDVFS incorporates similar statistical analysis (Table 4) to motivate reward design.

### Platform-Specific Observations

**Jetson TX2 Advantages**:
- Fine-grained frequency scaling: 12 steps (345.6-2035.2 kHz) vs Intel RAPL's coarser adjustments
- Per-core DVFS control via sysfs (scaling_max_freq parameter)
- ARM64 heterogeneity (Denver 2 + Cortex-A57) representative of embedded scenarios
- Software-controlled frequency scaling suitable for RL exploration

**Training Exception (concom benchmark)**:
- During training: GearDVFS outperforms HiDVFS (0.36× makespan)
- During finetuning: HiDVFS overcomes this (1.94× speedup)
- Reason: Connected components algorithm initially benefits from conservative frequency policies during exploration, but HiDVFS learns better strategy after finetuning

### Reproducibility and Experimental Infrastructure

- **Platform**: NVIDIA Jetson TX2, Ubuntu 18.04, Linux kernel 4.9.337 (ARM64, SMP PREEMPT)
- **Cores**: 6 heterogeneous (cores 1-2: Denver 2 cluster, cores 0,3-5: Cortex-A57, core 0 reserved for root tasks)
- **Frequency Range**: 12 steps, 345,600 kHz to 2,035,200 kHz (levels 0-11)
- **Tools**: perf (profiling), cpufreq-info (DVFS state), sensors (temperature), cpuset (core binding)
- **Benchmarks**: BOTS suite (alignment, concom, fft, fib, floorplan, health, sort, sparselu, strassen, uts)
- **Variants**: 3 per benchmark (serial, omp-tasks, omp-tasks-tied)
- **Seeds**: 42, 123, 456 for multi-seed validation
- **Epochs**: 100 per phase (training, finetuning)
- **Hyperparameter Tuning**: Grid search, 10-12 hours offline on 16 GB RAM, 4-core Intel i7 server

### Future Work and Open Questions

1. **Scalability to More Cores**: Jetson TX2 has 5 usable cores (core 0 reserved). How does HiDVFS scale to systems with 16+ cores? Action space grows linearly but state space may explode.

2. **Hard Real-Time Extension**: Incorporate WCET analysis and schedulability tests to provide safety guarantees for hard real-time DAGs.

3. **Cross-Platform Generalization**: Multi-seed validation uses same platform (Jetson TX2). How does HiDVFS transfer to Jetson Orin NX, RubikPi, Intel i7 without retraining? (ZeroDVFS addresses this with zero-shot LLM approach)

4. **Dynamic DAG Workloads**: Current evaluation uses fixed BOTS benchmarks. How does HiDVFS adapt to dynamically arriving DAGs with varying structures?

5. **Energy-First Objective**: Current design prioritizes makespan (β=1). What happens with β=0 (energy-first) or β=0.5 (balanced)?

6. **Distributed Multi-Node Extension**: HiDVFS targets single-node multicore. How to extend to distributed systems with network communication and node-level heterogeneity?

7. **Neuromorphic/FPGA Deployment**: Server-client architecture offloads training to server. Can HiDVFS agents be deployed on neuromorphic hardware (Intel Loihi 2) or FPGAs for ultra-low latency?

## Bibdata

```bibtex
@article{pivezhandi2026hidvfs,
  title={HiDVFS: A Hierarchical Multi-Agent DVFS Scheduler for OpenMP DAG Workloads},
  author={Pivezhandi, Mohammad and Saifullah, Abusayeed and Jannesari, Ali},
  journal={arXiv preprint arXiv:2601.06425},
  year={2026}
}
```
