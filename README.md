# Zasqua (legacy backend)

> **Archived.** This Django application was the original cataloging backend for
> [Zasqua](https://zasqua.org). It is preserved here, read-only, as a historical
> reference; it is no longer the source of truth and receives no further data.
>
> To make Zasqua easier for others to deploy, what this project did is now split
> into three parts:
>
> - **Cataloging** — [Fisqua](https://fisqua.org), the current tool for creating
>   and managing archival descriptions, entities, and places.
> - **The Zasqua engine** — [`UCSB-AMPLab/zasqua`](https://github.com/UCSB-AMPLab/zasqua)
>   (on npm as [`@ucsb-ampl/zasqua`](https://www.npmjs.com/package/@ucsb-ampl/zasqua)).
>   Compiles a six-file JSON dataset into a complete static archival website — no
>   application server, no database, no search backend at request time.
> - **Instance repositories** — the thin configuration-and-theming repos that
>   build a site on top of the engine. Neogranadina's is
>   [`neogranadina/zasqua-neogranadina`](https://github.com/neogranadina/zasqua-neogranadina),
>   and a fork-and-go starter for building your own is
>   [`UCSB-AMPLab/zasqua-template`](https://github.com/UCSB-AMPLab/zasqua-template).
>
> The engine ships importers (CSV, EAD3, CollectiveAccess), so the six-file
> dataset it builds from can come from this backend, from Fisqua, or from another
> cataloging system entirely.
>
> You are welcome to fork and develop this further — if you do, we would love to
> hear about it.

## What this was

Zasqua Backend was the cataloging and data-export engine for the Zasqua platform.
It managed archival descriptions, entities, and places for five repositories in
Colombia and Peru — over 104,000 descriptions and 52,000 entities covering
colonial and republican-era judicial, notarial, ecclesiastical, and administrative
records. It ran locally as a cataloging tool and was never deployed as a
public-facing server; instead it exported structured JSON that the static
frontend (now [`neogranadina/zasqua-legacy_frontend`](https://github.com/neogranadina/zasqua-legacy_frontend))
built into a fully static site.

**Key capabilities:**

- MPTT-based hierarchical data model (archival fonds, series, items) following ISAD(G) standards
- Management commands for data import from CollectiveAccess and CSV sources
- JSON export pipeline for the static site build
- IIIF Presentation API v3 manifest generation for digitized materials
- REST API for archival descriptions, entities, and places

## Requirements

- Python 3.11+
- MySQL 8.0+

## Setup

```bash
# Create virtual environment
python -m venv venv
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt

# Configure environment
cp .env.example .env
# Edit .env with your database credentials

# Run migrations
python manage.py migrate

# Start development server
python manage.py runserver
```

## Database Models

| Model | Description |
|-------|-------------|
| Repository | Archive institutions (AHR, AHRB, CIHJML, AHJCI, PE-BN) |
| Description | Archival descriptions with MPTT hierarchy |
| Entity | People, organizations, families |
| Place | Geographic locations |
| DescriptionEntity | Links descriptions to entities with roles |
| DescriptionPlace | Links descriptions to places with roles |

## API Endpoints

| Endpoint | Description |
|----------|-------------|
| `/api/repositories/` | List all repositories |
| `/api/repositories/{code}/` | Repository detail |
| `/api/descriptions/` | List descriptions with filtering |
| `/api/descriptions/{id}/` | Description detail |
| `/api/descriptions/{id}/children/` | Child descriptions |
| `/api/entities/` | List entities |
| `/api/places/` | List places |

## Management Commands

### Data Import

```bash
# Import from CollectiveAccess MySQL database
python manage.py import_ca --phase [descriptions|entities|places|links]

# Import AHR hierarchy from clean CSVs
python manage.py import_ahr_hierarchy --data-dir /path/to/ahr/csvs

# Import AHT item-level records from CSV
python manage.py import_aht_items --csv-path /path/to/AHT_items_clean.csv

# Update AHT legajo containers with metadata from CSV
python manage.py update_aht_legajos --csv-path /path/to/AHT_items_clean.csv

# Import OCR text from CA representations
python manage.py import_ocr_text

# Restructure PE-BN CDIP items into section-level hierarchy
python manage.py restructure_pebn_sections --cleaning-csv /path/to/section_title_mappings.csv
```

### Data Export

```bash
# Export JSON data for frontend static build
python manage.py export_frontend_data

# Export item metadata for title/entity processing
python manage.py export_acc_metadata
```

### IIIF

```bash
# Generate IIIF manifests for digitized descriptions
python manage.py generate_iiif_manifests --tiles-dir /path/to/tiles
```

All import commands support `--dry-run` to preview changes without modifying the database.

## Data Summary

| Repository | Location | Descriptions |
|------------|----------|-------------|
| AHR | Rionegro, Antioquia | ~53,000 |
| AHRB | Tunja, Boyaca | ~8,300 |
| CIHJML | Popayan, Cauca | ~25,000 |
| AHJCI | Istmina, Choco | ~300 |
| PE-BN | Lima, Peru | ~17,000 |

## Development

### Running Tests
```bash
python manage.py test
```

### Code Style
```bash
black .
flake8
```

## Related

- [Zasqua engine](https://github.com/UCSB-AMPLab/zasqua) — the deployment-agnostic publishing engine that supersedes the static frontend
- [Zasqua (legacy frontend)](https://github.com/neogranadina/zasqua-legacy_frontend) — the original static site, also archived
- [Fisqua](https://fisqua.org) — current cataloging tool

## License

Zasqua is licensed under the [GNU Affero General Public License v3.0](LICENSE).

Anyone may use, modify, and self-host Zasqua under AGPL terms. If you run a modified version as a network service for others, you must publish your modifications under the same license. The license governs the software; the archival content published with Zasqua belongs to the institutions that hold and describe it.

## Trademarks

"Zasqua", "Fisqua", "AMPL", and the associated logos are not covered by the AGPL-3.0 license. Forks may use the code freely under AGPL terms but should not present themselves as official Zasqua, Fisqua, or AMPL releases.

## Credits

Zasqua is developed by Juan Cobo Betancourt at the [Archives, Memory, and Preservation Lab](https://ampl.clair.ucsb.edu) (AMPL) of the University of California, Santa Barbara, and [Neogranadina](https://neogranadina.org).
