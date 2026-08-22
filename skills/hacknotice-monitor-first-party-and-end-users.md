---
name: Monitor first-party domains and end-user credentials
description: Watch the domains a company owns and the identities of its people, and pull the credential and leak alerts raised against them.
api: openapi/hacknotice-openapi.yml
operations: [getReadDocuments, postSearchDomainWithinWatchlist, getReadAPage, getReadADoc8, postReadFirstPartyAlertsPage, postReadCountForAnAccount, postSearchHitValue, getReadAnAlert, putUpdateAnAlert, putFixDomainalertsForAListOfAlertIds, postCreateADoc6, postSearchForAnEmail, getReadAPageOfDocuments7, getReadADoc10, deleteADoc8, postReadEndUserAlertsPage, postSearchEmailWithinAlerts, getReadADocument10, putUpdateADocument2, putFixEnduseralertsForAListOfAlertIds]
mcp_tools: [hacknotice_first_party_watchlist_get_watchlist_items, hacknotice_first_party_watchlist_search_item, hacknotice_first_party_watchlist_get_by_id, hacknotice_first_party_alerts, hacknotice_end_user_watchlist_get_many, hacknotice_end_user_watchlist_search_for_email, hacknotice_end_user_watchlist_add_item_to_watchlist, hacknotice_end_user_watchlist_get_by_id, hacknotice_end_user_watchlist_delete_by_id, hacknotice_end_user_alerts]
method: generated
source: openapi/hacknotice-openapi.yml + mcp/hacknotice-mcp-tools.json
---

# Monitor first-party domains and end-user credentials

Authenticate first — see `hacknotice-authenticate-and-call.md`.

These are two separate services with the same shape. **First-party** watches domains the company
owns. **End-user** watches individual identities, usually business email addresses. They are
licensed separately, so an empty result may mean "not subscribed", not "nothing found".

## First-party domains

- List: `getReadDocuments` (`GET /domainwatchlist`) or `getReadAPage`
  (`GET /domainwatchlist/page/{pageNum}`); `postReadAPageWithParameters` for a filtered page.
- Search: `postSearchDomainWithinWatchlist` (`POST /domainwatchlist/search`).
- Read one: `getReadADoc8` (`GET /domainwatchlist/{watchlistId}`).
- Count: `getTheCountOfAllDocuments` (`GET /domainwatchlist/count`).

Alerts:

- `postReadFirstPartyAlertsPage` (`POST /domainalerts/page/{pageNum}`) is the main page.
- `postReadCountForAnAccount` (`POST /domainalerts/count`) sizes it.
- `getReadAlertsLastDate` (`GET /domainalerts/lastdate`) tells you whether anything is new before
  you page at all — the cheapest poll available, and the right first call under a 1 rps limit.
- Per-domain: `getReadPageForADomain` (`GET /domainalerts/{alertId}/page/{pageNum}`),
  `getReadCountForADomain`, `getReadAlertsLastDateForADomain`.
- Drill into what leaked: `/domainalertscreds` and `/domainalertsleaks` are the credential and leak
  sub-resources.
- Bulk fix: `putFixDomainalertsForAListOfAlertIds` (`PUT /domainalerts`).
- Deleted alerts are retrievable: `getReadPageOfDeletedAlertsForAnAccount`
  (`GET /domainalerts/deleted/page/{pageNum}`). This is a read of the deleted set, not a restore —
  HackNotice publishes no route that puts a deleted alert back.

## End users

- Add: `postCreateADoc6` (`POST /enduserwatchlist/create`).
- Search first: `postSearchForAnEmail` (`POST /enduserwatchlist/search`).
- List: `getReadAPageOfDocuments7` (`GET /enduserwatchlist/page/{pageNum}`).
- Read one: `getReadADoc10` (`GET /enduserwatchlist/{watchlistId}`).

Alerts:

- `postReadEndUserAlertsPage` (`POST /enduseralerts/page/{pageNum}`).
- `postSearchEmailWithinAlerts` (`POST /enduseralerts/search`) and the paged
  `postSearchEmailWithinAlertsByPage`.
- `getReadCountForANumberOfPreviousDaysOfAlerts` (`GET /enduseralerts/count/{number}`) for a
  trailing-days count.
- `putUpdateAllDocumentsForAnEmail` (`PUT /enduseralerts/updateall`) updates every alert for one
  address at once.

## Handling people's data

These endpoints return exposed credentials and personal information about named individuals. Return
only what the user asked for, do not echo credential values into logs or chat transcripts, and do
not enumerate a whole roster to answer a question about one person.

## Destructive operations — no undo

`deleteADoc8` (`DELETE /enduserwatchlist/{watchlistId}`) removes an end user from monitoring
permanently. Re-adding with `postCreateADoc6` starts monitoring again but is a new record — HackNotice
does not state that prior alert history returns. Confirm before deleting.
