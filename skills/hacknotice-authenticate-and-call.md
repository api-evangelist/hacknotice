---
name: Authenticate against the HackNotice API
description: Obtain a HackNotice session and make a first correctly-formed call, avoiding the three mistakes this API invites — the wrong Authorization prefix, a missing Content-Type, and exceeding the 1 request/second governor.
api: openapi/hacknotice-openapi.yml
operations: [postSignIn, post2FaSignIn, getSignOut, getSignOutAllSessions]
method: generated
source: openapi/hacknotice-openapi.yml + https://api-docs.hacknotice.com
---

# Authenticate against the HackNotice API

Base URL: `https://extensionapi.hacknotice.com`

Before anything else: HackNotice grants API access to approved accounts only, and requires a
30-minute consultation call before use. If you do not have credentials, this skill cannot proceed —
stop and tell the user to contact HackNotice.

## Pick an auth mode

HackNotice has two, and they are not interchangeable.

1. **API key + email + password.** Call `postSignIn` (`POST /auth/sign_in`) with the form fields
   `email` and `password`, sending the `apikey` header. It returns a JWT.
   If the account has 2FA enabled, use `post2FaSignIn` (`POST /auth/twofa_sign_in`) instead.
2. **Integration key.** Send a single header `X-HackNotice-Integration-Key: hn_ik_…` on every
   request. No sign-in round trip. This is the same credential the HackNotice MCP server uses.

## The three rules that break naive clients

1. **The Authorization prefix is `JWT `, not `Bearer `.**
   Send `Authorization: JWT <token>`. A generic OAuth or Bearer client will fail with
   `401 {"message":"Unauthorized user!"}` and give you no hint why.
2. **`Content-Type` is required, and the default is form encoding.**
   Most operations take `application/x-www-form-urlencoded`, not JSON. HackNotice warns in its own
   documentation that its sample code omits this header. Send it explicitly, every time.
3. **The rate limit is 1 request per second, globally.**
   Self-throttle. No `X-RateLimit-*`, `RateLimit-*` or `Retry-After` header is documented, so you
   will get no runtime signal before you are governed.

## Verify

Prefer the MCP tool `hacknotice_verify_credentials` when you are working through
`https://mcp.hacknotice.com:13330/mcp` — it exists specifically to confirm configuration before you
spend calls.

## Finish cleanly

- `getSignOut` (`GET /auth/sign_out`) ends the current session.
- `getSignOutAllSessions` (`GET /auth/sign_out_all`) ends every session for the account. Treat this
  as disruptive: it will log the human user out too, and there is no undo.

## Error shape

Every failure is a flat `{"message": "<text>"}` with no code, type or title. You cannot branch on
error identity — match on HTTP status, not on the message string.
See `errors/hacknotice-problem-types.yml`.
