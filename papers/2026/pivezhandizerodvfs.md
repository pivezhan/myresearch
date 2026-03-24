# ZeroDVFS: Zero-Shot LLM-Guided Autonomous Agent for Energy-Aware Resource Allocation in Embedded Systems

**Citation Key**: `pivezhandizerodvfs`

**Year**: 2026

**Type**: inproceedings

**Authors**: Pivezhandi, Mohammad and Banisharif, Mahdi and Saifullah, Abusayeed

**Venue**: Agentic AI in the Wild: From Hallucinations to Reliable Autonomy (ICLR 2026 Workshop)

---

## Key Points

- Workshop paper presenting ZeroDVFS as an autonomous agent system demonstrating reliable LLM control in safety-critical embedded systems without hallucinations
- LLM-based semantic feature extraction (13 code-level features) from OpenMP programs enabling zero-shot deployment in under 5 seconds without workload-specific profiling
- Model-based hierarchical multi-agent reinforcement learning achieving 7.09× better energy efficiency and 4.0× better makespan improvement compared to existing DVFS approaches
- Addresses "Agentic AI in the Wild" workshop theme by showing LLMs can reliably guide autonomous scheduling decisions in resource-constrained embedded platforms
- Demonstrates cross-platform transfer learning with zero-shot deployment achieving R² scores of 0.80-0.90 for makespan prediction without platform-specific profiling
- Evaluated on 4 platforms (NVIDIA Jetson TX2, Jetson Orin NX, RubikPi, Intel Core i7) using 42 OpenMP benchmarks (BOTS + PolybenchC)

## Abstract

**Note**: This is a workshop paper (ICLR 2026 Workshop on "Agentic AI in the Wild: From Hallucinations to Reliable Autonomy") presenting the autonomous agent aspects of ZeroDVFS. The full technical paper is available as pivezhandi2026zerodvfs (arXiv:2601.08166).

This workshop paper presents ZeroDVFS as an autonomous agent system that addresses a critical challenge in agentic AI: achieving reliable, hallucination-free LLM control in safety-critical embedded systems. Traditional RL-based DVFS schedulers require extensive profiling and training for each workload-platform combination, limiting practical deployment. ZeroDVFS demonstrates that large language models can reliably extract semantic features from OpenMP source code and guide autonomous scheduling decisions without hallucinations, enabling zero-shot deployment without workload-specific profiling.

The key innovation is LLM-based semantic feature extraction that captures code-level characteristics (dominant operations, parallelization patterns, data dependencies) invisible to traditional profilers. Three state-of-the-art LLMs (DeepSeek-V3, Claude Sonnet 4, GPT-4o) extract 13 semantic features from OpenMP programs using zero-shot prompting without in-context examples. These features enable a hierarchical multi-agent RL system (Profiler Agent + Temperature Agent) to make autonomous core and frequency allocation decisions with 358ms decision latency in Python (expected sub-10ms in C++ production).

The autonomous agent framework demonstrates reliability through:
1. **Hallucination Prevention**: Inter-model agreement analysis shows 73.8% consensus on dominant operation identification across three LLMs, with disagreements concentrated in semantically similar categories (e.g., "compute" vs "memory")
2. **Safety-Critical Performance**: Zero thermal violations across all experiments (50°C limit maintained), demonstrating safe autonomous operation
3. **Zero-Shot Deployment**: Cross-platform transfer achieves R²=0.80-0.90 for makespan prediction without platform-specific profiling, enabling immediate deployment on new hardware

Evaluated on 4 platforms (NVIDIA Jetson TX2, Jetson Orin NX, RubikPi, Intel Core i7) using 42 OpenMP benchmarks (BOTS + PolybenchC), ZeroDVFS achieves 7.09× better energy efficiency and 4.0× better makespan improvement compared to existing DVFS approaches. Decision latency (358ms in Python, expected sub-10ms in C++) is negligible compared to application execution times (seconds to minutes), validating practical deployment feasibility.

This work contributes to the "Agentic AI in the Wild" theme by demonstrating that LLMs can move beyond text generation to reliably guide autonomous decisions in safety-critical embedded systems, where hallucinations could cause thermal violations or system failures.

## Contributions

1. **LLM-Guided Autonomous Agent Framework**: Demonstrates reliable LLM control in safety-critical embedded systems by extracting semantic features from OpenMP source code to guide autonomous DVFS decisions. Three LLMs (DeepSeek-V3, Claude Sonnet 4, GPT-4o) achieve 73.8% inter-model agreement on dominant operation classification, preventing hallucinations through ensemble consensus.

2. **Hallucination Prevention through Feature-Level Grounding**: Unlike free-form LLM generation prone to hallucinations, ZeroDVFS grounds LLMs in structured feature extraction (13 semantic features across 3 categories: operational, parallelization, structural). Zero-shot prompting without in-context examples demonstrates robustness, while disagreements concentrate in semantically similar categories rather than random hallucinations.

3. **Zero-Shot Cross-Platform Deployment**: Enables immediate deployment on new embedded platforms without retraining or profiling. Cross-platform transfer learning achieves R²=0.80-0.90 for makespan prediction (TX2→Orin NX: 64.5% MAPE, TX2→RubikPi: 73.2% MAPE), demonstrating autonomous agent's ability to generalize across hardware configurations.

4. **Safe Autonomous Operation in Safety-Critical Systems**: Demonstrates zero thermal violations across all experiments, validating that LLM-guided autonomous agents can operate safely in resource-constrained embedded systems. Thermal fail-safe mechanisms (50°C limit) prevent dangerous states even if LLM features are imperfect.

## Methodology

### LLM as Autonomous Feature Extractor

**Semantic Feature Extraction Pipeline**:
1. **Code Parsing**: Tree-sitter extracts OpenMP directives, parallel regions, task dependencies, loop structures
2. **LLM Prompting**: Zero-shot prompting (no in-context examples) extracts 13 semantic features:
   - **Operational** (5 features): Dominant operation (compute/memory/IO/mixed), compute intensity, memory access pattern, IO intensity, operation granularity
   - **Parallelization** (5 features): Parallelization type, task granularity, synchronization overhead, load balance, critical path length
   - **Structural** (3 features): Control flow complexity, data dependencies, resource contention likelihood
3. **Multi-Model Consensus**: DeepSeek-V3, Claude Sonnet 4, GPT-4o provide independent feature estimates
4. **Reliability Validation**: Inter-model agreement analysis (73.8% consensus on dominant operation) prevents hallucinations

**Hallucination Prevention**:
- Structured output format (categorical features + numerical scores 1-5) prevents free-form hallucinations
- Disagreements concentrated in semantically similar categories (e.g., "compute" vs "memory" for mixed workloads)
- Cost: $0.018 per program for all three LLMs (negligible compared to profiling overhead)

### Hierarchical Multi-Agent RL with LLM Features

**Two-Agent Decomposition**:
- **Profiler Agent**: Uses LLM semantic features + runtime profiling to select (cores, frequency)
- **Temperature Agent**: Uses per-cluster temperatures to select temperature-safe core combinations

**Action Space Reduction**: From m^n (2.1×10^9 for 5 cores, 12 frequencies) to m²+m+n (54 for 5 cores, 12 frequencies, 3 core combinations)

**Model-Based RL**: Environment models enable 20× faster convergence than model-free methods through Dyna-Q planning (5 synthetic steps per real step)

### Zero-Shot Cross-Platform Transfer

**Feature Normalization**:
- **Platform-Agnostic Features**: LLM semantic features (operation type, parallelization pattern) remain constant across platforms
- **Platform-Specific Features**: Frequency, core count, temperature normalized by platform characteristics

**Transfer Protocol**:
1. Train on source platform (e.g., Jetson TX2) using LLM features + runtime profiling
2. Deploy on target platform (e.g., Jetson Orin NX) using same LLM features
3. Optional few-shot fine-tuning (10-50 episodes) for improved accuracy

**Results**: Zero-shot R²=0.80-0.90, few-shot R²>0.95 (comparable to full training)

### Autonomous Decision Latency

**Breakdown** (Python implementation):
- Tree-sitter parsing: ~50ms
- LLM API calls (3 models): ~300ms (parallel, network-bound)
- Feature aggregation: ~8ms
- RL inference: ~5ms
- **Total**: 358ms

**Production Deployment** (C++ expected):
- Tree-sitter parsing: ~5ms
- LLM features (cached): ~0ms (pre-computed or on-demand with sub-10ms latency)
- Feature processing: ~1ms
- RL inference: ~2ms
- **Expected Total**: <10ms

Negligible compared to application execution (seconds to minutes).

## Results

### Autonomous Agent Performance

**Overall Performance** (4 platforms, 42 benchmarks):
- **Energy Efficiency**: 7.09× better than Precise Scheduler baseline
- **Makespan**: 4.0× better than existing DVFS approaches
- **Thermal Safety**: Zero violations across all experiments (50°C limit maintained)
- **Decision Latency**: 358ms (Python), expected sub-10ms (C++ production)

### LLM Feature Extraction Reliability

**Inter-Model Agreement Analysis** (3 LLMs on 42 benchmarks):
- **Dominant Operation**: 73.8% consensus across DeepSeek-V3, Claude Sonnet 4, GPT-4o
- **Parallelization Type**: 81.2% consensus
- **Load Balance**: 68.5% consensus (most subjective feature)

**Disagreement Analysis**:
- Disagreements concentrated in semantically similar categories (e.g., "compute-intensive" vs "mixed compute-memory")
- No random hallucinations observed (no features outside defined categories)
- Ensemble voting resolves disagreements, improving robustness

**Cost Analysis**:
- $0.006 per program per LLM × 3 LLMs = $0.018 per program
- Negligible compared to profiling overhead (minutes of execution)
- One-time cost for each program (features cached for reuse)

### Zero-Shot Cross-Platform Transfer

**Cross-Platform Makespan Prediction** (without fine-tuning):

| Source | Target | Zero-Shot R² | Zero-Shot MAPE | Few-Shot R² (50 episodes) |
|--------|--------|--------------|----------------|---------------------------|
| TX2 | Orin NX | 0.81 | 64.5% | 0.96 |
| TX2 | RubikPi | 0.80 | 73.2% | 0.95 |
| TX2 | Intel i7 | 0.85 | 58.3% | 0.97 |

**Insight**: LLM semantic features (platform-agnostic) enable immediate deployment on new platforms with R²>0.80, demonstrating autonomous agent's generalization capability.

### Safety-Critical Operation Validation

**Thermal Violations**: Zero across all experiments (4 platforms × 42 benchmarks × multiple seeds)

**Fail-Safe Mechanisms**:
- Hard temperature limit (50°C): Frequency throttled if exceeded
- Conservative default policy: If LLM features unavailable, revert to safe conservative frequency

**Reliability Under LLM Uncertainty**:
- When LLM models disagree (26.2% of cases for dominant operation), ensemble voting selects majority
- If no majority, default to "mixed" category (safest assumption)
- No system failures observed due to LLM feature errors

## Notes

### Addressing "Agentic AI in the Wild" Workshop Theme

This work directly addresses the workshop's focus on **"From Hallucinations to Reliable Autonomy"** by demonstrating:

1. **Hallucination Prevention**: Structured feature extraction (13 features, categorical + numerical) prevents free-form hallucinations. Inter-model agreement (73.8%) validates reliability.

2. **Safety-Critical Deployment**: Zero thermal violations across all experiments demonstrate safe autonomous operation in embedded systems where hallucinations could cause system failures.

3. **Real-World Constraints**: Decision latency (358ms Python, sub-10ms C++ expected), cost ($0.018 per program), and cross-platform generalization (R²>0.80 zero-shot) validate practical deployment feasibility.

4. **Autonomous Decision-Making**: LLMs move beyond text generation to guide resource allocation decisions in safety-critical systems, demonstrating reliable agentic AI in the wild.

### Relationship to Full Paper (pivezhandi2026zerodvfs)

This workshop paper focuses on the **autonomous agent and hallucination prevention aspects** of ZeroDVFS, while the full arXiv paper (pivezhandi2026zerodvfs, arXiv:2601.08166) provides:
- Complete technical details of model-based hierarchical MARL
- Comprehensive experimental evaluation (decision latency breakdown, ablation studies)
- Thermal-aware modeling with 6× better temperature prediction accuracy
- Full algorithmic details and reproducibility information

**Workshop Contribution**: Demonstrates LLM-guided autonomous agents for safety-critical embedded systems, addressing hallucination concerns through structured feature extraction and multi-model consensus.

### Comparison with Other Agentic AI Approaches

**Traditional Agentic AI** (e.g., LLM-based software engineering agents):
- Free-form natural language generation prone to hallucinations
- Difficult to validate correctness in safety-critical systems
- Limited grounding in structured outputs

**ZeroDVFS Autonomous Agent**:
- Structured feature extraction (13 features) prevents hallucinations
- Multi-model consensus (73.8% agreement) validates reliability
- Safety-critical validation (zero thermal violations)
- Quantitative performance metrics (7.09× energy efficiency, 4.0× makespan)

### Limitations and Future Work

1. **LLM Dependency**: Requires API access to commercial LLMs (DeepSeek-V3, Claude, GPT-4). Future work: Lightweight open-source LLMs for embedded deployment.

2. **Code-Level Features Only**: Current approach extracts features from source code. Runtime behavior (cache patterns, branch predictions) not captured. Future work: Hybrid LLM + runtime profiling.

3. **OpenMP-Specific**: Feature extraction tailored to OpenMP directives. Future work: Extend to other parallel programming models (MPI, CUDA, OpenCL).

4. **Cost at Scale**: $0.018 per program feasible for small datasets, but cost scales linearly. Future work: Amortize LLM calls across similar programs through program clustering.

5. **Hallucination Detection**: Current approach uses multi-model consensus. Future work: Automated hallucination detection through runtime validation (predicted features vs observed behavior).

### Deployment Recommendations

**When to Use ZeroDVFS Autonomous Agent**:
- **Zero-shot deployment** required (new platform without profiling)
- **Large program libraries** with diverse workloads (amortize LLM cost)
- **Safety-critical systems** with thermal constraints (hallucination prevention critical)

**When to Use Traditional RL-based DVFS**:
- **Single workload** on known platform (profiling cost justified)
- **Offline deployment** (no need for zero-shot)
- **Cost-sensitive** scenarios (no LLM API budget)

**Hybrid Approach**:
- Use LLM features for initial zero-shot deployment
- Collect runtime profiling data for fine-tuning (10-50 episodes)
- Cache LLM features for reuse (avoid repeated API calls)

## Bibdata

```bibtex
@inproceedings{pivezhandizerodvfs,
  title={ZeroDVFS: Zero-Shot LLM-Guided Autonomous Agent for Energy-Aware Resource Allocation in Embedded Systems},
  author={Pivezhandi, Mohammad and Banisharif, Mahdi and Saifullah, Abusayeed},
  booktitle={Agentic AI in the Wild: From Hallucinations to Reliable Autonomy}
}
```
