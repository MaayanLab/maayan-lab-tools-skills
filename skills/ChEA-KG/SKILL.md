---
name: chea-kg
description: Explore transcription factor regulatory subnetworks and analyze gene sets with the Ma'ayan Lab ChEA-KG app. Use for TF neighborhood and path searches, ChEA3 enrichment in network context, atlas exploration, API retrieval, network exports, and routing to ChEA-KG-TS for time-series analysis.
---

# ChEA-KG

Use [ChEA-KG](https://chea-kg.maayanlab.cloud/) to investigate signed, directed relationships among human transcription factors (TFs). Choose the search interface for specific TFs and the enrichment interface for a gene list. Keep observed outputs separate from biological hypotheses.

## Prepare the request

- Identify the goal: a TF neighborhood, connections between two TFs, candidate regulators of a gene list, atlas exploration, or time-series analysis.
- Record organism, tissue or cell type, treatment, comparison, and time points when provided. Ask only for missing details that affect the analysis.
- Trim and deduplicate gene lists, preserving original identifiers and reporting input and unique counts. Use gene symbols rather than silently treating transcript IDs or probes as symbols.
- Resolve aliases and identifier conversions using authoritative mappings. Retain ambiguous and unmapped entries in a mapping report.
- ChEA3 accepts human and mouse symbols, but the ChEA-KG network is human. Verify ortholog handling for nonhuman inputs; do not equate capitalization changes with validated ortholog mapping. See [ChEA3 input guidance](https://maayanlab.cloud/chea3/).
- Preserve separate upregulated and downregulated lists when direction matters. Record the differential-expression threshold and original contrast. Do not invent cutoffs for a supplied ranked list.

## Select the workflow

### TF search

Open [Search](https://chea-kg.maayanlab.cloud/). Select a recognized starting TF; add an ending TF when investigating a connection. The [source repository](https://github.com/MaayanLab/ChEA-KG-UI) describes neighborhood and shortest-path searches.

Inspect the current controls before automating. Record the search mode, endpoints, network-size setting, and filters. For paths, inspect every edge's direction and relation before interpreting a route as a regulatory chain. A visually connected path does not itself establish a causal mechanism.

### Gene-set enrichment

Open [Enrichment](https://chea-kg.maayanlab.cloud/enrichment) and submit gene symbols. ChEA3 identifies candidate TFs, which ChEA-KG displays in a regulatory subnetwork. The page links example notebooks for preparing lists from count matrices and BED files; consult these only when preprocessing is requested.

The [help page](https://chea-kg.maayanlab.cloud/help) documents defaults of ten TFs and support in three libraries. Record actual settings. Some ranked TFs lack network representation. Edge z-scores describe background-network evidence, not query enrichment. Submitted lists persist under a UUID; treat result URLs as shareable access links.

Compare rankings with the displayed graph rather than assuming every ranked TF is visible. Record excluded or disconnected TFs when the result makes this information available.

### Atlases and time series

The main navigation exposes Cell, Cancer, MoA, and Aging atlases. Follow the current links and inspect each atlas's dataset, contrasts, and filters before selecting a result. Keep atlas-derived findings distinct from an analysis of the user's own data.

For time-series requests, follow the [Appyters page](https://chea-kg.maayanlab.cloud/appyters) to ChEA-KG-TS. That workflow examines enriched TFs across time-series RNA-seq data. Read its current input contract before preparing uploads; preserve time labels, ordering, replicates, and comparison definitions. Do not substitute a pooled gene list for a requested temporal analysis.

The same page offers a ChEA-KG Appyter for regulatory subnetworks and function predictions. Label generated function predictions as hypotheses.

## API access

Use the [official API documentation](https://chea-kg.maayanlab.cloud/help). Base URL: `https://chea-kg.maayanlab.cloud/api/enrichment`.

- POST `/addList`: multipart `list` containing newline-separated symbols; optional `description`. Retain returned `userListId`.
- GET `/view`: supply `userListId`; returned `set` and `desc` describe the stored submission.
- Subnetwork retrieval: the example POSTs JSON to the base URL using `userListId`, `min_lib`, `libraries`, and `limit`. Each library entry specifies `library` and `term_limit`; the illustrated ranking is `Integrated--meanRank`.

Documentation inconsistencies: the subnetwork heading specifies GET, while its example uses POST; `minLib` also differs from example key `min_lib`. Verify current implementation before execution.

Do not copy example IDs into real requests. Separate submission from retrieval so a retry does not unnecessarily create another stored list. For a timed-out submission, report uncertain completion rather than claiming failure or resubmitting indefinitely.

Check HTTP status, content type, and returned structure. Use timeouts and bounded retries for transient retrieval failures. Save raw responses and exact request parameters. Report unavailable fields or partial results explicitly; do not fill missing scores with zero. API instructions here are documentation-derived, not a live integration test.

## Interpret results

- ChEA3 integrated scores rank candidate TFs: lower scores are better. They are not p-values, fold changes, or probabilities of regulation. Consult [ChEA3 methods](https://maayanlab.cloud/chea3/) when interpreting library-specific statistics.
- Inspect source, target, and relation explicitly. Preserve reciprocal edges and self-loops rather than collapsing the graph into undirected pairs.
- A background edge does not establish that the interaction operates in the user's tissue or perturbation. Distinguish predicted regulation from experimentally demonstrated binding or causality.
- Enrichment does not by itself establish whether a TF is active, inhibited, or differentially expressed. Check independent expression and perturbation evidence before making such claims.
- Inspect sensitivity to node and edge filters before emphasizing hubs or motifs. A missing edge can reflect filtering or incomplete coverage.
- Do not treat an empty graph as proof that no regulators exist. Check identifiers, enrichment output, coverage, filters, and service errors.

## Export and report

Use network/table exports to preserve nodes and directed edges, and image export for presentation. Verify exported row counts and identifiers against the displayed scope.

For bulk data, use [Downloads](https://chea-kg.maayanlab.cloud/download_files). It lists network archives, RummaGEO-derived gene sets, atlas resources, and network-building notebooks. Network archives contain a node file and two edge files. Check current links, metadata, and licenses before reuse; do not assume a bulk archive matches a filtered view.

Save the cleaned list, organism, contrast, query mode, settings, retrieval date, returned ID, raw results, and exports. Avoid publishing a user's result URL as part of a public skill or example without their instruction.

When useful, return two tables:

- TF results: symbol, returned rank and score, library support, overlap, and input membership where available.
- Network evidence: source TF, target TF, relation, edge statistics, and applied filters.

Summarize leading candidates, relevant connections, limitations, and evidence needed for follow-up. Never invent TFs, edges, statistics, or successful execution. Use live data for network counts; the site's documentation contains differing historical totals.

## Sources and citations

Documentation reviewed on 2026-10-19. Recheck current pages for API changes and updated methods.

- [ChEA-KG](https://chea-kg.maayanlab.cloud/)
- [Help and API](https://chea-kg.maayanlab.cloud/help)
- [Application source](https://github.com/MaayanLab/ChEA-KG-UI)
- [Appyter workflows](https://chea-kg.maayanlab.cloud/appyters)
- [Downloads](https://chea-kg.maayanlab.cloud/download_files)

The site currently requests citation of Byrd AI et al. (2026), the ChEA-KG and ChEA-KG-TS article in *Nucleic Acids Research*: [doi:10.1093/nar/gkag508](https://doi.org/10.1093/nar/gkag508). Use the current published citation rather than the older preprint entry retained in the help page.

For ChEA3 enrichment, also cite Keenan AB et al. (2019), *ChEA3: transcription factor enrichment analysis by orthogonal omics integration*: [doi:10.1093/nar/gkz446](https://doi.org/10.1093/nar/gkz446).
