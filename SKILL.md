---
name: asta-skill
description: Domain expertise for Ai2 Asta MCP tools (Semantic Scholar corpus). Intent-to-tool routing, safe defaults, workflow patterns, and pitfall warnings for academic paper search, citation traversal, and author discovery.
license: MIT
homepage: https://github.com/Agents365-ai/asta-skill
compatibility: Requires an MCP-capable host (Claude Code, Codex, Cursor, Windsurf, Hermes, OpenClaw) with the Asta MCP server registered at https://asta-tools.allen.ai/mcp/v1 using an x-api-key header. The skill does not make HTTP calls itself.
platforms: [macos, linux, windows]
metadata: {"openclaw":{"requires":{"env":["ASTA_API_KEY"]},"emoji":"🔭","mcp":{"name":"asta","type":"http","url":"https://asta-tools.allen.ai/mcp/v1","headers":{"x-api-key":"${ASTA_API_KEY}"}}},"hermes":{"tags":["asta","semantic-scholar","academic","paper-search","citation","mcp"],"category":"research","requires_tools":["mcp"],"related_skills":["semanticscholar-skill","zotero-research-assistant","literature-review"]},"author":"Agents365-ai","version":"0.2.1"}
---

# Asta MCP — Academic Paper Search

Asta is Ai2's Scientific Corpus Tool, exposing the Semantic Scholar academic graph over MCP (streamable HTTP transport). This skill tells agents **which Asta tool to call for which intent**, and how to compose them into useful workflows.

- **MCP endpoint:** `https://asta-tools.allen.ai/mcp/v1`
- **Auth:** `x-api-key` header (request key at https://share.hsforms.com/1L4hUh20oT3mu8iXJQMV77w3ioxm)
- **Transport:** streamable HTTP

## Prerequisite Check

Before invoking any tool, verify the Asta MCP server is registered in the host agent. Tool names will be prefixed by the MCP server name chosen at install time (commonly `asta__<tool>` or `mcp__asta__<tool>`). If no Asta tools are visible, direct the user to the **Installation** section below.

## Tool Map — Intent → Asta Tool

| User intent | Asta tool | Notes |
|---|---|---|
| Broad topic search | `search_papers_by_relevance` | Supports venue + date filters |
| Known paper title | `search_paper_by_title` | Optional venue restriction |
| Known DOI / arXiv / PMID / CorpusId / MAG / ACL / SHA / URL | `get_paper` | Single-paper lookup |
| Multiple known IDs at once | `get_paper_batch` | Batch lookup — prefer over N sequential `get_paper` calls |
| Who cited paper X | `get_citations` | Citation traversal with filters, paginated |
| Find author by name | `search_authors_by_name` | Returns profile info |
| An author's publications | `get_author_papers` | Pass author id from previous call |
| Find passages mentioning X | `snippet_search` | ~500-word excerpts from paper bodies |

All tools accept **date-range filters** and **field selection** — pass them whenever the user's intent constrains scope (e.g., "recent", "since 2022", "at NeurIPS").

### ⚠️ `fields` parameter — avoid context blowups

`get_paper` / `get_paper_batch` accept a `fields` string. **Never request `citations` or `references`** via `fields` — a single highly-cited paper (e.g. *Attention Is All You Need*) returns 200k+ characters and will overflow the agent's context window. Use the dedicated `get_citations` tool for forward citations (it paginates). Asta does not provide a dedicated `get_references` tool — to retrieve a paper's reference list, use `get_paper` with `fields=references` only for papers you know have a small reference list (typically < 100).

Safe default `fields` for `get_paper`:
```
title,year,authors,venue,tldr,url,abstract
```
Add `journal`, `publicationDate`, `fieldsOfStudy`, `isOpenAccess` only when needed.

## Workflow Patterns

### Pattern 1 — Topic Discovery
1. `search_papers_by_relevance(query, year="2022-", venue=?)` → initial hits
2. Rank/present top N by citationCount + recency
3. Offer follow-ups: `get_citations` on the most influential, or `snippet_search` for specific claims

### Pattern 2 — Seed-Paper Expansion
1. `get_paper(DOI|arXiv|...)` → verify seed
2. `get_citations(paperId)` → forward expansion
3. Optionally `search_papers_by_relevance` with seed title terms for sideways discovery
4. Deduplicate by paperId before presenting

### Pattern 3 — Author Deep-Dive
1. `search_authors_by_name(name)` → pick correct profile (disambiguate by affiliation)
2. `get_author_papers(authorId)` → full publication list
3. Filter client-side by topic keywords or date

### Pattern 4 — Evidence Retrieval
1. `snippet_search(claim_query)` → find passages making/supporting a claim
2. For each hit, optionally `get_paper(id)` for full metadata

## Output & Interaction Rules

- Always report **total count** and **which tool was used**.
- Present top 10 as a table (title, year, venue, citations), then details for the most relevant.
- If the user writes in Chinese, present summaries in Chinese; keep titles in original language.
- After results, offer: **Details / Refine / Citations / Snippet / Export / Done**.

## Critical Rules

- **Prefer batched intent over ping-pong.** If the user's question needs two independent lookups, issue them as parallel MCP tool calls in one turn, not sequentially.
- **Never guess IDs.** If a user gives a fuzzy title, use `search_paper_by_title` before `get_paper`.
- **Respect rate limits.** An API key buys higher limits but not unlimited — stop expanding citation graphs beyond what the user asked for.
- **Do not fabricate fields.** If Asta returns null `abstract` or `venue`, say so rather than inventing.

## Relationship to `semanticscholar-skill`

Both wrap the Semantic Scholar corpus, but target different runtimes:

| | `semanticscholar-skill` | `asta-skill` |
|---|---|---|
| Transport | Python + direct REST (`s2.py`) | MCP (streamable HTTP) |
| Host needs | `S2_API_KEY` + Python | Asta MCP registered in host |
| Best for | Scripted batch workflows, custom filters | Zero-code agent integration (Claude Code, Codex, Cursor, Windsurf, OpenClaw) |
| Auth | `S2_API_KEY` | `ASTA_API_KEY` via `x-api-key` header |

Use `asta-skill` when the host agent supports MCP; fall back to `semanticscholar-skill` for scripted/pipeline work.

---

## Installation

Quick-start (Claude Code):

```bash
export ASTA_API_KEY="..."   # request at https://share.hsforms.com/1L4hUh20oT3mu8iXJQMV77w3ioxm
claude mcp add -t http -s user asta https://asta-tools.allen.ai/mcp/v1 \
  -H "x-api-key: $ASTA_API_KEY"
```

For other platforms (Codex, Cursor, Windsurf, Hermes, LM Studio, OpenClaw), see the full install recipes in [README.md](README.md#mcp-server-registration).

## Verification

After installation, ask the agent: *"Use Asta to get the paper ARXIV:1706.03762 with fields title,year,authors,venue,tldr."* A successful call returns *Attention Is All You Need*, NeurIPS 2017, Vaswani et al. If the agent reports no Asta tools, the MCP server is not registered — re-check the config file path and restart the host.

## Troubleshooting

| Symptom | Cause | Fix |
|---|---|---|
| `401 Unauthorized` | Missing or invalid `x-api-key` | Verify `ASTA_API_KEY` is set and header is forwarded |
| `429 Too Many Requests` | Rate limit hit | Slow down / batch; ensure API key is attached (unauth'd limits are lower) |
| No Asta tools visible | MCP server not registered in host | Re-run install step, restart agent |
| Empty `abstract` | Not all corpus papers have full text | Use `snippet_search` instead, or fall back to title + TLDR |
| Author disambiguation wrong | Common name collisions | Inspect affiliations in `search_authors_by_name` before calling `get_author_papers` |
