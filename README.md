# Advanced Company MDM
Advanced Company Profiling and Data Validation

## Overview
This repository implements a practical workflow for profiling company records and validating them against the UK Companies House API. It standardizes names, performs fuzzy matching, enriches records with official identifiers and metadata, and summarizes data quality so you can trust what flows into downstream analytics and master data systems.

The core logic lives in a single Jupyter notebook:
- **advancedCompanyProfiling.ipynb** : end-to-end workflow for loading input data, validating companies via API, fuzzy-matching names, profiling quality, and exporting results.

## Why this approach
- **Normalization** reduces trivial text differences while keeping originals intact.
- **RapidFuzz** recovers near-matches where exact matching fails.
- **Companies House API** anchors results to official company numbers, status, Address.
- **KPIs and proxy metrics** provide a quality signal without needing labeled ground truth.

## Pipeline
1. **Data Collection** — load `Company.csv`.
2. **Profiling** — schema, nulls, uniqueness.
3. **Cleansing** — non-destructive normalization (name, postcode, email, block key).
4. **Deduplication** — exact drop-duplicates + fuzzy clustering within blocks.
5. **Matching** — search Companies House and score candidates with RapidFuzz.
6. **Enrichment** — attach company number, status, creation date, address snippet.
7. **Reporting** — KPIs, status distribution, score histogram, proxy metrics.
8. **Delivery** — export enriched data to `outputs/` (CSV).

## Setup
```bash
pip install -r requirements.txt
```
Create `.env`:
```
COMPANIES_HOUSE_API_KEY=<your_api_key_here> # Place your API key here 
```
## Running
```bash
jupyter notebook
```
Open `advancedCompanyProfiling.ipynb` and run cells in order.


## Outputs
- `outputs/enriched_companies.csv`


## Troubleshooting

- 401/403: check .env key and restart kernel.
- 429: you’ve hit rate limits, wait or run smaller batches.
- CSV encoding: read with encoding="utf-8".

## Data Dictionary (brief)

- company_name_norm: uppercase, de-accented, spacing-normalized name.
- postcode_norm: UK format normalized (single space before last 3 chars).
- email_norm: lowercase email.
- block_key: company_name_norm (no spaces) + | + postcode_norm.
- ch_best_*: fields returned from Companies House best match (title, number, status, creation date, address, score).
