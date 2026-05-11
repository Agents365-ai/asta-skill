# Usage

[中文](USAGE_CN.md)

Just describe what you want:

```
> Use Asta to get the paper with DOI 10.48550/arXiv.1706.03762

> Search Asta for recent papers on mixture-of-experts at NeurIPS since 2023

> Who cited "Attention Is All You Need"? Show me the top 20 by citation count

> Find snippets in the Asta corpus that mention "flash attention latency"

> Look up Yann LeCun on Asta and list his 2024 papers
```

The skill picks the right Asta tool, attaches safe `fields`, and follows the documented workflow patterns.

## Example: Search + Batch Download (chained with `paper-fetch`)

`asta-skill` only handles **search and metadata**; it does not download PDFs. To go from a search query to local PDFs, chain it with a `paper-fetch` skill (or any DOI-based downloader of your choice):

```
> Use Asta to find the 5 most cited papers on "single-cell ATAC-seq batch correction"
  since 2022, then hand the DOIs to paper-fetch to download all PDFs into ./papers/
```

What happens under the hood:

1. **asta-skill** → `search_papers_by_relevance` with `publication_date_range="2022:"` and `fields=title,year,authors,venue,tldr,externalIds` (note `externalIds` to expose DOI)
2. Agent extracts `externalIds.DOI` for each hit; falls back to `externalIds.ArXiv` when DOI is absent
3. **paper-fetch** → batch-resolves each DOI/arXiv ID through Unpaywall → arXiv → bioRxiv/medRxiv → PMC → SS → Sci-Hub fallback chain
4. PDFs land in `./papers/`, one per paper

`paper-fetch` is a separate skill — install it if you need download capability. `asta-skill` itself stays scoped to the Semantic Scholar corpus.

**Step 1 — Asta returns the top 5 papers with DOIs:**

![Asta search result](docs/images/asta-search.png)

**Step 2 — paper-fetch downloads all 5 PDFs into `./papers/`:**

![paper-fetch batch download](docs/images/asta-paper-fetch.png)
