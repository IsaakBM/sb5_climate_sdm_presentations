# SB5 Climate SDM Presentations

Reusable Quarto/Reveal.js presentation repository for SB5 climate-impact and species distribution modeling talks.

The first deck is for the Megafauna Workshop Galapagos 2026:

```bash
quarto render talks/megafauna_workshop_galapagos_2026/index.qmd
```

## Reproducible R Environment

This repository uses `renv` to lock the R package environment for Quarto/Reveal.js
HTML presentations. When opening the project for the first time in RStudio, run:

```r
renv::restore()
```

Then render the deck from the project root:

```bash
quarto render talks/megafauna_workshop_galapagos_2026/index.qmd
```

The lockfile includes packages useful for this kind of reproducible HTML slide
repository:

- Quarto and Reveal.js rendering support: `quarto`, `knitr`, `rmarkdown`, `bslib`, `sass`, `htmltools`
- Data wrangling: `dplyr`, `tidyr`, `readr`, `tibble`
- Figures and interactive HTML outputs: `ggplot2`, `leaflet`, `plotly`, `htmlwidgets`
- Spatial workflows: `sf`, `terra`
- Presentation assets and export helpers: `qrcode`, `webshot2`, `chromote`, `magick`, `png`, `fontawesome`, `ragg`, `svglite`
- Project utilities: `renv`, `here`, `glue`, `yaml`, `cli`

## Repository Structure

```text
shared/
  theme/        Shared Reveal.js SCSS theme
  assets/       Logos, photos, screenshots, icons
  references/   Papers, workshop notes, and source documents

talks/
  megafauna_workshop_galapagos_2026/
    index.qmd
    sections/

scripts/        Reproducible figure and data scripts
data/           Input data or documented pointers to external data
outputs/        Rendered slides and generated outputs
renv.lock       Locked R package versions for reproducibility
```

## Design Intent

This project treats Quarto/Reveal.js as the reproducible source of truth. The existing PowerPoint deck is used as a visual reference, especially for:

- full-bleed marine imagery
- translucent blue/teal overlays
- clean white technical slides
- gray SB5 header and rule
- blue section titles
- screenshot-heavy app demo slides
- pipeline and uncertainty diagrams

PowerPoint export is secondary and may require manual cleanup. The primary output is the HTML Reveal.js deck.

## First Talk

The Galapagos 2026 workshop deck begins with a required shared framing section:

- climate projections vs predictions
- interpretation of uncertainty
- limitations and assumptions associated with SDMs
- appropriate use of model outputs in conservation planning

The goal is shared terminology and expectations, not technical training.
