# My Research Papers Repository

This repository contains organized documentation and key information for all published research papers by Mohammad Pivezhandi.

## Summary Statistics

- **Total Publications**: 15 papers (2015-2026)
- **First-Author Papers**: 12 papers
- **Co-Authored Papers**: 3 papers
- **Venues**: 10 conferences, 2 journals, 2 arXiv preprints, 1 thesis
- **Primary Research Focus**: DVFS and Energy-Aware Scheduling for Embedded Systems

## Repository Structure

```
myresearch/
├── README.md          # This file
├── pastwork.md        # Comprehensive table of all publications
└── papers/            # All research papers organized by year
    ├── 2015/          # Publications from 2015
    ├── 2016/          # Publications from 2016
    ├── 2017/          # Publications from 2017
    ├── 2020/          # Publications from 2020
    ├── 2021/          # Publications from 2021
    ├── 2022/          # Publications from 2022
    ├── 2023/          # Publications from 2023
    ├── 2024/          # Publications from 2024
    ├── 2025/          # Publications from 2025
    └── 2026/          # Publications from 2026
```

## Papers Organization

Each paper is documented in a markdown file named after its BibTeX citation key. The files contain:

- **Key Points**: Main contributions and findings
- **Abstract**: Summary of the paper
- **Contributions**: Detailed list of contributions
- **Methodology**: Approach and techniques used
- **Results**: Key experimental results and findings
- **Notes**: Additional observations and references
- **BibTeX**: Citation information
- **PDF Link**: Direct link to PDF file in the repository

## Research Areas

### Primary PhD Research: DVFS and Energy-Aware Scheduling (2023-2026)

**Core DVFS Papers** (7 papers, 100% ML relevance):

All papers use advanced machine learning techniques for energy-aware scheduling on embedded platforms (NVIDIA Jetson TX2/Orin NX, RubikPi, Intel Core i7):

- **2026 (3 papers)**:
  - `pivezhandi2026zerodvfs` - Zero-shot LLM-guided DVFS with hierarchical MARL (7.09× energy efficiency)
  - `pivezhandi2026hidvfs` - Hierarchical multi-agent DVFS scheduler for OpenMP workloads
  - `pivezhandizerodvfs` - Workshop paper on LLM-guided autonomous agents (ICLR'26)

- **2025 (2 papers)**:
  - `pivezhandi2025graphperf` - GNN surrogate for performance prediction (66% makespan reduction)
  - `pivezhandi2025feature` - Statistical learning for task-to-core allocation (IEEE RTCSA'25)

- **2024 (2 papers)**:
  - `pivezhandi2024flowrl` - Few-shot RL with continuous normalizing flows (35% higher frame rates)
  - `pivezhandi2024work` - Hierarchical MARL for energy/thermal-aware scheduling (RTSS'24 WIP)

- **2023 (1 paper)**:
  - `pivezhandi2023toward` - Master's thesis: RL-based DVFS foundation (Iowa State University)

**Co-Authored DVFS Papers** (2 papers):

- **2023**: `bhuiyan2023precise` - DAG scheduling with DPM on heterogeneous platforms (ECRTS, 40% ML)
- **2022**: `bashir2022energy` - Energy/thermal-aware scheduling on Intel processors (HPCC, 50% ML)

### Other Research Areas

**Machine Learning and Neural Networks** (2 papers, 100% ML relevance):

- **2021**: `bengre2021learning` - GNN-based scheduler for data warehouse processing (PADTAD, co-author)
- **2015**: `pivezhandi2015statistical` - Statistical neural networks for human activity recognition (IJCA)

**FPGA and Hardware Accelerators** (2 papers):

- **2020**: `pivezhandi2020parahist` - Parallel event-based histogram for optical flow on Xilinx FPGA (ASAP, 20% ML)
- **2016**: `pivezhandi2016asip` - ASIP design for CORDIC-based DFT/DCT algorithms (ICCKE, 0% ML)

**Device Modeling** (1 paper):

- **2017**: `pivezhandi2017accuracy` - Statistical models for CNT-FET applications (Journal of Computational Electronics, 30% ML)

### ML Relevance Distribution

- **100% ML Relevance**: 10 papers (RL/DL-based DVFS, GNN, neural networks)
- **50% ML Relevance**: 1 paper (hybrid ML/systems)
- **30-40% ML Relevance**: 2 papers (statistical methods, optimization)
- **20% ML Relevance**: 1 paper (computer vision preprocessing)
- **0% ML Relevance**: 1 paper (pure hardware design)

## Key Milestones

- **2015**: First ML publication (neural networks for activity recognition)
- **2016**: First hardware design paper (ASIP for signal processing)
- **2017**: Device modeling with statistical methods (CNT-FET)
- **2020**: First FPGA publication (hardware acceleration for computer vision)
- **2021**: First co-authored ML work (GNN for scheduling)
- **2022**: First DVFS implementation (energy-aware scheduling on Intel)
- **2023**: Master's thesis (RL-based DVFS foundation at Iowa State University)
- **2024**: FlowRL (few-shot RL for DVFS), RTSS WIP (hierarchical MARL)
- **2025**: GraphPerf-RT (GNN for performance prediction), Feature-Aware (statistical learning)
- **2026**: ZeroDVFS (LLM-guided DVFS), HiDVFS (hierarchical MARL), ICLR Workshop

## Research Evolution Timeline

1. **Early Work (2015-2017)**: Neural networks, hardware design, device modeling
   - Foundations in ML and hardware co-design
   - Statistical methods for both software and hardware

2. **FPGA Era (2016-2020)**: Hardware accelerators for signal processing and computer vision
   - Application-specific processors (ASIP)
   - FPGA implementations for real-time vision

3. **Collaboration Phase (2021-2022)**: Co-authored ML and DVFS papers
   - Graph neural networks for scheduling
   - Energy-aware scheduling implementations

4. **Master's Research (2023)**: Foundation of RL-based DVFS
   - Iowa State University thesis
   - Real-time energy-aware automation using reinforcement learning

5. **PhD Research (2024-2026)**: Advanced ML techniques for DVFS
   - **Few-shot RL** (FlowRL): Distribution-aware flow matching
   - **Hierarchical MARL** (HiDVFS, RTSS WIP): Multi-agent coordination
   - **Graph Neural Networks** (GraphPerf-RT): Performance modeling
   - **Statistical Learning** (Feature-Aware): Task allocation optimization
   - **LLM-Guided Agents** (ZeroDVFS, Workshop): Zero-shot learning for embedded systems

## Citation Information

All citation keys and BibTeX entries are maintained in `/home/mohammad/Desktop/PhD-Thesis/my_citations.bib`.

For usage in LaTeX documents, refer to `.claude/rules/06-my-citations.md` in the main PhD-Thesis repository.

For a comprehensive table with all paper details, see `pastwork.md`.

## Papers by Year

### 2026 (3 papers)

- **`pivezhandi2026zerodvfs`** (arXiv) - Zero-Shot LLM-Guided Core and Frequency Allocation for Embedded Platforms
  - Platforms: Jetson TX2/Orin NX, RubikPi, Intel i7
  - 7.09× energy efficiency, 4.0× makespan improvement

- **`pivezhandi2026hidvfs`** (arXiv) - HiDVFS: A Hierarchical Multi-Agent DVFS Scheduler for OpenMP DAG Workloads
  - Decomposed action space for efficient thermal and energy-aware scheduling

- **`pivezhandizerodvfs`** (ICLR'26 Workshop) - ZeroDVFS: Zero-Shot LLM-Guided Autonomous Agent for Energy-Aware Resource Allocation
  - Demonstrates reliable LLM control in safety-critical embedded systems

### 2025 (2 papers)

- **`pivezhandi2025graphperf`** (arXiv) - GraphPerf-RT: Graph-Driven Performance Model for Hardware-Aware Scheduling
  - GNN surrogate: 2-7ms inference, R²>0.95, 66% makespan reduction with MAMBRL-D3QN

- **`pivezhandi2025feature`** (IEEE RTCSA'25) - Feature-Aware Task-to-Core Allocation in Embedded Multi-core Platforms
  - Statistical learning approach for optimal core assignment

### 2024 (2 papers)

- **`pivezhandi2024flowrl`** (arXiv) - FlowRL: Flow-Augmented Few-Shot Reinforcement Learning for Semi-Structured Sensor Data
  - Continuous normalizing flows, 35% higher frame rates on Jetson TX2

- **`pivezhandi2024work`** (RTSS'24 WIP) - Energy and Thermal-Aware Scheduling based on HMARL for OpenMP DAG Workloads
  - Early work on hierarchical multi-agent reinforcement learning

### 2023 (2 papers)

- **`pivezhandi2023toward`** (Iowa State University) - Toward Real-Time Energy-Aware Automation of Resource Scheduling using Reinforcement Learning
  - Master's thesis, foundation for PhD DVFS research

- **`bhuiyan2023precise`** (ECRTS'23, co-author) - Precise Scheduling of DAG Tasks with Dynamic Power Management
  - 5 tables, heterogeneous multi-core platforms

### 2022 (1 paper)

- **`bashir2022energy`** (HPCC'22, co-author) - Energy-and Temperature-Aware Scheduling: From Theory to Implementation on Intel Processor
  - Practical DVFS deployment with temperature constraints

### 2021 (1 paper)

- **`bengre2021learning`** (PADTAD'21, co-author) - A Learning-Based Scheduler for High Volume Processing in Data Warehouse using Graph Neural Networks
  - GNN-based task scheduling optimization

### 2020 (1 paper)

- **`pivezhandi2020parahist`** (ASAP'20) - ParaHist: FPGA Implementation of Parallel Event-Based Histogram for Optical Flow Calculation
  - Real-time computer vision on Xilinx FPGA

### 2017 (1 paper)

- **`pivezhandi2017accuracy`** (Journal of Computational Electronics) - Accuracy Improvement with Reliable Statistical-Based Models for CNT-FET Applications
  - Statistical modeling for carbon nanotube transistors

### 2016 (1 paper)

- **`pivezhandi2016asip`** (ICCKE'16) - ASIP Design for Two Dimensional CORDIC Based DFT and DCT Algorithms
  - Application-specific instruction-set processor for signal processing

### 2015 (1 paper)

- **`pivezhandi2015statistical`** (IJCA) - Statistical Based Neural Network in Human Activity Recognition
  - ML techniques for sensor-based activity classification

## Quick Navigation

```bash
# List all papers
find papers/ -name "*.md" -type f | sort

# List papers by year
ls papers/2026/

# View specific paper
cat papers/2026/pivezhandi2026zerodvfs.md
```

## Usage

This repository serves as:

1. **Documentation Hub**: Central location for all research paper summaries
2. **Reference Source**: Quick access to key points and contributions
3. **Citation Database**: Organized BibTeX citation keys
4. **Knowledge Base**: Notes and observations from each publication

## Maintenance

- Papers are organized chronologically by publication year
- Each markdown file follows a consistent template structure
- Citation keys match those in `my_citations.bib`
- Updates should maintain the template format for consistency

## Related Repositories

This is a submodule of the main PhD-Thesis repository:

- **Main Repository**: `/home/mohammad/Desktop/PhD-Thesis`
- **Citation Database**: `/home/mohammad/Desktop/PhD-Thesis/my_citations.bib`
- **Other Submodules**: 01-HiDVFS, 02-FeatureEvaluation, 03-ZeroDVFS, 04-FlowRL, 05-GraphPerf-RT, FPGA-Vision-Survey

---

**Last Updated**: 2026-03-26
**Total Papers**: 15 papers (2015-2026)
**First-Author Papers**: 12 papers
**Co-Authored Papers**: 3 papers
**Primary Research Focus**: DVFS and Energy-Aware Scheduling for Embedded Systems
**Comprehensive Table**: See `pastwork.md` for detailed publication information
