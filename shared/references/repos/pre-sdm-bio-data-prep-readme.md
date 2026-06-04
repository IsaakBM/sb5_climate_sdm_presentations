# Pre-SDM Biological Data Preparation

Private RStudio/`renv` project for preserving, harmonizing, quality-controlling, and exporting biological telemetry and observation records before species distribution modeling (SDMs).

Open `pre_sdm_bio_data_prep.Rproj` to work from the project root.

Raw biological data in `data/raw/` are part of the project record and should be tracked in Git for provenance. Do not edit raw files directly; all cleaning should happen through scripts.

## Project Structure

```text
pre_sdm_bio_data_prep/
  R/                         # reusable functions and project definitions
  scripts/                   # numbered workflow scripts
  data/
    raw/                     # untouched source data, tracked for provenance
    metadata/                # source inventory, species lookup, decisions
    interim/
      telemetry/             # source-standardized telemetry files
      observations/          # source-standardized observation and effort files
    processed/
      telemetry/             # species-level harmonized telemetry files
      observations/          # species-level harmonized observation files
      sdm_ready/             # auditable SDM-ready control files by species
      summaries/             # aggregate control and summary tables
  outputs/
    qc_tables/               # inventory and QC tables
    qc_maps/                 # future coordinate/QC maps
    sdm_inputs/              # minimal model inputs: longitude, latitude, species
    downscaling_inputs/      # future downscaling-ready exports
  docs/                      # notes, protocols, and documentation
```

## Raw Data Inventory

Snapshot updated: 2026-05-19. Current raw archive contains 78 tracked source files: 50 CSV, 14 TXT, 13 XLSX, and 1 XLS. Temporary Excel lock files and `.DS_Store` are not counted.

| Raw source | Taxa | Files | Species currently represented | Contents and cleaning focus |
| --- | --- | ---: | --- | --- |
| `sharks_migramar`<br>MigraMar; Hearn et al.; Penaherrera et al. | Sharks | 3 CSV | `Sphyrna lewini` (scalloped hammerhead); `Rhincodon typus` (whale shark) | Telemetry locations.<br>Already close to standardized: species code, owner, ID, UTC date/time, lon/lat, location quality. |
| `sharks_mision_tiburon`<br>Mision Tiburon | Sharks | 2 XLSX | `Sphyrna lewini` (scalloped hammerhead); `Rhincodon typus` (whale shark) | Shark observations/sightings.<br>Needs Excel date conversion and Spanish coordinate parsing. |
| `turtles_noaa_seminoff`<br>NOAA / Seminoff | Sea turtles | 59 files<br>44 CSV, 14 TXT, 1 XLS | `Chelonia mydas` (green turtle) | Turtle telemetry/tag files plus tag summary.<br>Legacy TXT and newer CSV formats differ; confirm species/tag mapping from `Seminoff Galapagos green tag summary.xls`. |
| `turtles_crema`<br>CREMA | Sea turtles | 6 files<br>3 CSV, 3 XLSX | `Chelonia mydas` (green turtle); `Eretmochelys imbricata` (hawksbill turtle) | Argos/CLS telemetry plus tag metadata.<br>Preserve location class, error/ellipse fields, message diagnostics, and release metadata. |
| `sea_turtles_kelez_velez_zuazo_manrique_peru`<br>Kelez, Velez-Zuazo & Manrique | Sea turtles | 1 XLSX | `Chelonia mydas` (green turtle); `Dermochelys coriacea` (leatherback turtle) | Peru bycatch/capture observations from 2003-2008.<br>Coordinates are decimal degrees; dates are Excel dates with capture time. Preserve lance ID, turtle ID, curved carapace length measurements, weight, and observer initials in notes. |
| `marine_mammals_panacetacea`<br>Panacetacea / Frank Garita | Marine mammals | 6 XLSX | `Megaptera novaeangliae` (humpback whale); `Pseudorca crassidens` (false killer whale); `Stenella attenuata` (pantropical spotted dolphin); `Tursiops truncatus` (common bottlenose dolphin) | Whale/dolphin sightings, daily summaries, effort summaries, and survey information.<br>Split into separate sightings and effort tables before SDM merging. |
| `marine_mammals_bedrinana_chile`<br>Luis Bedriñana, Chile | Marine fauna collection; cetaceans filtered for SDM layer | 1 XLSX | Confirmed cetacean species include `Balaenoptera musculus` (blue whale), `Balaenoptera physalus` (fin whale), `Balaenoptera borealis` (sei whale), `Balaenoptera edeni` (Brydes whale), `Balaenoptera acutorostrata` (common minke whale), `Megaptera novaeangliae` (humpback whale), `Eubalaena australis` (southern right whale), `Orcinus orca` (killer whale), `Phocoena spinipinnis` (Burmeisters porpoise), `Cephalorhynchus eutropia` (Chilean dolphin), `Lagenorhynchus australis` (Peales dolphin), `Lagenorhynchus obscurus` (dusky dolphin), `Lagenorhynchus cruciger` (hourglass dolphin), `Pseudorca crassidens` (false killer whale), `Grampus griseus` (Rissos dolphin), `Tursiops truncatus` (common bottlenose dolphin), `Globicephala melas` (long-finned pilot whale), `Globicephala macrorhynchus` (short-finned pilot whale), `Delphinus delphis` (common dolphin), `Delphinus capensis` (long-beaked common dolphin), `Stenella coeruleoalba` (striped dolphin), `Feresa attenuata` (pygmy killer whale), `Kogia breviceps` (pygmy sperm whale), and `Ziphius cavirostris` (Cuviers beaked whale). | Observation collection from Chile. Raw file also includes non-cetacean marine fauna and uncertain/group-level codes; raw data are preserved, interim standardized observations keep all rows, and SDM-ready/model exports keep only confirmed cetacean species-level records with valid coordinates. |

## Harmonized Data Design

The pipeline separates records into two major data families:

- `telemetry`: satellite/GPS/Argos locations from tagged animals.
- `observations`: sightings, survey observations, and effort summaries.

The main schemas are defined in `R/schemas.R`:

- telemetry control schema
- observation control schema
- SDM-ready control schema
- minimal modeling schema

SDM-ready control files keep provenance and QC metadata. Minimal modeling inputs are exported separately with only:

```text
longitude,latitude,species
```

## Workflow Scripts

Run scripts from the project root.

```r
source("scripts/00_inventory.R")
source("scripts/01_standardize_telemetry.R")
source("scripts/02_standardize_observations.R")
source("scripts/03_build_sdm_ready.R")
source("scripts/04_export_model_inputs.R")
```

Or run the full workflow:

```r
source("scripts/run_pipeline.R")
```

Script roles:

| Script | Purpose | Main outputs |
| --- | --- | --- |
| `00_inventory.R` | Inventory raw files and summarize file counts. | `outputs/qc_tables/raw_file_inventory*.csv` |
| `01_standardize_telemetry.R` | Standardize MigraMar, NOAA/Seminoff, and CREMA telemetry. | `data/interim/telemetry/` |
| `02_standardize_observations.R` | Standardize Mision Tiburon observations, Panacetacea sightings/effort, the Bedriñana Chile observation collection, and Peru turtle bycatch captures. | `data/interim/observations/` |
| `03_build_sdm_ready.R` | Build species-level SDM-ready control files. | `data/processed/sdm_ready/`, `data/processed/telemetry/`, `data/processed/observations/`, `data/processed/summaries/` |
| `04_export_model_inputs.R` | Export minimal SDM model inputs. | `outputs/sdm_inputs/` |

## Current Framework Outputs

Species-level SDM-ready files are written one file per species in `data/processed/sdm_ready/`. The aggregate audit layer is kept separately in `data/processed/summaries/`.

Existing telemetry and observation sources currently cover:

- `Chelonia mydas` (green turtle): telemetry only; Peru bycatch observations after rerun
- `Eretmochelys imbricata` (hawksbill turtle): telemetry only
- `Rhincodon typus` (whale shark): telemetry + observations
- `Sphyrna lewini` (scalloped hammerhead): telemetry + observations
- `Megaptera novaeangliae` (humpback whale): observations only
- `Pseudorca crassidens` (false killer whale): observations only
- `Stenella attenuata` (pantropical spotted dolphin): observations only
- `Tursiops truncatus` (common bottlenose dolphin): observations only


After rerunning the workflow with `sea_turtles_kelez_velez_zuazo_manrique_peru`, the observation, SDM-ready, summary, and model-input layers will include Peru bycatch/capture observations for:

- `Chelonia mydas` (green turtle): observations; 57 raw records
- `Dermochelys coriacea` (leatherback turtle): observations; 2 raw records

After rerunning the workflow with `marine_mammals_bedrinana_chile`, the observation, SDM-ready, summary, and model-input layers will also include confirmed cetacean species-level records from Chile. `Megaptera novaeangliae`, `Pseudorca crassidens`, and `Tursiops truncatus` merge with existing observation outputs; additional Chile cetacean species include:

- `Balaenoptera musculus` (blue whale): observations only
- `Balaenoptera physalus` (fin whale): observations only
- `Balaenoptera borealis` (sei whale): observations only
- `Balaenoptera edeni` (Brydes whale): observations only
- `Balaenoptera acutorostrata` (common minke whale): observations only
- `Eubalaena australis` (southern right whale): observations only
- `Orcinus orca` (killer whale): observations only
- `Phocoena spinipinnis` (Burmeisters porpoise): observations only
- `Cephalorhynchus eutropia` (Chilean dolphin): observations only
- `Lagenorhynchus australis` (Peales dolphin): observations only
- `Lagenorhynchus obscurus` (dusky dolphin): observations only
- `Lagenorhynchus cruciger` (hourglass dolphin): observations only
- `Grampus griseus` (Rissos dolphin): observations only
- `Tursiops truncatus` (common bottlenose dolphin): observations only
- `Globicephala melas` (long-finned pilot whale): observations only
- `Globicephala macrorhynchus` (short-finned pilot whale): observations only
- `Delphinus delphis` (common dolphin): observations only
- `Delphinus capensis` (long-beaked common dolphin): observations only
- `Stenella coeruleoalba` (striped dolphin): observations only
- `Feresa attenuata` (pygmy killer whale): observations only
- `Kogia breviceps` (pygmy sperm whale): observations only
- `Ziphius cavirostris` (Cuviers beaked whale): observations only

Primary auditable control file:

```text
data/processed/summaries/sdm_ready_all_species_control.csv
```

Primary all-species modeling export:

```text
outputs/sdm_inputs/model_input_all_species.csv
```

## Reproducible R Environment

This project uses `renv` to lock R package versions. After cloning or moving the project, open the `.Rproj` file and run:

```r
renv::restore()
```

Track `renv.lock`, `.Rprofile`, `renv/activate.R`, and `renv/settings.json`. Do not track the local `renv/library/` directory.

## Guiding Rules

- Never edit files in `data/raw/`.
- Record source interpretation and cleaning choices in `data/metadata/cleaning_decisions.md`.
- Update `data/metadata/species_lookup.csv` when a source species code is discovered.
- Treat `data/processed/sdm_ready/` as the species-level SDM-ready layer. Treat `data/processed/summaries/` as the aggregate control and summary layer.
- Treat `outputs/sdm_inputs/` as generated model-ready exports.

## Spatial QC and Marine SDM Exports

The pipeline keeps raw and standardized records auditable, but final marine SDM inputs must exclude records that cannot be matched safely to ocean environmental predictors. This is especially important for sea turtle records, where land or beach-adjacent records may be biologically meaningful but should not enter final marine SDM occurrence files.

Spatial QC is documented in both:

```text
docs/spatial_qc_notes.md
data/metadata/spatial_qc_decisions.md
```

The SDM-ready control files now include spatial QC annotation columns such as `spatial_qc_flag`, `spatial_qc_decision`, `spatial_qc_reason`, `on_land`, `land_admin`, and `distance_to_land_boundary_km`. The control files preserve excluded records for auditability.

Only records with:

```text
spatial_qc_decision == "keep_for_marine_sdm"
```

are exported to final SDM input files. The auditable control files retain `data_type` (`telemetry` or `observation`) for filtering; the minimal model exports use only `longitude`, `latitude`, and `species`.

They are exported to:

```text
outputs/sdm_inputs/
```

Spatial QC audit outputs are written to:

```text
outputs/qc_tables/spatial_qc_exclusions.csv
outputs/qc_tables/spatial_qc_summary.csv
```
