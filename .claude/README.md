# .claude/ Directory - Claude Code Configuration

This directory contains configuration, rules, and workflows for Claude Code when working with the myresearch repository (Mohammad Pivezhandi's publication archive).

## Directory Structure

```
.claude/
├── README.md                      # This file
├── rules/                         # Verification and documentation rules
│   ├── 01-paper-verification.md   # Core verification workflow
│   ├── 02-bibdata-preservation.md # Bibdata handling from my_citations.bib
│   └── 03-pdf-extraction.md       # PDF content extraction rules
```

## Repository Context

**Purpose**: Comprehensive documentation of all published research papers by Mohammad Pivezhandi

**Structure**:
- `papers/YYYY/` - Papers organized by publication year (2015-2026)
- `my_citations.bib` - Verified BibTeX entries with row number comments
- `pastwork.md` - Comprehensive table of all 15 publications
- `README.md` - Repository documentation

## Key Features

### 1. Verified BibTeX Database

`my_citations.bib` contains 15 manually verified BibTeX entries:
- Organized chronologically (2015-2026)
- Row number comments (% Row N) for cross-referencing with pastwork.md
- All entries verified against original papers

### 2. Paper Documentation Template

Each paper markdown file (papers/YYYY/citation_key.md) contains:
- Key Points (3-5 bullet points)
- Abstract (comprehensive summary)
- Contributions (numbered list)
- Methodology (approach and techniques)
- Results (experimental findings)
- Notes (additional observations)
- Bibdata (exact copy from my_citations.bib)

### 3. Verification Workflow

All papers are verified against:
1. PDF files in same directory
2. Verified bibdata in my_citations.bib
3. pastwork.md table entry

## Rules Summary

### Rule 1: Bibdata Preservation
- **Source**: my_citations.bib is SACRED
- **Action**: Copy verified bibdata EXACTLY as-is
- **Never**: Modify, fix typos, add fields, or "improve"

### Rule 2: PDF Verification
- **Always**: Read PDF before writing markdown content
- **Extract**: Key points, abstract, contributions, methodology, results
- **Match**: Paper content must align with bibdata title/topic

### Rule 3: Comprehensive Documentation
- **Key Points**: 3-5 concise bullet points highlighting main contributions
- **Abstract**: Full paper summary (2-4 paragraphs)
- **Contributions**: Numbered list of specific technical contributions
- **Methodology**: Detailed approach and techniques used
- **Results**: Experimental findings with quantitative metrics

## Verification Process

```
Step 1: Read paper markdown file
   ↓
Step 2: Extract citation_key from filename
   ↓
Step 3: Find verified bibdata in my_citations.bib (check row number comment)
   ↓
Step 4: Copy verified bibdata EXACTLY to ## Bibdata section
   ↓
Step 5: Read PDF file (same directory as markdown)
   ↓
Step 6: Extract comprehensive content from PDF
   ↓
Step 7: Fill all template sections (Key Points, Abstract, Contributions, etc.)
   ↓
Step 8: Verify content matches bibdata title/topic
   ↓
Step 9: Update pastwork.md if needed
   ↓
Step 10: Commit changes with descriptive message
```

## Research Areas

Papers are categorized by research focus:

### Primary PhD Research: DVFS and Energy-Aware Scheduling (7 papers)
- pivezhandi2026zerodvfs, pivezhandi2026hidvfs
- pivezhandi2025graphperf, pivezhandi2025feature
- pivezhandi2024flowrl, pivezhandi2024work
- pivezhandi2023toward

### Co-Authored DVFS Papers (2 papers)
- bashir2022energy, bhuiyan2023precise

### Machine Learning and Neural Networks (2 papers)
- bengre2021learning, pivezhandi2015statistical

### FPGA and Hardware (2 papers)
- pivezhandi2020parahist, pivezhandi2016asip

### Device Modeling (1 paper)
- pivezhandi2017accuracy

### Workshop Papers (1 paper)
- pivezhandizerodvfs

## Common Mistakes to Avoid

❌ **DO NOT**:
- Modify verified bibdata from my_citations.bib
- Add DOI or missing fields to bibdata
- Fix typos in bibdata (preserve exactly)
- Create content without reading PDF
- Add unsolicited new files
- Use double dash (--) as em-dash

✅ **DO**:
- Copy bibdata character-by-character
- Read PDF before writing content
- Extract comprehensive information from papers
- Fill ALL template sections
- Ask before creating new files
- Use proper LaTeX conventions

## File Naming

Papers: `{author}{year}{shorttitle}.md`
- Example: `pivezhandi2024flowrl.md` matches citation key `pivezhandi2024flowrl`

PDFs: `{author}{year}{shorttitle}.pdf`
- Example: `pivezhandi2024flowrl.pdf` in same directory as markdown

## Git Workflow

After verifying papers:
1. `git add` changed files
2. `git commit -m "Descriptive message"`
3. `git push origin main`

Commit after every 3-5 papers to maintain progress.

## Maintenance

Last Updated: 2026-03-24
Total Papers: 15 (2015-2026)
Verified Papers: Check individual markdown files
