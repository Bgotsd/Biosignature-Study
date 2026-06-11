# Biosignature-Study

This repository contains research materials for the project:

**行星样品的生命记号：特异性生命分子的判别**

The project studies whether Molecular Assembly Index (MA) can be used as a life-specific molecular biosignature, and builds a curated molecular evidence database for later machine-learning exploration of biosignature features.

## Research Goals

The current scientific goals are:

1. Test the limitations of MA as a biosignature criterion.
   - High MA does not necessarily imply biological origin.
   - Low MA does not necessarily exclude biological origin.
2. Build an auditable molecular database containing molecules from different source contexts.
   - Strong abiotic examples, such as returned asteroid samples, prebiotic experiments, combustion, pyrolysis, and PAHs.
   - Biological or biomarker candidates, with explicit evidence levels and nonbiotic-report checks.
3. Prepare a future machine-learning-ready dataset.
   - Labels should be evidence-based, not based on assumptions about MA.
   - Molecular structures, source provenance, label strength, and leakage-control groups must be tracked.

## Current Recommended Database

The current recommended workbook is:

[`数据库/5.20完善数据集_research_db_v0.4_ma_corrected.xlsx`](数据库/5.20完善数据集_research_db_v0.4_ma_corrected.xlsx)

Its detailed structure is documented here:

[`数据库/README.md`](数据库/README.md)

The original dataset is preserved as:

[`数据库/5.20完善数据集.xlsx`](数据库/5.20完善数据集.xlsx)

A corrected copy with yellow-highlighted/random MA values removed is available as:

[`数据库/5.20完善数据集_ma_corrected.xlsx`](数据库/5.20完善数据集_ma_corrected.xlsx)

## Repository Structure

- `数据库/`
  - Main curated molecular database, source registry, BibTeX file, and database README.
- `scripts/`
  - Reproducible scripts for building the research database, curating pilot samples, and deduplicating sources.
- `中期/`
  - Midterm project materials and progress documents.
- `大创项目申请/`, `重点项目/`, `汇报/`
  - Project application and reporting materials.

The local folders `Python-MA/` and `MA 算法/` are excluded from this repository because they are nested working repositories related to algorithm development and already point to the separate GitHub repository `Bgotsd/Molecular-Complexity`.

## Database Workflow

The database is designed around evidence rather than only molecule names.

1. Add literature/database sources to `Assembly Method.bib`.
2. Parse sources into `Source_Registry`.
3. Deduplicate repeated literature records into canonical sources plus `Source_Aliases`.
4. Record molecule-level evidence in `Evidence_Log`.
5. Curate structures, labels, confidence, and analysis eligibility in `Molecule_Curated`.
6. Generate analysis views:
   - `High_MA_Abiotic`
   - `Low_MA_Bio`
   - `ML_Dataset_View`

The current database includes a 20-molecule pilot curation set in `Pilot_Curation_20`.
In v0.4, 51 yellow-highlighted MA values from the original `All` sheet were removed because they were randomly filled placeholders. These rows are tracked in `MA_Removal_Log`.

## Scripts

Run from the repository root:

```bash
python3 scripts/build_research_database.py
python3 scripts/curate_pilot_20.py
python3 scripts/deduplicate_sources.py
python3 scripts/remove_highlighted_random_ma.py
```

These scripts generate the current research database versions from the legacy workbook and BibTeX source list.

## Important Scientific Cautions

- PubChem presence is not required for MA calculation; an auditable molecular graph is the important requirement.
- Repeated molecules across different sources are evidence records, not noise.
- Biomarker candidates should not be treated as life-exclusive without a targeted nonbiotic-origin search.
- The machine-learning view is not yet a final training dataset.
- Pilot samples are intentionally held out until full source, structure, and leakage audits are complete.
