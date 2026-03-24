# Paper Verification Rules

## Critical Rule #1: Preserve Verified BibTeX Data

### Rule 1: ALWAYS Check my_citations.bib FIRST

**Before editing ANY paper markdown file**, you MUST:

1. **Extract citation key from filename**:
   ```bash
   # Example: papers/2024/pivezhandi2024flowrl.md → pivezhandi2024flowrl
   basename paper.md .md
   ```

2. **Find verified BibTeX in my_citations.bib**:
   ```bash
   grep -A 15 "^@.*pivezhandi2024flowrl" my_citations.bib
   ```

3. **Check row number comment**:
   ```bibtex
   % Row 9
   @article{pivezhandi2024flowrl,
     ...
   }
   ```

4. **Copy bibdata EXACTLY as-is**:
   - Include ALL typos, capitalization, formatting
   - DO NOT modify in any way
   - DO NOT add DOI, URL, or missing fields
   - DO NOT fix publisher names or punctuation

### Rule 2: Verified BibTeX Preservation Protocol

**What you MUST preserve exactly**:
- Typos in titles, venues, author names
- Capitalization quirks
- Missing fields (if DOI missing, keep it missing)
- Extra spaces or formatting
- Field order

**Example**:

From my_citations.bib (% Row 9):
```bibtex
@article{pivezhandi2024flowrl,
  title={FlowRL: Flow-Augmented Few-Shot Reinforcement Learning for Semi-Structured Sensor Data},
  author={Pivezhandi, Mohammad and Saifullah, Abusayeed},
  journal={arXiv preprint arXiv:2409.14178},
  year={2024}
}
```

In markdown file, you MUST include EXACTLY:

```markdown
## Bibdata

```bibtex
@article{pivezhandi2024flowrl,
  title={FlowRL: Flow-Augmented Few-Shot Reinforcement Learning for Semi-Structured Sensor Data},
  author={Pivezhandi, Mohammad and Saifullah, Abusayeed},
  journal={arXiv preprint arXiv:2409.14178},
  year={2024}
}
```
```

### Rule 3: Common Mistakes to AVOID

❌ **DO NOT**:
- Add DOI to verified bibdata
- Fix capitalization ("reinforcement learning" → "Reinforcement Learning")
- Add missing fields (publisher, month, DOI, URL)
- Change journal format ("arXiv preprint" → "arXiv e-prints")
- Fix author name formatting
- "Improve" verified bibdata in any way

✅ **DO**:
- Copy verified bibdata character-by-character
- Include the exact same capitalization
- Preserve field order
- Note verification in commit message

---

## Critical Rule #2: PDF Verification Required

### Rule 4: ALWAYS Read PDF Before Writing Content

**Before filling ANY content sections**, you MUST:

1. **Locate PDF file**:
   ```bash
   # PDF should be in same directory as markdown
   ls papers/2024/pivezhandi2024flowrl.pdf
   ```

2. **Read PDF using Read tool**:
   ```
   Read papers/2024/pivezhandi2024flowrl.pdf
   ```

3. **Extract information**:
   - Abstract (usually in first 2 pages)
   - Key contributions (often in introduction or conclusion)
   - Methodology (methods/approach section)
   - Results (experiments/evaluation section)
   - Tables and figures (note their numbers and content)

4. **Verify topic matches bibdata**:
   - Compare PDF title with bibdata title
   - Ensure content aligns with paper topic
   - Check year, authors, venue match

### Rule 5: Comprehensive Content Extraction

**Required sections to fill from PDF**:

1. **## Key Points** (3-5 bullet points):
   - Main contributions
   - Key results with metrics
   - Novel techniques or approaches

2. **## Abstract** (2-4 paragraphs):
   - Full abstract from paper
   - Background and motivation
   - Approach overview
   - Key results and impact

3. **## Contributions** (numbered list):
   - Specific technical contributions
   - Novel algorithms, architectures, or methods
   - Experimental validations
   - Theoretical insights

4. **## Methodology** (detailed description):
   - Technical approach
   - Algorithms and architectures
   - Implementation details
   - Experimental setup

5. **## Results** (quantitative findings):
   - Performance metrics
   - Comparison with baselines
   - Ablation studies
   - Key takeaways

6. **## Notes** (observations):
   - Related work connections
   - Future directions mentioned
   - Limitations discussed
   - Additional references

### Example: Complete Verification

**Paper**: papers/2024/pivezhandi2024flowrl.md

**Step 1**: Extract citation key
```
pivezhandi2024flowrl
```

**Step 2**: Find bibdata (Row 9)
```bibtex
% Row 9
@article{pivezhandi2024flowrl,
  title={FlowRL: Flow-Augmented Few-Shot Reinforcement Learning for Semi-Structured Sensor Data},
  author={Pivezhandi, Mohammad and Saifullah, Abusayeed},
  journal={arXiv preprint arXiv:2409.14178},
  year={2024}
}
```

**Step 3**: Read PDF
```
Read papers/2024/pivezhandi2024flowrl.pdf
```

**Step 4**: Extract content and fill sections
```markdown
## Key Points

- Flow-augmented few-shot reinforcement learning for DVFS
- Continuous normalizing flows for synthetic data generation
- 35% higher frame rates on NVIDIA Jetson TX2
- Zero-shot generalization across benchmarks

## Abstract

This paper presents FlowRL, a flow-augmented few-shot reinforcement learning framework...
[Full abstract from PDF]

## Contributions

1. Flow-based data augmentation for RL in resource-constrained settings
2. Semi-structured sensor data representation for DVFS
3. Experimental validation on embedded platforms
4. Generalization analysis across multiple benchmarks

## Methodology

FlowRL uses continuous normalizing flows to generate synthetic training...
[Detailed methodology from PDF]

## Results

- 35% higher frame rates on Jetson TX2
- Energy efficiency improvement: 2.3×
- Convergence: 50% fewer episodes than baseline
- Zero-shot transfer: 89% of fine-tuned performance

## Notes

- Related to pivezhandi2023toward (master's thesis foundation)
- Extends to pivezhandi2026zerodvfs (LLM-guided approach)
- Published on arXiv 2024

## Bibdata

```bibtex
@article{pivezhandi2024flowrl,
  title={FlowRL: Flow-Augmented Few-Shot Reinforcement Learning for Semi-Structured Sensor Data},
  author={Pivezhandi, Mohammad and Saifullah, Abusayeed},
  journal={arXiv preprint arXiv:2409.14178},
  year={2024}
}
```
```

---

## Critical Rule #3: Verification Workflow

### Rule 6: Follow 10-Step Verification Process

```
Step 1: Extract citation_key from filename
   ↓
Step 2: Find verified bibdata in my_citations.bib
   ↓
Step 3: Note row number from comment (% Row N)
   ↓
Step 4: Locate PDF file in same directory
   ↓
Step 5: Read PDF using Read tool
   ↓
Step 6: Copy verified bibdata EXACTLY to ## Bibdata section
   ↓
Step 7: Extract Key Points (3-5 bullets) from PDF
   ↓
Step 8: Extract Abstract, Contributions, Methodology from PDF
   ↓
Step 9: Extract Results and Notes from PDF
   ↓
Step 10: Verify all sections filled, commit changes
```

### Rule 7: Quality Checklist

Before saving paper markdown file, verify:

- [ ] Citation key in filename matches bibdata
- [ ] Bibdata copied EXACTLY from my_citations.bib
- [ ] PDF read and content extracted
- [ ] All 6 sections filled (Key Points, Abstract, Contributions, Methodology, Results, Notes)
- [ ] Content matches bibdata title/topic
- [ ] Metrics and quantitative results included
- [ ] Year matches bibdata year
- [ ] No placeholder text like "[Add...]" remains

### Rule 8: Commit Message Format

```
Verify and enhance Paper #N: citation_key

Paper: Title from bibdata
Year: YYYY
Venue: Venue from bibdata

Verification:
- Added verified bibdata from my_citations.bib (Row N)
- Extracted comprehensive content from PDF
- Filled all sections: Key Points, Abstract, Contributions, Methodology, Results, Notes

Status: Complete ✓
```

---

## Summary

**Golden Rules**:
1. **Rule 1**: Verified bibdata from my_citations.bib is SACRED - copy exactly as-is
2. **Rule 2**: ALWAYS read PDF before writing content
3. **Rule 3**: Fill ALL template sections comprehensively
4. **Rule 4**: Content must match bibdata title/topic
5. **Rule 5**: Include quantitative results and metrics
6. **Rule 6**: Follow 10-step verification workflow
7. **Rule 7**: Use quality checklist before saving
8. **Rule 8**: Write descriptive commit messages

**Remember**: These papers represent Mohammad's research journey. Documentation must be comprehensive, accurate, and professional.
