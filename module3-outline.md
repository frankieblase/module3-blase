# Module 3 outline: whose values are in the plan?

Runs 29 Oct to 12 Nov, in the slots already on the syllabus.  Students pull real cloud-native layers for Europe (Switzerland included), make protected area plans with prioritizr under a 30% land budget, change what each plan maximises, and see who is inside each plan: how many people, and which countries carry the burden.

The layers, at 10 km (about 58,000 land cells), all on one grid:

- species richness: IUCN Red List combined richness, a local raster in `data/` (World Mollweide), cropped and masked to Europe by the students
- carbon: irrecoverable carbon 2024 (Conservation International), a COG on Source Cooperative (source.coop/cboettig), window-read with terra from a coarser overview level, no keys needed
- nature's contributions to people (NCP): the Chaplin-Kramer et al. (2019) layer, a small COG on Source Cooperative read with the same recipe as carbon (code given)
- population: GHS-POP 2020 (JRC), a global 90 m COG on Source Cooperative that students read at the end from a coarse overview with `method = "sum"` (overview pixels are averages, so the sum has to be scaled back up by 16 x 16), not used as an objective

What I want students to leave with: an optimiser always gives an answer, but the answer depends on layers, budgets, weights and borders that are value judgements, and someone has to make them. The Europe-wide framing makes the last point concrete: the efficient plan loads most of the protected land onto a few countries, and "fair" has several competing meanings.

## How the exercise is structured

1. Load and clip (student tasks with hints and collapsed solutions): crop the richness raster, window-read the carbon COG, pull NCP, stack and mask to country polygons. The traps are the point: a metres raster cropped with degrees, `resample()` returning all `NA`, `bilinear` leaving speckled holes when aggregating, and sea cells that must be `NA` rather than 0.
2. Four plans, each protecting 30% of the land and maximising one benefit: richness, carbon, NCP, or all three with equal weights. There are no per-feature targets, and each layer is rescaled to 0-1 first (the doc explains why, and that it is a choice). The first two plans (carbon, then richness and carbon) are written out by hand with no function; students are then asked what they copied and pasted and why they would write a function, before the doc gives one. Population is deliberately left out of the objective.
3. Your turn (six tasks): why equal weights do not give equal influence (Plan 4 holds about 48% of carbon, 55% of NCP and 32% of richness), changing weights and budget, the amphibian richness layer, a planning group of their own, and a comparison of rescaling choices (raw values, 0-1, divide by total).
4. Exercise, how many people: pull population and count the people inside each plan.
5. Exercise, which countries: build a country raster and a `country_burden()` function, and compare protected share by country.
6. Reflection, is the optimiser neutral? A short paragraph on where values entered the plans.
7. Optional extension A, burden sharing: students design and compare their own alternatives to the pooled plan (every country protects its own 30%, a minimum share per country, or another fairness rule). Few hints and no code are given.
8. Optional extension B, what is already protected: WDPA via `wdpar` and locked-in constraints, again with few hints and no code.

## 29 Oct morning

8:15 Module introduction. 30x30 in Switzerland and the EU, and what it means to hand "where" to an algorithm.

9:00 Methods deep-dive. Load and clip the layers (terra for the carbon and NCP COGs, the same cloud-native moves as Module 1), then what prioritizr does: planning units, features, budget, weights, cost, and why the layers are rescaled to 0-1 before they are summed. Live code up to the four plans. Students should reach the plans by the end of the slot, so tell them to use the backup `cache/europe_grid.tif` if the pulls drag.

## 29 Oct evening

18:15 Critical reflection on Sandbrook et al. (2023). Start from the exercise: the same 30% looks very different depending on what it maximises, and the people inside it were never asked. Which plan would you defend, and who decides?

19:00 Module project. Pairs do "Your turn" tasks 1 to 5, then the people exercise: pull population and see how many people sit inside each plan, even though population was never an objective.

## 5 Nov morning

8:15 Methods deep-dive on adaptive and dynamic decisions, as planned.

9:00 Module project. Pairs do the country exercise and the neutrality reflection, then optional extension A if they have time. In the pooled plan with equal weights, Germany protects 87% of its land and Switzerland 80%, against 36% for France, 15% for Finland and 5% for Spain. Pairs who take on the extension compare the pooled plan with alternatives of their own, measure what fairness costs in benefit, and choose a fairness rule.

## 5 Nov evening

18:15 Critical reflection on Adams (2019). Accountability and participation: if Europe used a plan like ours, who would be in the room when the burden-sharing rule was set? Switzerland is in the geography but not the EU: should it be in the same plan?

19:00 Module project. Prepare the 5-minute presentation. Optional extension B (WDPA) is a good fit for groups who want to ask how existing protection should count towards a country's share.

## 12 Nov morning

Module 3 presentations, 5 minutes per group. Each group shows the plan for the group they invented (Your turn task 5), puts it next to one of the four starting plans, and says which value choice moved the map the most and which countries carry the result. Groups that did extension A can instead show their burden-sharing rule next to the pooled plan.

## Reflection (due 12 Nov)

The usual format: share the repo, pick one figure and explain how it was made and what its limits are, then one paragraph with one technical and one critical insight. The neutrality reflection is a good starting point for the critical one.

## Files

- module3.qmd: the exercise, ready for a dsep-ethz/module3 template repo
- data/Combined_SR_2025.tif, data/Amphibians_RWR_2025.tif: IUCN Red List species richness rasters, bundled in the repo (no pull needed)
- cache/europe_grid.tif: richness + carbon + NCP, already pulled onto one masked grid, as a backup if the network is slow in class (population and the country raster are built in the exercises)

Tested end to end on 29 Oct with prioritizr 9.0.1, highs 1.15.1.1 and terra 1.9.50. The full render takes about 3 minutes, including the collapsed solutions. Results with equal weights on richness, carbon and NCP, each layer rescaled to 0-1:

| Plan | Richness held | Carbon held | NCP held | People inside |
|---|---|---|---|---|
| richness | 0.36 | 0.26 | 0.28 | 29% of Europe's population |
| carbon | 0.29 | 0.73 | 0.36 | 17% |
| NCP | 0.31 | 0.38 | 0.58 | 45% |
| all three | 0.32 | 0.48 | 0.55 | 39% |

Burden sharing under the all-three plan (my own trial for extension A; the document gives no code for it): the national plan (30% in every country) holds 0.31 of richness, 0.52 of carbon and 0.46 of NCP, against 0.32, 0.48 and 0.55 for the pooled plan, and a 15% floor in every country holds 0.31, 0.50 and 0.54. The pooled plan is not better on every layer, because it maximises the rescaled sum, not each share held. Timings to warn students about: the population pull takes about 20 to 25 seconds, the national plan about 15 seconds, and a floor plan about 30 seconds.

Rescaling matters: with raw values richness (mean about 247 per cell) swamps carbon (about 14); dividing each layer by its Europe-wide total instead of rescaling to 0-1 makes the all-three plan hold 0.30 of richness, 0.67 of carbon and 0.46 of NCP. Your turn task 6 asks students to compare these.

Choices worth saying out loud in class: Russia is dropped from the polygon but Belarus and Ukraine stay in; the crop removes far-flung territories such as Svalbard; microstates smaller than a 10 km cell vanish; and cells are assigned to countries by their centre. The `wdpar` route for extension B has not been tested.

The data URLs are written out in full in the document and point at Source Cooperative's AWS bucket (s3.us-west-2.amazonaws.com/us-west-2.opendata.source.coop/cboettig/...). On 29 Oct the https://data.source.coop/cboettig/... endpoint for the same files stalled on large reads (about 6 MB in 60 seconds, against 3 seconds for 20 MB from the bucket), so test it before class if you want to switch back. The NCP and population READMEs list the same files on s3-west.nrp-nautilus.io, which also works if AWS is ever a problem.
