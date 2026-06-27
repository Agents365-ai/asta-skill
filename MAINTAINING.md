# Maintaining asta-skill

`asta-skill` is a **pure instruction pack** — it ships no code and makes no HTTP
calls. Its entire value is the accuracy of what `skills/asta-skill/SKILL.md` tells
an agent about the Asta MCP tools. When the live server's tool signatures change
(param renamed, default limit changed, a field dropped), the skill becomes
silently wrong: the agent emits malformed or ignored arguments and the user gets
worse results with no error.

The release history shows this is the dominant failure mode — v0.3.1, v0.3.2, and
v0.3.3 each corrected the *same class* of fact (per-tool param names, defaults,
silent-drop behavior). This document exists to make that drift a checked step
instead of a recurring surprise.

## Ground truth = the live MCP tool schemas

The authoritative source for every per-tool claim in `SKILL.md` is the schema the
Asta MCP server advertises for each tool. Re-pull it whenever you bump the version.

**How to pull the schemas** (any registered MCP host works):

- In an MCP host (Claude Code, Cursor, etc.) with `asta` registered, ask the host
  to list the Asta tools — each tool's parameter schema (names, defaults, required
  flags) and its description string are shown verbatim. The description strings are
  where the *behavioral* facts live (limit defaults, accepted ID types, date-range
  null-`publicationDate` behavior, the `fields` available-values list).
- Compare each tool's schema against the matching row/claim in `SKILL.md`.

## Pre-release checklist

Before tagging a new version, confirm each item against a freshly pulled schema:

- [ ] **All 8 tools still exist** with the same names: `search_papers_by_relevance`,
      `search_paper_by_title`, `get_paper`, `get_paper_batch`, `get_citations`,
      `search_authors_by_name`, `get_author_papers`, `snippet_search`.
- [ ] **Per-tool field param name** matches (`get_author_papers` → `paper_fields`;
      everything else → `fields`).
- [ ] **Default `limit`s** match the snapshot below (these drive the context-blowup
      warnings — wrong values here let the agent pull oversized responses).
- [ ] **Which tools accept `venues` / `publication_date_range`** is unchanged
      (the exceptions are the fragile part).
- [ ] **`get_paper_batch.ids` is still a JSON array** (not comma-separated).
- [ ] **`snippet_search.paper_ids` is still a comma-separated string** (≤100).
- [ ] **Undocumented pass-through fields** (`externalIds`, `citationCount`,
      `influentialCitationCount`) still return — if a release drops them, update the
      "Retrieving DOI" section to degrade gracefully.
- [ ] Bump `version` in the `SKILL.md` frontmatter `metadata` block.

## Live schema snapshot (last verified 2026-06-27)

This is the diff target. Re-pull and compare; any divergence is a `SKILL.md` edit.

| Tool | Required | Field param (default) | `limit` default | `publication_date_range` | `venues` | Notes |
|---|---|---|---|---|---|---|
| `search_papers_by_relevance` | `keyword` | `fields` (`title`) | **50** | yes | yes | — |
| `search_paper_by_title` | `title` | `fields` (`title`) | n/a | yes | yes | — |
| `get_paper` | `paper_id` | `fields` (`title`) | n/a | no | no | single paper |
| `get_paper_batch` | `ids` (**array**) | `fields` (`title`) | n/a | no | no | unresolvable ids silently dropped |
| `get_citations` | `paper_id` | `fields` (`title`) | **100** | yes | **no** | forward citations |
| `search_authors_by_name` | `name` | `fields` (**`name`**) | **100** | n/a | n/a | default returns only `name`+`authorId`; request `affiliations,paperCount,citationCount,hIndex,externalIds` |
| `get_author_papers` | `author_id` | **`paper_fields`** (`title`) | **1000** | yes | **no** | `fields=` is silently ignored |
| `snippet_search` | `query` | n/a (no `fields`) | **20** | **no** (use `inserted_before`) | yes | `paper_ids` comma-sep ≤100; ~500 words/snippet |

**Paper `fields` available list** (per the server's own description — note it is
*non-exhaustive*): `abstract, authors, citations, fieldsOfStudy, isOpenAccess,
journal, publicationDate, references, tldr, url, venue, year`. The list omits
`title` (which is the *default*), plus the pass-through fields `externalIds`,
`citationCount`, `influentialCitationCount` — all verified to return despite being
unlisted. Never request `citations` or `references` on a highly-cited paper (200k+
chars → context overflow).

## Why there is no auto-verify script

A script that drives the streamable-HTTP MCP handshake (`initialize` → session id →
`tools/list`) and diffs against an expected snapshot would automate this, but it
adds a dependency + API-key + maintenance surface to an otherwise zero-dependency
docs repo. The manual checklist above is deliberately the lighter contract. If the
schema starts churning often enough to justify it, add the script then — not
preemptively.
