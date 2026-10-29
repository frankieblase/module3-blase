# Module 3: Spatial conservation planning

This module builds spatial conservation plans for Europe (Switzerland included) that each protect 30% of the land, and changes what each plan maximises: species richness, carbon, nature's contributions to people (NCP), or all three together. Afterwards you count who is inside each plan: how many people live in the protected land, and which countries carry it. The goal is to see that an optimizer always gives an answer, but the answer depends on what you told it to count.

The main lesson is in [`module3.qmd`](module3.qmd).

## Learning goals

By the end of the module, you will be able to:

- load and clip a local raster, and cloud-optimised GeoTIFFs to a study area on one common grid;
- build a budget-constrained, raster-based conservation planning problem with `prioritizr`;
- read a spatial optimization's output beyond the map alone (benefit held, people inside, share by country); and
- explain, concretely, where value judgements enter a planning exercise that "just optimizes."

Two optional extensions cover burden sharing between countries and what is already protected.

## Data sources

- **Species richness:** [IUCN Red List spatial data](https://www.iucnredlist.org/resources/spatial-data-download), already extracted to `data/Combined_SR_2025.tif` and `data/Amphibians_RWR_2025.tif`. Terms of use: `data/IUCN_Red_List_Terms_and_Conditions_of_Use_v3_1.pdf`.
- **Carbon:** Noon et al. (2022), irrecoverable carbon, via [Source Cooperative](https://source.coop/cboettig).
- **Nature's contributions to people:** Chaplin-Kramer et al. (2019), via Source Cooperative.
- **Population (end-of-module exercise):** GHS-POP 2020 (EU Joint Research Centre), via Source Cooperative.

Carbon, NCP, and population are pulled over the network each time you run the pull steps. Once you have stacked the layers, the document writes them to `cache/europe_grid.tif` (richness, carbon, NCP). If the network is slow, load that file with `terra::rast()` instead of repeating the pulls.

## Getting started

1.  Open [`module3.Rproj`](module3.Rproj) in RStudio.
2.  Open [`module3.qmd`](module3.qmd).
3.  Install the required packages if you do not already have them.
4.  Restart R, then work through the document from the beginning.

Required R packages:

``` r
install.packages(c(
  "terra", "sf", "dplyr", "tidyr", "ggplot2",
  "rnaturalearth",
  "prioritizr", "highs"
))
```

`data/` already contains the IUCN rasters, so no download is needed for the richness layer. You need an internet connection that can reach Source Cooperative's AWS bucket for carbon, NCP, and population, unless you use the cached grid.

## How to work

Work with a partner, switching who drives every 10-15 minutes and before each new scenario. Discuss each map with your partner before moving to the next step.

## Render the document

Use RStudio's **Render** button, or run:

``` sh
quarto render module3.qmd
```

A full render runs every network pull and solve, including the collapsed solutions, and takes about 3 minutes.

## Troubleshooting

- If a network pull times out, rerun once your connection can reach Source Cooperative's AWS bucket, or load `cache/europe_grid.tif`.
- If carbon is all `NA` or full of speckled holes, check that you used `project()` (not `resample()`) with an aggregating `method`.
- If `solve()` refuses with a presolve error, check that at least one layer has a weight above zero and that the budget can hold any locked-in cells.
- If the cached grid looks stale, delete `cache/europe_grid.tif` and rerun the stacking step.
