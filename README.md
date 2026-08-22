# HackNotice

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

HackNotice is an external threat-intelligence and cyber-risk platform founded in 2018 and
headquartered in Austin, Texas. It continuously collects intelligence from ransomware groups,
infostealer malware logs, data breaches, dark-web marketplaces, hacker forums and public
disclosures, then matches that intelligence against the domains, employees, customers and vendors an
organization asks it to watch. The product is organized around first-party domain monitoring,
third-party vendor risk monitoring, end-user credential monitoring and threat research, plus
AI-assisted vendor security assessments.

## Public API surface

| Surface | Where |
|---|---|
| REST API base | `https://extensionapi.hacknotice.com` |
| API documentation | https://api-docs.hacknotice.com (a Postman collection, 262 requests) |
| MCP server (remote, Streamable HTTP) | `https://mcp.hacknotice.com:13330/mcp` — 80 tools |
| Help centre | https://hacknotice.zendesk.com/hc/en-us |
| GitHub organization | https://github.com/HackNotice |

HackNotice publishes **no OpenAPI**. `openapi/hacknotice-openapi.yml` in this repository is an API
Evangelist derivation of HackNotice's own published Postman collection — 202 paths, 246 operations —
not a provider artifact. Its provenance is recorded in `info.description` and in
`overlays/hacknotice-openapi-overlay.yaml`.

HackNotice's MCP server answers `initialize` and `tools/list` **anonymously**, so its full tool
catalogue and every JSON Schema `inputSchema` are public; `tools/call` is gated on a per-user
`X-HackNotice-Integration-Key`. The verbatim tool manifest is saved at
`mcp/hacknotice-mcp-tools.json`, and `mcp/hacknotice-tool-crosswalk.yml` binds 54 of the 80 tools to
the REST operations they declare as their backing routes.

REST API access itself is granted to approved accounts only, after a 30-minute consultation call.

## Measured gaps

No `/.well-known` document on any host, no `security.txt`, no OAuth/OIDC metadata, no A2A agent card,
no status page, no SLA, no API changelog, no deprecation policy (though a `Deprecated` folder exists
in the published collection), no idempotency contract, no reversal operation or window for any
destructive operation, no published compliance program or trust centre, errors that are a flat
`{"message": "..."}` rather than RFC 9457, and no STIX/TAXII/MISP/OpenC2/CVE interchange in a
threat-intelligence contract.

## Links

- Website: https://hacknotice.com/
- Pricing: https://hacknotice.com/pricing/
- Blog: https://hacknotice.com/category/blog/ (RSS https://hacknotice.com/feed/)
- Terms: https://hacknotice.com/businesstandc/ · Privacy: https://hacknotice.com/privacy/
- Support: support@hacknotice.com
