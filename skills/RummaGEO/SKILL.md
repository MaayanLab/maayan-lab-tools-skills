---
name: rummageo
description: Search and interpret human and mouse GEO-derived gene sets with the Ma'ayan Lab RummaGEO app. Use for RummaGEO gene-set enrichment, PubMed or metadata searches, GraphQL queries, and library downloads; not for raw RNA-seq processing or RummaGene literature-derived gene sets.
---

# RummaGEO

Use [RummaGEO](https://rummageo.com/) to connect a gene list or research topic to relevant GEO expression signatures. Choose the web interface for interactive exploration and the documented GraphQL API for reproducible retrieval.

## Biological context

RummaGEO derives human and mouse gene sets from ARCHS4-processed GEO RNA-seq studies. Its pipeline infers sample groups from metadata and computes differential expression with limma-voom. Separate upregulated and downregulated sets represent inferred comparisons. Automated grouping and control identification require inspection before biological interpretation. See the [official methods](https://rummageo.com/about).

Do not describe the collection as exhaustive coverage of GEO. Check the live release and available studies when coverage matters.

## Prepare the request

- Establish whether the user wants matches for a gene list, studies about a topic, a specific accession, or downloadable libraries.
- Determine organism from the user's experiment or metadata. Ask when ambiguous; capitalization alone is insufficient evidence of species.
- For gene lists, trim whitespace, remove blanks, and deduplicate. Preserve gene-symbol case. Report input and unique counts.
- If identifiers require conversion, use an authoritative mapping and retain a mapping table, including ambiguous and unmapped identifiers. Do not silently map orthologs or mix species.
- Keep user-provided upregulated and downregulated lists separate when direction matters. Record the original contrast and any list-selection threshold.

## Choose a search

The [user manual](https://rummageo.com/usermanual) documents these routes:

- **Gene set search:** Submit gene symbols to find overlapping signatures using Fisher's exact test. Inspect the matching signatures, their study metadata, and overlapping genes.
- **PubMed search:** Use a literature query to find associated GEO signatures. The documented workflow considers the first 5,000 returned publications; report that coverage limit when relevant.
- **Metadata search:** Search study descriptions or identifiers, then inspect the returned comparisons.

Use keyword filters to narrow relevant results and download tables as tab-delimited files. The interface also provides common-term summaries, Enrichr summaries, and hypothesis generation. Treat summaries as aids to exploration and generated hypotheses as proposals requiring evidence.

For UI automation, inspect current controls before interacting. If an interface or query has changed, consult current documentation instead of assuming historical behavior.

## Programmatic access

The documented endpoint is `https://rummageo.com/graphql`. Send GraphQL requests as JSON with query text and variables. Obtain operation definitions from the maintained [application queries](https://github.com/MaayanLab/rummageo/blob/rummageo/src/graphql/core.graphql); check the live schema when available.

1. Retrieve available backgrounds and choose the intended species. Record the selected background ID and creation date. Resolve multiple candidates against current application behavior rather than taking the first result.
2. Use the enrichment operation for a gene list, term search for metadata, or publication-ID lookup for known PMIDs.
3. Retrieve identifiers, enrichment statistics, comparison metadata, and gene-set sizes. Fetch overlap genes separately if needed.
4. Paginate to the requested scope. Keep filters fixed between pages and label partial retrieval explicitly.

Do not hard-code a background UUID from an example. Validate variable types and sorting semantics against the current schema. Check HTTP status and GraphQL-level errors; a successful HTTP response can still contain a failed query. Use request timeouts and bounded retries for transient failures. Preserve partial results and explain unresolved errors.

Prefer read-only queries for retrieval. Do not add a stored user gene set merely to run enrichment when direct querying suffices.

## Interpret the evidence

- Report raw and adjusted enrichment p-values as returned. Do not assume a multiple-testing correction method unless verified. Distinguish enrichment significance from differential-expression significance used to construct the library.
- Evaluate overlap size, set size, odds ratio, biological context, and statistical significance together. If filtering by an adjusted-p threshold, state it explicitly.
- Read comparison labels and sample metadata before calling a match concordant or reversed. Overlap with an upregulated set alone does not establish concordance for an unsigned query.
- Group related signatures by GEO study when summarizing evidence; multiple contrasts from one study are not independent replication.
- Check tissue, cell type, treatment, species, and control selection for leading hits. An enriched overlap supports an association, not a causal mechanism.
- Distinguish an empty result from a failed query, wrong organism, identifier mismatch, or incomplete retrieval.

## Downloads and reproducibility

Use the live [download page](https://rummageo.com/download) to obtain species-specific GMT libraries and companion JSON metadata. Verify current links, sizes, and release dates before downloading. Preserve library identifiers so gene sets can be joined to their metadata. For large collections, stream processing where practical and limit downloads to the requested scope.

For an analysis, retain the cleaned input, organism, contrast, query or search text, filters, background ID, retrieval date, release information when available, and raw results. Report missing information explicitly.

Return a concise findings table when useful: study accession and link, title, comparison and direction, overlap and set size, p-value, adjusted p-value, and relevant context. Separate observed results from interpretation and suggested follow-up experiments. Never fabricate hits, gene members, statistics, or successful execution.

## Sources and citation

Documentation reviewed on 2026-10-19. Recheck live documentation for changing API behavior and database coverage.

- [RummaGEO user manual](https://rummageo.com/usermanual)
- [Methods and publication citation](https://rummageo.com/about)
- [Downloads](https://rummageo.com/download)
- [MaayanLab source repository](https://github.com/MaayanLab/rummageo)

For publication use, cite Marino GB, Clarke DJB, Lachmann A, Deng EZ, and Ma'ayan A. “RummaGEO: Automatic mining of human and mouse gene sets from GEO.” *Patterns* 5(10), 101072 (2024). [Article](https://www.cell.com/patterns/fulltext/S2666-3899(24)00231-9).
