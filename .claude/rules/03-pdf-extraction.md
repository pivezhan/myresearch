# PDF Content Extraction Rules

## Purpose

Extract comprehensive, accurate information from PDF files to populate markdown documentation. Each paper must have complete content extracted from its PDF source.

## PDF Location

PDFs are located in same directory as markdown files:

```
papers/YYYY/citation_key.md   # Markdown documentation
papers/YYYY/citation_key.pdf  # PDF source file
```

## Extraction Workflow

### Step 1: Locate and Read PDF

```bash
# Extract citation key from markdown filename
key=$(basename paper.md .md)

# Check PDF exists
ls papers/YYYY/${key}.pdf

# Read PDF with Read tool
Read papers/YYYY/${key}.pdf
```

### Step 2: Extract Structured Sections

#### A. Key Points (3-5 bullet points)

**What to extract**:
- Main contributions or novelty
- Key results with quantitative metrics
- Novel techniques or approaches
- Platforms or devices used

**Where to find**:
- Abstract (brief overview)
- Introduction (problem statement and contributions)
- Conclusion (summary of achievements)
- Experimental results (key metrics)

**Format**:
```markdown
## Key Points

- Main contribution with brief description
- Key result: X% improvement over baseline
- Novel technique: Specific method name
- Platform: Specific device model
```

**Example**:
```markdown
## Key Points

- Flow-augmented few-shot RL for DVFS with synthetic data generation
- 35% higher frame rates on NVIDIA Jetson TX2
- Continuous normalizing flows for semi-structured sensor data
- Zero-shot generalization: 89% of fine-tuned performance across benchmarks
```

#### B. Abstract (2-4 paragraphs)

**What to extract**:
- Background and motivation
- Problem statement
- Proposed approach overview
- Key results and impact
- Conclusions

**Where to find**:
- Abstract section (usually on page 1)
- May need to paraphrase for clarity
- Can combine with introduction if abstract is brief

**Format**:
```markdown
## Abstract

Background paragraph explaining context and motivation.

Problem and approach paragraph describing the challenge and proposed solution.

Results paragraph with quantitative findings.

Impact paragraph summarizing significance.
```

#### C. Contributions (numbered list)

**What to extract**:
- Specific technical contributions
- Novel algorithms, architectures, or methods
- Experimental validations
- Theoretical insights
- Datasets or benchmarks released

**Where to find**:
- Introduction section ("Our contributions are...")
- Often explicitly numbered or bulleted
- May need to extract from conclusion

**Format**:
```markdown
## Contributions

1. First specific contribution (e.g., novel algorithm)
2. Second contribution (e.g., architectural design)
3. Third contribution (e.g., experimental validation)
4. Fourth contribution (e.g., theoretical analysis)
```

**Example**:
```markdown
## Contributions

1. Flow-based data augmentation framework for few-shot RL in resource-constrained embedded systems
2. Semi-structured sensor data representation for DVFS decision-making
3. Continuous normalizing flow model for generating synthetic training trajectories
4. Comprehensive experimental validation on NVIDIA Jetson TX2 with BOTS benchmarks
5. Zero-shot generalization analysis across multiple parallel workloads
```

#### D. Methodology (detailed description)

**What to extract**:
- Technical approach and architecture
- Algorithms (with pseudocode if available)
- Implementation details
- Training procedures (for ML papers)
- Hardware/software setup
- Datasets used

**Where to find**:
- Methods section
- Approach section
- System design section
- Implementation details section

**Format**:
```markdown
## Methodology

**Overall Approach**:
Brief overview of the methodology.

**Architecture/Algorithm**:
Detailed description of key technical components:
- Component 1: Description
- Component 2: Description

**Implementation**:
- Platform: Specific device
- Framework: TensorFlow, PyTorch, etc.
- Language: Python, C++, etc.

**Experimental Setup**:
- Benchmarks: Specific benchmark names
- Baselines: Comparison methods
- Metrics: Evaluation metrics used
```

#### E. Results (quantitative findings)

**What to extract**:
- Performance metrics with numbers
- Comparison with baselines/state-of-the-art
- Ablation study findings
- Resource utilization (power, energy, latency)
- Accuracy, throughput, speedup metrics

**Where to find**:
- Results section
- Experimental evaluation section
- Tables and figures (note table numbers)
- Discussion section

**Format**:
```markdown
## Results

**Performance Metrics**:
- Metric 1: X% improvement over baseline
- Metric 2: Y value achieved
- Metric 3: Z speedup compared to state-of-the-art

**Comparison with Baselines**:
- Method A: Our approach achieves X% higher performance
- Method B: Reduces resource usage by Y%

**Ablation Studies**:
- Component X contributes Z% to overall performance
- Removing Y degrades performance by W%

**Resource Utilization**:
- Power: X watts
- Energy: Y joules
- Latency: Z milliseconds
```

**Example**:
```markdown
## Results

**Performance on NVIDIA Jetson TX2**:
- Frame rate: 35% higher than baseline RL (4.2 vs 3.1 FPS)
- Energy efficiency: 2.3× improvement (12.4 mJ/frame vs 28.6 mJ/frame)
- Convergence speed: 50% fewer episodes to reach target performance

**Zero-Shot Generalization**:
- Transfer to unseen benchmarks: 89% of fine-tuned performance
- No additional training required on target benchmarks
- Robust across 5 BOTS parallel workloads

**Comparison with State-of-the-Art**:
- vs Standard RL: 35% higher throughput, 2.3× energy efficiency
- vs Static DVFS: 78% higher performance, 1.9× energy savings
- vs Oracle (offline): 94% of optimal performance

**Ablation Study**:
- Flow-based augmentation: +22% performance improvement
- Semi-structured representation: +18% improvement
- Combined approach: +35% overall improvement
```

#### F. Notes (additional observations)

**What to extract**:
- Connections to related work
- Citations to Mohammad's other papers
- Future work mentioned
- Limitations discussed
- Code/data availability
- Conference/journal details

**Format**:
```markdown
## Notes

**Related Work**:
- Builds on pivezhandi2023toward (master's thesis foundation)
- Extended by pivezhandi2026zerodvfs (LLM-guided approach)

**Future Directions**:
- Multi-platform generalization
- Online adaptation during execution
- Integration with thermal management

**Limitations**:
- Requires initial profiling phase
- Limited to DAG workloads

**Availability**:
- Code: https://github.com/...
- Data: Available upon request

**Publication Details**:
- Published: arXiv 2024
- arXiv ID: 2409.14178
```

## Special Cases

### DVFS Papers (7 papers)

For DVFS-related papers, extract:
- Platforms: Specific embedded devices (Jetson TX2, Orin NX, RubikPi)
- DVFS metrics: Frequency scaling, voltage levels, power consumption
- RL metrics: Reward, convergence, episode count
- Benchmarks: BOTS, Rodinia, PARSEC, etc.
- Baseline comparisons: Static DVFS, other RL methods

### Hardware Papers (FPGA, ASIP)

For hardware papers, extract:
- Device: Specific FPGA model or custom hardware
- Resource utilization: LUTs, DSPs, BRAM, flip-flops
- Performance: Throughput (GOPS, FPS), latency
- Power: Watts, energy efficiency (GOPS/W)
- Comparison: vs CPU, GPU, other implementations

### Machine Learning Papers

For ML papers, extract:
- Architecture: Neural network structure
- Training: Epochs, batch size, learning rate
- Accuracy: Top-1, Top-5, F1-score, precision, recall
- Dataset: Name, size, splits (train/val/test)
- Baselines: Other ML methods compared

### Co-Authored Papers

For papers where Mohammad is co-author:
- Clearly state Mohammad's specific contributions
- Note collaboration context
- Extract relevant portions to Mohammad's research arc

## Quality Standards

### Minimum Content Requirements

Each paper MUST have:
- [ ] 3-5 key points (concise bullets)
- [ ] Abstract (2-4 paragraphs, 150-300 words)
- [ ] 3-7 contributions (numbered list)
- [ ] Methodology (2-5 paragraphs, 200-400 words)
- [ ] Results (quantitative metrics with numbers)
- [ ] Notes (connections, limitations, future work)

### Quantitative Metrics Required

All results must include numbers:
- ❌ "Significant improvement" → ✅ "35% improvement"
- ❌ "Low power consumption" → ✅ "12.4 mW average power"
- ❌ "Fast convergence" → ✅ "50% fewer episodes (30 vs 60)"
- ❌ "Good accuracy" → ✅ "92.3% classification accuracy"

### Platform Specificity Required

All hardware mentions must be specific:
- ❌ "FPGA" → ✅ "Xilinx Zynq-7020 FPGA"
- ❌ "Embedded device" → ✅ "NVIDIA Jetson TX2 (ARM Cortex-A57)"
- ❌ "Intel processor" → ✅ "Intel Core i7-8700K (6 cores, 12 threads)"

## Extraction Checklist

Before marking paper complete:

- [ ] PDF read completely
- [ ] All 6 sections filled (Key Points, Abstract, Contributions, Methodology, Results, Notes)
- [ ] Quantitative metrics extracted with numbers
- [ ] Platform/device specified with exact model
- [ ] Connections to other papers noted
- [ ] No placeholder text ("[Add...]") remains
- [ ] Content matches bibdata title/topic
- [ ] Tables mentioned by number if present in PDF

## Example: Complete Extraction

See `papers/2024/pivezhandi2024flowrl.md` (once verified) for a complete example of proper PDF extraction.

---

**REMEMBER**: The goal is comprehensive documentation. Extract sufficient detail that someone unfamiliar with the paper can understand its contributions, methodology, and results without reading the PDF.
