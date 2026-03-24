# ZeroDVFS: Zero-Shot LLM-Guided Core and Frequency Allocation for Embedded Platforms

**Citation Key**: `pivezhandi2026zerodvfs`

**Year**: 2026

**Type**: article

**Authors**: Pivezhandi, Mohammad and Banisharif, Mahdi and Saifullah, Abusayeed and Jannesari, Ali

**Venue**: arXiv preprint arXiv:2601.08166

---

## Key Points

- Model-based hierarchical multi-agent RL with accurate environment models enabling 20× faster convergence than model-free methods
- LLM-based semantic feature extraction from OpenMP source code enabling zero-shot deployment in under 5 seconds without workload-specific profiling
- Two collaborative agents decompose exponential action space (m^n) to linear complexity (m² + m + n), achieving 358ms decision latency
- 7.09× better energy efficiency and 4.0× better makespan compared to existing DVFS approaches
- Evaluated on NVIDIA Jetson TX2, Jetson Orin NX, RubikPi, and Intel Core i7 with BOTS and PolybenchC benchmarks
- Cross-platform transfer learning achieves R² scores of 0.80-0.90 without platform-specific profiling

## Abstract

Dynamic voltage and frequency scaling (DVFS) and task-to-core allocation are critical for thermal management and balancing energy and performance in embedded systems. Existing approaches either rely on utilization-based heuristics that overlook stall times, or require extensive offline profiling for table generation, preventing runtime adaptation.

Building upon hierarchical multi-agent scheduling, this work contributes model-based reinforcement learning with accurate environment models that predict thermal dynamics and performance states, enabling synthetic training data generation and converging 20× faster than model-free methods. The work introduces Large Language Model (LLM)-based semantic feature extraction that characterizes OpenMP programs through code-level features without execution, enabling zero-shot deployment for new workloads in under 5 seconds without workload-specific profiling.

Two collaborative agents decompose the exponential action space, achieving 358ms latency for subsequent decisions. The hierarchical MARL approach addresses the challenge of high-dimensional action space: a naive single-agent implementation would face m^n possible actions (approximately 2.1 × 10^9 for Jetson TX2 with 6 cores and 12 frequency levels), while the hierarchical decomposition reduces this to m² + m + n (54 actions for TX2).

Experiments on Barcelona OpenMP Tasks Suite (BOTS) and PolybenchC benchmarks across NVIDIA Jetson TX2, Jetson Orin NX, RubikPi, and Intel Core i7 demonstrate 7.09× better energy efficiency, 4.0× better makespan, and 358ms decision latency compared to existing power management techniques.

## Contributions

### 1. Model-Based Multi-Agent Reinforcement Learning for Few-Shot DVFS

The work integrates environment modeling into few-shot learning techniques through an off-policy MARL approach to enable efficient adaptation to new thermal scenarios with limited training data. The precise modeling framework reduces sample collection overhead, achieving 4.0× better mean makespan compared to heuristic baselines while converging 20× faster than model-free methods.

**Environment Model Architecture**: Five regression models evaluated (FCN, CNN, RNN, LSTM, Attention). FCN achieves best profiler accuracy (P_MSE=0.09%) with fewest parameters (714), while Conv1D balances accuracy and efficiency for thermal modeling. Both achieve sub-5ms inference on Jetson TX2. The models outperform state-of-the-art temperature prediction by over 6× in Mean Squared Error while maintaining model inference latencies under 5ms.

**Dyna-Q Planning Algorithm**: Combines direct RL (real environment interactions) with planning (synthetic data generation using learned models). Generates 5 synthetic rollouts per real transition, enabling 4× better sample efficiency. Training workflow: (1) Direct RL collects real transitions, (2) Environment model trains on real data every 10 episodes, (3) Planning generates synthetic transitions (5:1 ratio), (4) Policy trains on combined real + synthetic data.

### 2. LLM-Based Semantic Feature Extraction for Zero-Shot Deployment

The work introduces LLM-based semantic feature extraction that characterizes OpenMP programs through 13 code-level features without execution. Three state-of-the-art LLMs evaluated (DeepSeek-V3, Claude Sonnet, GPT-4o), achieving up to 73.8% inter-model agreement on dominant operation type and 59.5% on algorithmic complexity. This enables zero-shot deployment to new platforms with one-time extraction cost of $0.018 per program (using all 3 LLMs) or $0.0015 (using DeepSeek-V3 alone).

**Semantic Feature Taxonomy** (13 features across 3 categories):

*Memory Access Characteristics*: memory_access_pattern (unit_stride, non_unit_stride, random, mixed), spatial_locality (high, medium, low), temporal_locality (high, medium, low), cache_behavior_pattern (streaming, random, blocked, mixed), numa_sensitivity (high, medium, low)

*Algorithmic Characteristics*: algorithmic_complexity (O(n), O(n²), O(n³), O(n log n), other), dominant_operation (arithmetic, memory, logic, mixed), vectorization_potential (high, medium, low)

*Parallelization Characteristics*: data_dependency_type (none, loop_carried, cross_iteration, complex), false_sharing_risk (high, medium, low, none), load_balance_characteristic (uniform, irregular, dynamic), parallelization_overhead (low, medium, high), scalability_bottleneck (none, memory_bandwidth, synchronization, load_imbalance)

**Zero-Shot Prompting Strategy**: Uses zero-shot prompts without in-context examples to avoid bias, requests structured JSON responses with explicit value constraints for each feature, applies identical prompts across all three LLMs for fair comparison, achieves 100% JSON parsing success rate across 126 API calls (42 benchmarks × 3 LLMs).

**Cost-Effectiveness**: Feature extraction completes in under 5 seconds per program versus 8-12 hours for exhaustive profiling. Monetary cost: $0.0015-$0.018 per program. One-time extraction enables unlimited cross-platform predictions. Profiling 1,000 programs costs $400,000 (manual labor at $50/hour × 8 hours) versus $18 (LLM extraction with all 3 models), representing 22,222× cost reduction.

### 3. Thermal-Aware Accurate Modeling of Embedded Processors

The work develops thermal-aware accurate modeling that captures thermal dynamics of embedded processors under varying workloads. The proposed regression models outperform state-of-the-art temperature prediction by a factor of over 6 in Mean Squared Error (MSE), with model inference latencies under 5ms.

**Model Selection**: FCN selected for profiler modeling (best latency-accuracy balance), Conv1D selected for thermal modeling (best temperature prediction accuracy). Hybrid approach achieves overall inference latency under 5ms while maintaining R² > 0.96 across all metrics.

**Prediction Accuracy**: Temperature MSE = 0.401 (FCN), 0.446 (Conv1D) vs 2.500 (prior work), representing 6-7× improvement. Profiler MSE = 0.089 (FCN), 0.167 (Conv1D). Models predict branch misses, cache misses, context switches, per-core temperatures across 100-200 timestamps with high fidelity.

### 4. Comprehensive Experimental Evaluation with Decision Latency Analysis

The work evaluates the approach through extensive experiments using OpenMP benchmarks (BOTS and PolybenchC, 42 total) on NVIDIA Jetson TX2, Jetson Orin NX, RubikPi, and Intel Core i7. The model-based approach achieves 358ms inference latency for subsequent decisions and 8,300× faster first-decision latency than table-based profiling, while achieving 7.09× better energy efficiency than the Linux ondemand governor.

**Decision Latency Breakdown** (Python implementation on Jetson TX2):
- Profiler Model forward pass: 121.6ms
- Thermal Model forward pass: 122.2ms
- Policy Network forward pass: 122.3ms
- Python overhead: ~8ms
- **Total RL inference**: 358ms

**First-Decision Latency** (for new benchmarks):
- T_total = T_LLM + T_static + T_RL
- GPT-4o: 3.48s (3.07s LLM + 0.05s static + 0.358s RL)
- Claude Sonnet: 5.77s (4.12s LLM + 0.05s + 0.358s)
- DeepSeek-V3: 8.05s (7.64s LLM + 0.05s + 0.358s)

**Speedup vs Table-Based Profiling**:
- First-decision speedup: 8,300× (3.48s vs 27,000s = 7.5 hours)
- Subsequent-decision speedup: 80,000× (358ms vs 7.5 hours)
- Production C++ deployment (future work): Expected sub-10ms based on TensorRT FP16 speedups

**Energy Efficiency Results** (BOTS FFT on Jetson TX2):
- ZeroDVFS: Energy = 9.1mJ, Makespan = 1.13s, Temp = 42.1°C
- zTT baseline: Energy = 31.2mJ, Makespan = 1.93s, Temp = 43.6°C
- Precise scheduler: Energy = 75.5mJ, Makespan = 5.96s, Temp = 44.0°C
- **Improvements**: 7.09× better energy efficiency vs Precise, 4.0× better makespan

## Methodology

### Hierarchical Multi-Agent RL Architecture

**Action Space Decomposition**: Naive single-agent approach requires m^n actions (m cores, n frequencies). For Jetson TX2 (m=6, n=12): 6^12 ≈ 2.1 × 10^9 actions (intractable). Hierarchical decomposition introduces two agents: Profiler Agent and Temperature Agent, reducing to m² + m + n = 6² + 6 + 12 = 54 actions (reduction factor ≈ 10^7).

**Profiler Agent**: Assesses workload performance to minimize energy and makespan. Action: a_profiler = {a_cores, a_freq} determines number of cores and operating frequency. State: s_profiler = {s_energy, s_workload} includes energy consumption, makespan, core utilizations, frequency states (18 dimensions). Reward: Exponential function based on energy and makespan deviations from powersave/performance governor targets.

**Temperature Agent**: Prioritizes cores based on temperature to prevent heat concentration. Action: a_temp assigns priority levels to cores (m × m choices for m cores). State: s_temp includes per-core temperature readings from 8 thermal zones (Jetson TX2). Reward: Linear function maintaining temperatures below 50°C threshold (TX2 thermal throttling at 70°C).

**D3QN Network Architecture**: Each agent uses Dueling Double Deep Q-Network. Dueling architecture: Q(s,a) = V(s) + (A(s,a) - mean(A(s,·))) separates value and advantage streams. Double Q-learning: Action selection from online network, evaluation from target network (reduces overestimation). Network complexity: ~10,000 total parameters for 2 agents, enabling sub-10ms inference on ARM processors.

### Reward Function Definitions

**Profiler Reward** (exponential structure):
- r_energy = exp(-c_st × (E_A - E_Psav) / (c_th × E_Psav)) × 2 - 1
- r_makespan = exp(-c_st × (makespan_A - makespan_Perf) / (c_th × makespan_Perf)) × 2 - 1
- r_profiler = (r_energy + r_makespan) / 2
- Threshold factor c_th = 0.3 (penalization boundary), steepness factor c_st = 0.5 (gradient control)

**Temperature Reward** (linear structure):
- Per-core: r_temp^i = {-1 if temp_i > 50°C, 50 - temp_i otherwise}
- Overall: r_temp = (1/m) × Σ r_temp^i
- Incentivizes keeping all cores cool, preventing thermal throttling

### Model-Based RL with Dyna-Q Planning

**Algorithm Workflow**:
1. **Direct RL Phase**: Agents select actions using D3QN policies, execute in real environment, observe next states and rewards, store transitions in replay buffers B_profiler and B_temp
2. **Model Training**: Every 10 episodes, train environment model M_profiler using samples from B_profiler (profiler state model), infer thermal synthetic data from profiler predictions (no separate thermal model)
3. **Planning Phase**: For each real step, generate ζ = 5 synthetic steps: (a) Generate action a'_profiler (randomly or from policy), (b) Predict s''_profiler, r'_profiler using M_profiler, (c) Derive s''_temp, r'_temp from s''_profiler, (d) Store synthetic transitions in B'_profiler and B'_temp
4. **Agent Training**: Sample minibatches from combined real + synthetic buffers, train D3QN_profiler and D3QN_temp

**Convergence Speed**: Model-based achieves 95% optimal threshold at episode 20, model-free requires 40 episodes (2× slower). Sample efficiency: 1,200 real transitions (model-based) vs 4,800 (model-free), 4× improvement.

### Cross-Platform Transfer Learning

**Two-Stage Transfer Methodology**:

*Stage 1: Zero-Shot Transfer*: Environment model trained on source platform (Jetson TX2) directly applied to target platform without modification. Works because: (a) Workload characteristics (algorithmic complexity, memory patterns, parallelism) are ISA-agnostic, (b) Relative performance trends (frequency scaling impact, core allocation effects) generalize across platforms, (c) Neural network representations capture underlying physical relationships.

*Stage 2: Few-Shot Fine-Tuning*: Transfer model refined using 5, 10, or 50 samples from target platform. Fine-tuning adapts: (a) Frequency scaling coefficients to platform-specific P-states, (b) Thermal prediction to different cooling solutions, (c) Energy consumption estimates to platform TDP characteristics.

**Feature Normalization**:
- Platform-Agnostic Features (high transferability): Algorithmic complexity O(n), memory access locality, parallelism structure, branch prediction patterns
- Platform-Specific Features (require adaptation): Frequency (normalized: f_norm = (f - f_min) / (f_max - f_min)), temperature (normalized: T_norm = (T_throttle - T_current) / (T_throttle - T_ambient)), energy (normalized: P_norm = P_measured / P_TDP), core count (normalized: cores_norm = cores_used / cores_total)

**Transfer Results**: Zero-shot achieves 64.5% MAPE (Orin NX), 73.2% MAPE (RubikPi) vs 10.9% MAPE (source TX2). R² scores: 0.90 (Orin NX), 0.80 (RubikPi). 10-shot fine-tuning reduces to 60.9% and 69.2% respectively. Important: High MAPE with preserved rank correlation (Spearman ρ = 0.82) enables effective scheduling decisions.

### LLM Feature Extraction Pipeline

**Two-Stage Pipeline**:
1. **Static Analysis (Tree-sitter)**: Extracts 17 syntactic features in ~50ms: control flow metrics (loop depth, nesting complexity), OpenMP directive counts (parallel regions, task constructs), synchronization primitives (critical sections, atomic operations), variable scope classifications (shared, private, reduction)
2. **Semantic Analysis (LLMs)**: Extracts 13 semantic features via zero-shot prompting in 3-8 seconds: memory access characteristics (5 features), algorithmic characteristics (3 features), parallelization characteristics (5 features)

**Multi-Model Agreement**: DeepSeek-GPT-4o pair: 61.9% average agreement. High-consensus features: dominant_operation (73.8%), algorithmic_complexity (59.5%). Low-agreement features: false_sharing_risk (14.3%), cache_behavior_pattern (16.7%). Gradient boosting predictor learns importance weights, automatically down-weighting unreliable features.

**Feature Encoding**: Ordinal encoding for inherent ordering (low=0, medium=1, high=2), integer encoding for nominal categories (memory_access_pattern: unit_stride=0, non_unit_stride=1, random=2, mixed=3), algorithmic_complexity: O(n)=0, O(n log n)=1, O(n²)=2, O(n³)=3, other=4.

## Results

### Performance Ranking Across 11 Baselines

**Final Performance Ranking by Makespan** (BOTS FFT, Jetson TX2):
1. ZeroDVFS (MAMBRL D3QN): 1.13s — Model-Based Multi-Agent
2. HiDVFS_S: 1.33s — Model-Free Single-Agent
3. MAML: 1.75s — Model-Free Multi-Agent
4. zTT: 1.88s — Model-Free Single-Agent
5. MAMF: 2.14s — Model-Free Multi-Agent
6. HiDVFS: 2.58s — Model-Free Multi-Agent
7. DynaQ: 3.21s — Model-Based Single-Agent
8. MBPG (PlanGAN): 3.87s — Model-Based Single-Agent
9. SARB: 4.62s — Model-Free Single-Agent
10. Precise Scheduler: 5.96s — Table-Based Heuristic
11. GearDVFS: 9.81s — Model-Free Single-Agent

**Key Insights**:
- Multi-Agent > Single-Agent: Top 4 methods all use multi-agent architectures
- Model-Based Acceleration: MAMBRL D3QN (1.13s) vs MAMFRL (2.47s), 2.2× improvement
- D3QN Improvement: MAMBRL D3QN (1.13s) vs base MAMBRL (1.88s), 40% better
- Learned > Table-Based: All learned policies (top 9) outperform Precise (5.96s)

### Energy Efficiency and Thermal Management

**Normalized Comparison** (ZeroDVFS baseline = 1.0):
- Precise scheduler: 7.09× energy, 4.00× makespan, 1.00× temperature
- zTT baseline: 3.43× energy, 1.71× makespan, 1.00× temperature
- ZeroDVFS: 1.00× energy (9.1mJ), 1.00× makespan (1.13s), 1.00× temperature (42.1°C)

**Thermal Violations**: Zero thermal violations across all experiments on all platforms. Conservative thermal reward function (50°C threshold on TX2 with 70°C throttling threshold) maintains 20°C safety margin.

**Convergence Analysis**: Model-based achieves low makespan (1-2s) with occasional exploration spikes. Model-free (GearDVFS) exhibits high variance (2-15s makespan, 20-180mJ energy). Variance: Model-free std = 45.2, model-based std = 28.7 (60% reduction).

### Cross-Platform Transfer Learning

**Zero-Shot Transfer Results** (Source: TX2 → Target):
- TX2 (source): 10.9% MAPE, R² = 0.99
- Orin NX (zero-shot): 64.5% MAPE, R² = 0.90
- RubikPi (zero-shot): 73.2% MAPE, R² = 0.80

**N-Shot Fine-Tuning** (samples → MAPE reduction):
- Orin NX: 0→64.5%, 10→60.9% (5.6% reduction), 20→62.3% (slight increase, variance)
- RubikPi: 0→73.2%, 10→69.2% (5.5% reduction), 20→67.2% (8.2% total reduction)

**Practical Deployment**: Zero-shot provides immediate deployment (8,300× faster than profiling). Accuracy improves incrementally with 10-20 samples (minutes vs hours). Conservative thermal limits (50% max frequency) during initial adaptation ensure safety despite prediction errors.

### LLM Feature Contribution Analysis

**Prediction Accuracy Across Feature Configurations** (Jetson TX2):
- Baseline_static (17 Tree-sitter features): R² = 0.944, MAPE = 24.1%
- Baseline (17 static + 22 hardware counters): R² = 0.944, MAPE = 24.7%
- All (17 static + 22 hardware + 13 LLM): R² = 0.944, MAPE = 24.2%

**Statistical Testing**: Paired t-test (baseline vs all): t = -1.67, p = 0.156. Conclusion: No significant difference at α = 0.05 when hardware counters available. LLM features provide redundant information on known benchmarks.

**Feature Importance Ranking** (Top 15 by XGBoost gain metric):
1. energy_main_j: 28.52%
2. context_switches: 22.27%
3. energy_system_j: 16.59%
4. instructions: 7.15%
5. energy_gpu_j: 4.80%
...
10. cache_behavior_pattern (LLM): 1.03%
15. data_dependency_type (LLM): 0.57%

**Interpretation**: Hardware counters dominate (80%+ importance) when available because they directly measure runtime behavior. LLM features (5% total importance) encode predictive properties obtainable pre-execution. Critical distinction: Semantic features enable zero-shot prediction for unseen programs where hardware counters unavailable.

**Prediction Accuracy by Benchmark Category** (Jetson TX2):
- Linear Algebra BLAS (7 benchmarks): MAPE = 18.5%, R² = 0.97 (highest accuracy)
- Regular BOTS (6 benchmarks): MAPE = 19.2%, R² = 0.96
- Data Mining (2 benchmarks): MAPE = 16.9%, R² = 0.98
- Recursive BOTS (6 benchmarks): MAPE = 28.4%, R² = 0.91 (lowest accuracy due to dynamic iteration counts)

### Ablation Study: Model-Based vs Model-Free

**Controlled Comparison** (MAMBRL D3QN vs MAMFRL D3QN, identical architecture except environment model):
- Initial Convergence: Model-based at episode 20, model-free at episode 40 (2× slower)
- Sample Efficiency: Model-based uses 1,200 real transitions, model-free uses 4,800 (4× more data)
- Variance: Model-free std = 45.2, model-based std = 28.7 (improved stability)
- Final Performance: Both reach comparable (within 2%): MAMBRL 3.20s/9.1mJ, MAMFRL 3.26s/9.4mJ (after 40 episodes)
- Computational Overhead: Model training adds 45% of total training time (805s over 20 episodes), but amortized by 4× fewer real samples and 2× faster convergence

## Notes

### Real-Time Systems Considerations

**Scope**: ZeroDVFS designed for soft real-time and best-effort workloads (average-case optimization), NOT hard real-time systems requiring formal WCET guarantees. Current system provides no formal WCET bounds, deadline guarantees, schedulability analysis, or timing correctness proofs.

**Intended Applications**: Mobile video processing, IoT sensor fusion, background analytics, adaptive vision pipelines. Tasks with soft deadlines (occasional misses tolerable), energy efficiency and thermal management critical, dynamic workload phases requiring runtime adaptation.

**Inappropriate Applications**: Safety-critical hard real-time (automotive brake controllers, medical devices, avionics), mixed-criticality systems requiring WCET guarantees, applications governed by real-time standards (AUTOSAR, DO-178C), time-triggered architectures.

**Decision Latency Constraints**: Current Python implementation: 358ms per decision. Applicable to coarse-grained workloads (1-5 second tasks, 7-36% overhead). Inappropriate for fine-grained control loops at 100Hz-1000Hz (10ms-1ms periods). All reported results include this 358ms overhead in end-to-end makespan measurements.

**Thermal Fail-Safe Mechanisms**: (1) Conservative constraints during zero-shot deployment: 50% max frequency, 50% max cores, thermal threshold 40°C vs 50°C, epsilon=0. (2) Prediction uncertainty tracking: If σ_pred > τ_threshold, reject prediction and fallback to Linux ondemand. (3) Hardware monitoring daemon: 100ms sampling, emergency fallback to powersave governor if temp > 65°C. (4) Phased relaxation: After 5/10/20 samples with MAPE < 50%, progressively remove constraints.

### Comparison with Precise Scheduler (Baseline Equity)

**Important Clarification**: Precise Scheduler [7] is a hard real-time federated DAG scheduler for Mixed-Criticality Systems. Comparison is inherently asymmetrical. Precise guarantees: Formal worst-case timing correctness, deadline satisfaction under maximum microarchitectural interference, mathematically provable schedulability, safety for mixed-criticality workloads.

**Energy Difference Interpretation**: Precise consumes 75.5mJ vs ZeroDVFS 9.1mJ (66.4mJ delta). This is NOT algorithmic inefficiency. It represents the physical cost of worst-case safety margins: higher voltage/frequency to ensure deadline satisfaction under worst-case execution paths, thermal headroom to prevent throttling during worst-case power spikes, schedulability slack from conservative core allocation, the energy cost of predictability.

**Tradeoff Quantification**: For soft real-time applications tolerating occasional misses, ZeroDVFS offers 7× energy savings. For hard real-time systems requiring formal guarantees, Precise Scheduler's 7× energy cost is justified. Comparison demonstrates that average-case optimization and worst-case guarantees occupy different points on the Pareto frontier.

### Related Papers and Research Lineage

**Prior Work by Authors**:
- HiDVFS (pivezhandi2026hidvfs): Hierarchical multi-agent DVFS foundation, this work builds upon
- GraphPerf-RT (pivezhandi2025graphperf): Graph-driven performance modeling, provides performance prediction baseline
- Feature-Aware (pivezhandi2025feature): Statistical learning for task allocation, complements DVFS
- FlowRL (pivezhandi2024flowrl): Flow-augmented few-shot RL, few-shot learning techniques

**Cited Baselines**:
- Precise Scheduler [7]: Bhuiyan et al. ECRTS 2023, table-based exhaustive profiling
- zTT [28]: Kim et al. MobiSys 2021, model-free single-agent RL
- GearDVFS [36,37]: Lin et al. MobiCom 2023, workload-aware DVFS
- Temperature Modeling [23]: Hosseinimotlagh et al. RTSS 2021, state-of-the-art thermal prediction (6× worse MSE than this work)

### Innovations and Technical Novelty

**Key Innovations**:
1. **First to combine**: Model-based RL + hierarchical MARL + LLM semantic features for DVFS
2. **Action space reduction**: 10^7× reduction from m^n to m² + m + n via agent decomposition
3. **Zero-shot workload characterization**: LLM features replace benchmark-specific identifiers, enabling generalization to unseen programs
4. **Few-shot cross-platform transfer**: Zero-shot deployment with 80-90% R² variance capture, fine-tuning with 10-20 samples
5. **Thermal-aware modeling**: 6× better temperature prediction than prior work while maintaining <5ms inference
6. **Decision latency analysis**: Comprehensive breakdown showing 8,300× speedup vs table-based profiling

### Limitations and Future Work

**Current Limitations**:
- Python prototype: 358ms decision latency limits to coarse-grained workloads (1-5s tasks)
- No deadline-awareness: Reward functions optimize energy/makespan, not deadline satisfaction
- LLM API dependency: Requires internet connectivity, incurs per-token costs (mitigated by pre-extraction)
- Zero-shot high MAPE: 64-73% on new platforms (though rank correlation preserved at ρ=0.82)
- Soft real-time only: No formal WCET guarantees or schedulability analysis

**Future Directions** (explicitly listed in paper conclusion):
1. **Formal real-time analysis**: WCET bounds, deadline guarantees, thermal safety proofs with fail-safe mechanisms
2. **Leave-one-benchmark-out validation**: True zero-shot capability evaluation (hold out entire program during training)
3. **Production deployment**: C++ rewrite + TensorRT optimization → sub-10ms latency target
4. **Concurrent workloads**: Multi-program scheduling with interference modeling
5. **Multi-file analysis**: LLM analysis across multiple source files for large applications
6. **LLM fine-tuning**: Domain-specific models trained on HPC/embedded code corpora
7. **GPU offloading**: Extend to heterogeneous CPU-GPU scheduling
8. **Recent deep RL baselines**: Comparison with PPO, SAC, TD3, Rainbow DQN
9. **Broader platform evaluation**: ARM big.LITTLE, Intel 12th-gen hybrid architectures

### Reproducibility and Availability

**Experimental Infrastructure**:
- Platforms: NVIDIA Jetson TX2 (Ubuntu 18.04), Intel Core i7 (Ubuntu 22.04), Jetson Orin NX, RubikPi
- Kernel: Preemptible Linux 4.9.337, FIFO real-time scheduler
- Power management: Intel p-state and c-state disabled, ACPI-Freq for frequency control
- Energy measurement: In-kernel IIO power monitoring (in_power_input sysfs), 10ms sampling
- Benchmarks: BOTS (12 programs), PolybenchC (30 programs), 42 total
- LLMs: DeepSeek-V3 (deepseek-chat), Claude Sonnet (claude-3-5-sonnet), GPT-4o (gpt-4o-2024-08-06)
- Training: Adam optimizer, learning rate 0.001, discount γ=0.99, ε-greedy exploration (1.0→0.01)

**Dataset Scale**: 47,040 total samples (TX2: 20,160, RubikPi: 26,880). Each benchmark × all frequency-core configs × 8 repetitions. Train/val/test split: 70/10/20 with benchmark-stratified sampling.

**Implementation Details**: PyTorch for neural networks, XGBoost for LLM feature prediction (100 estimators, depth 6), Tree-sitter for static analysis, OpenAI/Anthropic/DeepSeek APIs for LLM extraction.

## Bibdata

```bibtex
@article{pivezhandi2026zerodvfs,
  title={ZeroDVFS: Zero-Shot LLM-Guided Core and Frequency Allocation for Embedded Platforms},
  author={Pivezhandi, Mohammad and Banisharif, Mahdi and Saifullah, Abusayeed and Jannesari, Ali},
  journal={arXiv preprint arXiv:2601.08166},
  year={2026}
}
```
