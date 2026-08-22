---
name: Run an AI-scored vendor security assessment
description: Find vendors with no assessment, build one from a framework template, invite the vendor, collect their evidence files, and AI-score the result.
api: openapi/hacknotice-openapi.yml
operations: [getListAvailableFrameworks, postCreateTemplate, postListTemplatesPaginated, getReadOneTemplate, postCreateADoc4, postAPageOfDocuments, postTheCountOfDocuments, getReadADoc5, putUpdateADoc3, postProjection, postCreateInvite, postListInvitesPaginated, getReadInviteById, getReadInviteByCode, postActivateInvite, getMyInvitesInvitedUserView, putUpdateInviteById, deleteInviteById, deleteADoc5, deleteOneTemplate]
mcp_tools: [hacknotice_assessment_vendors_without_assessment, hacknotice_assessment_template_list_template_frameworks, hacknotice_assessment_list_assessments_page, hacknotice_assessment_count_assessments, hacknotice_assessment_get_assessment, hacknotice_assessment_ai_score_assessment, hacknotice_assessment_ai_score_unscored, hacknotice_assessment_search, hacknotice_assessment_data_file_list_files_page, hacknotice_assessment_data_file_download_file]
method: generated
source: openapi/hacknotice-openapi.yml + mcp/hacknotice-mcp-tools.json
---

# Run an AI-scored vendor security assessment

Authenticate first — see `hacknotice-authenticate-and-call.md`.

Assessments are the largest surface on this API: 49 of the 80 MCP tools sit here.

## 1. Find the gap

`hacknotice_assessment_vendors_without_assessment` returns watchlist vendors that have no assessment
yet. Start there rather than listing everything.

## 2. Choose a framework

`getListAvailableFrameworks` (`GET /assessmenttemplates/frameworks`) enumerates the frameworks
HackNotice supports. Then either reuse a template — `postListTemplatesPaginated`
(`POST /assessmenttemplates/page/{pageNum}`), `getReadOneTemplate`
(`GET /assessmenttemplates/{templateId}`) — or create one with `postCreateTemplate`
(`POST /assessmenttemplates`).

## 3. Create and invite

- `postCreateADoc4` (`POST /assessments`) creates the assessment.
- `postCreateInvite` (`POST /assessmentinvites`) invites the vendor.
- The vendor side activates with `postActivateInvite`
  (`POST /assessmentinvites/activate/{inviteCode}`) and can look itself up with
  `getReadInviteByCode` (`GET /assessmentinvites/code/{inviteCode}`) or list its own invites with
  `getMyInvitesInvitedUserView` (`GET /assessmentinvites/mine`).

Note the pattern: nearly every assessment resource has an `/invited` twin — events, data files,
assessments themselves. The plain route is the customer's view; the `/invited` route is the vendor's.
Do not mix them.

## 4. Always filter by vendor name

HackNotice's own MCP server instructs this explicitly: when the user names a vendor, **always** pass
`term` on `hacknotice_assessment_list_assessments_page` and `hacknotice_assessment_count_assessments`.
Never fetch all assessments unfiltered to find one company. Then sort `timestamp` descending to pick
the latest, and read it with `hacknotice_assessment_get_assessment` for the score and custom questions.

On REST, `postAPageOfDocuments` (`POST /assessments/page/{pageNum}`) takes the filter,
`postTheCountOfDocuments` (`POST /assessments/count`) sizes it, and `postProjection`
(`POST /assessments/projection`) is the closest thing this API has to sparse fieldsets — use it when
you only need a few fields across many assessments.

## 5. Evidence files

`hacknotice_assessment_data_file_list_files_page` lists them,
`hacknotice_assessment_data_file_download_file` retrieves one. The vendor uploads through
`hacknotice_invited_assessment_data_file_upload_invited_file`.

## 6. Score

`hacknotice_assessment_ai_score_assessment` scores one assessment;
`hacknotice_assessment_ai_score_unscored` sweeps everything unscored. The sweep has no documented
bound on how many it will process — check the count first, and remember the 1 rps governor applies
to whatever it fans out into.

## Destructive operations — no undo

`deleteADoc5` (`DELETE /assessments/{assessmentId}`), `deleteOneTemplate`
(`DELETE /assessmenttemplates/{templateId}`) and `deleteInviteById`
(`DELETE /assessmentinvites/{inviteId}`) are permanent. There is no restore route, no trash, and no
retention window published for any of them. Deleting a template that live assessments were built
from is the highest-consequence call in this skill — confirm explicitly before making it.
