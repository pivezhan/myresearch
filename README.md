# My Research Papers Repository

This repository contains organized documentation and key information for all published research papers by Mohammad Pivezhandi.

## Repository Structure

```
myresearch/
├── README.md          # This file
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

## Research Areas

### Primary PhD Research: DVFS and Energy-Aware Scheduling (2023-2026)

**Core DVFS Papers** (7 papers):
- **2026**: ZeroDVFS (Zero-Shot LLM-Guided), HiDVFS (Hierarchical Multi-Agent), ZeroDVFS Workshop (ICLR'26)
- **2025**: GraphPerf-RT (Graph-Driven Performance Model), Feature-Aware (Task-to-Core Allocation)
- **2024**: FlowRL (Few-Shot RL for DVFS), RTSS WIP (Energy and Thermal-Aware Scheduling)
- **2023**: Master's Thesis (Real-Time Energy-Aware Automation)

**Co-Authored DVFS Papers** (2 papers):
- **2023**: Precise Scheduling of DAG Tasks with Dynamic Power Management (ECRTS)
- **2022**: Energy and Temperature-Aware Scheduling (HPCC)

### Other Research Areas

**FPGA and Hardware Accelerators** (2 papers):
- **2020**: ParaHist (FPGA Optical Flow)
- **2016**: ASIP Design (CORDIC-based DFT/DCT)

**Machine Learning and Neural Networks** (2 papers):
- **2021**: Learning-Based Scheduler (Graph Neural Networks)
- **2015**: Statistical Neural Network (Human Activity Recognition)

**Device Modeling** (1 paper):
- **2017**: CNT-FET Statistical Models

## Citation Information

All citation keys and BibTeX entries are maintained in `/home/mohammad/Desktop/PhD-Thesis/my_citations.bib`.

For usage in LaTeX documents, refer to `.claude/rules/06-my-citations.md` in the main PhD-Thesis repository.

## Papers by Year

### 2026 (3 papers)
- `pivezhandi2026zerodvfs` - Zero-Shot LLM-Guided Core and Frequency Allocation
- `pivezhandi2026hidvfs` - Hierarchical Multi-Agent DVFS Scheduler
- `pivezhandizerodvfs` - ZeroDVFS Workshop Paper (ICLR'26 Agentic AI)

### 2025 (2 papers)
- `pivezhandi2025graphperf` - GraphPerf-RT: Graph-Driven Performance Model
- `pivezhandi2025feature` - Feature-Aware Task-to-Core Allocation (IEEE RTCSA'25)

### 2024 (2 papers)
- `pivezhandi2024flowrl` - FlowRL: Few-Shot RL for DVFS
- `pivezhandi2024work` - Work-In-Progress: Energy and Thermal-Aware Scheduling (RTSS'24)

### 2023 (2 papers)
- `pivezhandi2023toward` - Master's Thesis: Real-Time Energy-Aware Automation (Iowa State)
- `bhuiyan2023precise` - Precise Scheduling of DAG Tasks with DPM (ECRTS, co-author)

### 2022 (1 paper)
- `bashir2022energy` - Energy and Temperature-Aware Scheduling (HPCC, co-author)

### 2021 (1 paper)
- `bengre2021learning` - Learning-Based Scheduler using GNN (PADTAD, co-author)

### 2020 (1 paper)
- `pivezhandi2020parahist` - ParaHist: FPGA Optical Flow (ASAP)

### 2017 (1 paper)
- `pivezhandi2017accuracy` - CNT-FET Statistical Models (Journal of Computational Electronics)

### 2016 (1 paper)
- `pivezhandi2016asip` - ASIP Design for CORDIC-based DFT/DCT (ICCKE)

### 2015 (1 paper)
- `pivezhandi2015statistical` - Statistical Neural Network for Activity Recognition (IJCA)

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

**Last Updated**: 2026-03-24
**Total Papers**: 15 papers (2015-2026)
**Primary Research Focus**: DVFS and Energy-Aware Scheduling for Embedded Systems
