# BibData Preservation Rules

## Purpose

Verified BibTeX data in `my_citations.bib` is the **authoritative source of truth** for all bibliographic information. This file contains 15 manually verified entries representing Mohammad Pivezhandi's complete publication record (2015-2026).

## Why Preservation Matters

1. **Data Integrity**: my_citations.bib represents verified publication metadata
2. **Consistency**: pastwork.md table and all documentation depend on this data
3. **Traceability**: Row number comments (% Row N) link bibdata to table
4. **Reproducibility**: Exact preservation ensures documentation can be regenerated

## Preservation Protocol

### Step 1: Always Check First

```bash
# Search by citation key
grep -A 15 "^@.*{citation_key," my_citations.bib

# Search by row number
grep -B 1 -A 15 "% Row 9" my_citations.bib

# Search by year
grep -A 15 "year={2024}" my_citations.bib
```

### Step 2: Copy Character-by-Character

When verified bibdata exists:
1. Select entire entry (from `% Row N` comment to final `}`)
2. Copy to clipboard
3. Paste into `## Bibdata` section of markdown file
4. **DO NOT** edit, format, or "improve" in any way

### Step 3: Verify Preservation

Before saving:
```bash
# Extract citation key from filename
key=$(basename paper.md .md)

# Compare markdown bibdata with verified source
diff <(grep -A 15 "@.*{$key," my_citations.bib | tail -n +2) \
     <(grep -A 15 "@.*{$key," paper.md | tail -n +2)
```

Should show **NO DIFFERENCES**.

## Row Number Comments

Each bibdata entry has a row number comment:

```bibtex
% Row 9
@article{pivezhandi2024flowrl,
  ...
}
```

**Purpose**: Links bibdata to pastwork.md table row
**Usage**: Easy cross-referencing between files
**Preservation**: Include row comment in markdown if needed for context

## Fields to Never Add

If verified bibdata is missing these fields, **DO NOT** add them:

- `doi` (even if you know it)
- `url` (even if available)
- `publisher` (if not in verified source)
- `month` (if not in verified source)
- `note` (unless in verified source)
- `issn` (unless in verified source)
- `pages` (if not in verified source)
- `volume` (if not in verified source)

## Examples of Exact Preservation

### Example 1: Master's Thesis (Row 8)

**verified source** (my_citations.bib):
```bibtex
% Row 8
@mastersthesis{pivezhandi2023toward,
  title={Toward real-time energy-aware automation of the resource scheduling using reinforcement learning},
  author={Pivezhandi, Mohammad},
  year={2023},
  school={Iowa State University}
}
```

**markdown file** (papers/2023/pivezhandi2023toward.md):
```markdown
## Bibdata

```bibtex
@mastersthesis{pivezhandi2023toward,
  title={Toward real-time energy-aware automation of the resource scheduling using reinforcement learning},
  author={Pivezhandi, Mohammad},
  year={2023},
  school={Iowa State University}
}
```
```

Note: No DOI, no URL, no advisor field. Preserved exactly as-is.

### Example 2: Conference Paper (Row 10)

**verified source** (my_citations.bib):
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

**markdown file** (papers/2024/pivezhandi2024work.md):
```markdown
## Bibdata

```bibtex
@inproceedings{pivezhandi2024work,
  title={Work-In-Progress: Energy and Thermal-Aware Scheduling based on HMARL for OpenMP DAG Workloads},
  author={Pivezhandi, Mohammad and Jain, Aakriti and Saifullah, Abusayeed and Jannesari, Ali},
  booktitle={2024 IEEE Real-Time Systems Symposium (RTSS)},
  pages={455--458},
  year={2024},
  organization={IEEE}
}
```
```

Note: Pages preserved with double dash (455--458). No DOI added.

### Example 3: Workshop Paper (Row 15)

**verified source** (my_citations.bib):
```bibtex
% Row 15
@inproceedings{pivezhandizerodvfs,
  title={ZeroDVFS: Zero-Shot LLM-Guided Autonomous Agent for Energy-Aware Resource Allocation in Embedded Systems},
  author={Pivezhandi, Mohammad and Banisharif, Mahdi and Saifullah, Abusayeed},
  booktitle={Agentic AI in the Wild: From Hallucinations to Reliable Autonomy}
}
```

**markdown file** (papers/2026/pivezhandizerodvfs.md):
```markdown
## Bibdata

```bibtex
@inproceedings{pivezhandizerodvfs,
  title{ZeroDVFS: Zero-Shot LLM-Guided Autonomous Agent for Energy-Aware Resource Allocation in Embedded Systems},
  author={Pivezhandi, Mohammad and Banisharif, Mahdi and Saifullah, Abusayeed},
  booktitle={Agentic AI in the Wild: From Hallucinations to Reliable Autonomy}
}
```
```

Note: No year, no pages, no organization. Minimal entry preserved exactly.

## Error Recovery

If you accidentally modified verified bibdata:

1. **Revert immediately**:
   ```bash
   git checkout paper.md
   ```

2. **Re-extract from verified source**:
   ```bash
   key=$(basename paper.md .md)
   grep -A 15 "@.*{$key," my_citations.bib
   ```

3. **Paste exact copy** into ## Bibdata section

4. **Document in commit message**:
   ```
   Fix: Restore verified bibdata for citation_key

   - Accidentally modified bibdata (added DOI)
   - Restored exact copy from my_citations.bib (Row N)
   - Verified character-by-character match
   ```

## Special Cases

### Co-Authored Papers

Some papers have Mohammad as co-author (not first author):

**Row 5**: bengre2021learning (First author: Vivek Bengre)
**Row 6**: bashir2022energy (First author: Qaisar Bashir)
**Row 7**: bhuiyan2023precise (First author: Ashikahmed Bhuiyan)

Preserve author order exactly as in bibdata. Do not rearrange.

### ArXiv Preprints

ArXiv papers use journal field with preprint format:

```bibtex
journal={arXiv preprint arXiv:2409.14178}
```

Preserve exactly. Do not change to:
- `journal={arXiv}`
- `archivePrefix={arXiv}`
- `eprint={2409.14178}`

### Missing Year

Row 15 (pivezhandizerodvfs) has no year field. This is intentional (workshop paper). Do not add `year={2026}`.

## Summary Checklist

Before saving ANY paper markdown:

- [ ] Checked my_citations.bib for citation key
- [ ] Found row number comment (% Row N)
- [ ] Copied bibdata EXACTLY to ## Bibdata section
- [ ] Preserved ALL capitalization and formatting
- [ ] Did NOT add missing fields (DOI, URL, etc.)
- [ ] Did NOT fix any quirks or typos
- [ ] Verified no changes with diff command
- [ ] Used proper markdown code block for bibdata

**REMEMBER**: my_citations.bib is the authoritative source. When in doubt, preserve exactly as-is.
