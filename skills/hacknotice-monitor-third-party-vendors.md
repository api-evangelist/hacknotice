---
name: Monitor third-party vendors for breaches
description: Add vendors to the HackNotice third-party watchlist, pull the alerts raised against them, and triage what comes back. The core third-party-risk flow.
api: openapi/hacknotice-openapi.yml
operations: [postCreateADoc3, postSearchForADomain, getReadAPageOfDocuments6, getReadADoc4, putUpdateADocTags, deleteADoc4, postReadThirdPartyAlertsPage, getReadAPageOfAlertsSortedByPublishedDate, getReadCountForAlerts, postSearchDomainWithinAlerts, getReadADocument9, putUpdateADocument, getReadRansomwareHarvestersAll]
mcp_tools: [hacknotice_third_party_watchlist_search_domain, hacknotice_third_party_watchlist_get_watchlist_domains, hacknotice_third_party_watchlist_get_by_id, hacknotice_third_party_watchlist_update_by_id, hacknotice_third_party_alerts]
method: generated
source: openapi/hacknotice-openapi.yml + mcp/hacknotice-mcp-tools.json
---

# Monitor third-party vendors for breaches

Authenticate first — see `hacknotice-authenticate-and-call.md`.

## 1. Check before you add

Call `postSearchForADomain` (`POST /hackwatchlist/search`) with the vendor domain, or the MCP tool
`hacknotice_third_party_watchlist_search_domain`. The watchlist has no documented uniqueness
constraint, so searching first is how you avoid duplicates.

## 2. Add the vendor

`postCreateADoc3` (`POST /hackwatchlist/create`).

## 3. List what is being watched

- REST: `getReadAPageOfDocuments6` (`GET /hackwatchlist/page/{pageNum}`), or
  `postReadAPageOfDocuments2` for the same page with filter options.
- Count: `getTheCountOfDocuments4` (`GET /hackwatchlist/count`).
- MCP: `hacknotice_third_party_watchlist_get_watchlist_domains`.

Pagination is a **path segment**, not a query parameter: `/page/1`, `/page/2`. There is no
`page_size` parameter. Size the result set with the sibling `/count` route before you walk pages.

## 4. Pull the alerts

- `postReadThirdPartyAlertsPage` (`POST /hackalerts/page/{pageNum}`) — the filtered page.
- `getReadAPageOfAlertsSortedByPublishedDate` (`GET /hackalerts/page/{pageNum}`) — the plain page.
- `getReadAPageOfAlertsSortedByTimestamp` (`GET /hackalerts/alertedpage/{number}`) — sorted by when
  HackNotice alerted, which is not the same as when the breach was published. Choose deliberately.
- `getReadCountForAlerts` (`GET /hackalerts/count`) before paging.
- `postSearchDomainWithinAlerts` (`POST /hackalerts/search`) to search within alerts.
- `getReadRansomwareHarvestersAll` (`GET /hackalerts/harvester/all`) for the ransomware-source view.

Via MCP, `hacknotice_third_party_alerts` takes a `savedSearchJson` payload obtained from
`hacknotice_list_saved_searches` — that is the intended way to apply a saved filter rather than
rebuilding one.

## 5. Time windows

If you are using the MCP surface, the window rules are strict and worth stating: `timeRange` accepts
`lastDay | lastWeek | lastMonth`; `startDate`/`endDate` take ISO 8601; `hoursAgo` accepts **8–72
only** — values under 8 are silently coerced to 8, and over 72 is rejected in favour of a date range.

## 6. Triage

`getReadADocument9` (`GET /hackalerts/{alertId}`) reads one alert; `putUpdateADocument`
(`PUT /hackalerts/{alertId}`) updates it. `putFixHackalertsForAListOfAlertIds` (`PUT /hackalerts`)
marks a list of alerts fixed in one call — prefer it over a loop, given the 1 rps governor.

## Destructive operations — no undo

`deleteADoc4` (`DELETE /hackwatchlist/{watchlistId}`) and
`postDeleteHackalertsForAHackwatchlistId` (`POST /hackalerts/hackwatchlist/remove`) are permanent.
HackNotice publishes **no** restore, undelete or retention window for either. Confirm with the user
before calling them. See the `reversibility` block in `conventions/hacknotice-conventions.yml`.
