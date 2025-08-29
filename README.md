# Data Ingestion & Schema — WQuality_River-Data-2023

**Source PDF:** `WQuality_River-Data-2023.pdf` (CPCB water quality tables). Source file used in parsing: see repository. :contentReference[oaicite:1]

## Purpose
This README documents the ingestion approach and the normalized CSV schema used for Project Ganga Purity pilot models.

## Ingestion Steps
1. Attempt table extraction using `tabula-py` (preferred; requires Java). If tabula fails, fallback to `pdfplumber` text extraction and heuristic parsing.
2. For each discovered row, parse:
   - station_code (int)
   - monitoring_location (string)
   - state_name (string)
   - numeric parameter min/max pairs (floats) as present
3. Normalize into tidy CSV with one station per row and explicit min/max columns.

## Normalized CSV schema (columns)
- station_code
- monitoring_location
- state_name
- temp_min, temp_max
- do_min, do_max
- ph_min, ph_max
- conductivity_min, conductivity_max
- bod_min, bod_max
- nitrate_min, nitrate_max
- fecal_coliform_min, fecal_coliform_max
- total_coliform_min, total_coliform_max
- fecal_streptococci_min, fecal_streptococci_max

## Notes & caveats
- The CPCB PDF tables are heterogeneous across pages and rivers (some tables have slightly different column order). This script uses heuristics and may require manual review of extracted CSV for edge cases.
- For highest accuracy on a large-scale ingest, use `tabula-py` with page-specific `area` parameters or a PDF-to-CSV pipeline tuned per-table.
- After initial extraction, perform QA: check for missing `station_code`, empty `state_name`, and outlier numeric ranges.

## Next steps
- Manually validate first 200-500 rows to produce a high-quality training/station inventory file.
- Geocode monitoring_location to lat/lon using Google Maps / Nominatim where possible and add `latitude`/`longitude`.
