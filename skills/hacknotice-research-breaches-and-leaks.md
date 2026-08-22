---
name: Research breaches, leaks and threat-actor chatter
description: Investigate what happened to a named company or domain using HackNotice's research database — global breaches, credential leaks, leaked files, forum chatter and correlated threat search.
api: openapi/hacknotice-openapi.yml
operations: [postCountSearchTerm, postSearchTermAndPage, postCountSearchFilenameTerm, postSearchFilenameTermAndPage, postCountWordPool, postSearchWordPoolAndPage, postSearchAll2, postGetByFilename, getReadADocument2]
mcp_tools: [search_global_breaches, search_exposure, search_chatter, search_credential_leaks, search_leaked_files, search_correlated_leaks, hacknotice_list_saved_searches, hacknotice_research_phrase_alerts, hacknotice_research_wordpool_alerts]
method: generated
source: openapi/hacknotice-openapi.yml + mcp/hacknotice-mcp-tools.json
---

# Research breaches, leaks and threat-actor chatter

Authenticate first — see `hacknotice-authenticate-and-call.md`.

This is the one flow where the **MCP surface is materially richer than the REST surface**. Six of the
research tools — `search_global_breaches`, `search_exposure`, `search_chatter`,
`search_credential_leaks`, `search_leaked_files`, `search_correlated_leaks` — have no public REST
operation behind them at all. If you have MCP access, use it here. See
`mcp/hacknotice-tool-crosswalk.yml` for the full divergence.

## Answering "what happened to {company}?"

HackNotice's own MCP server instructs: for ransomware attacks, data breaches, or "what happened to
{company/domain}" questions, call `search_global_breaches` **first**. It returns breach details plus
`recommendations.workflowActions`, which suggest the next step — open an assessment, add to a
watchlist. Follow that recommendation rather than inventing a next step.

## The rest of the research toolkit

- `search_exposure` — structured leak records. Not chatter.
- `search_chatter` — breach/leak discussion and forum posts.
- `search_credential_leaks` — credential exposure specifically.
- `search_leaked_files` — files recovered from leaks.
- `search_correlated_leaks` — correlated threat search across sources.

Pick the narrowest one that answers the question. They overlap but are not interchangeable, and each
call costs you a second against the governor.

## REST research

Count before you page — every research search has a count sibling:

- Term: `postCountSearchTerm` (`POST /research/count/term`) then
  `postSearchTermAndPage` (`POST /research/search/term/page/{pageNum}`).
- Filename: `postCountSearchFilenameTerm` then `postSearchFilenameTermAndPage`.
- Word pool: `postCountWordPool` (`POST /research/count/pool`) then `postSearchWordPoolAndPage`.

Leak files: `postSearchAll2` (`POST /leakfile/search`) searches all, `postGetByFilename`
(`POST /leakfile/filename`) finds by name, `getReadADocument2` (`GET /leakfile/{documentId}`) reads
one.

## Saved searches

`hacknotice_list_saved_searches` returns saved-search payloads. Pass the JSON straight into the alert
tools as `savedSearchJson` or `researchSavedSearchJson` — do not try to reconstruct a filter by hand.

## Handling what comes back

These endpoints return breached credentials, personal data and threat-actor material about real
people and real companies. Summarise; do not dump raw leak contents into a chat transcript or a log.
Do not use the research surface to look up an individual who is not part of the monitored scope the
user is responsible for.

## Read-only

Everything in this skill is a read. There is nothing here to undo.
