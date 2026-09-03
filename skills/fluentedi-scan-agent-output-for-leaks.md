---
name: fluentedi-scan-agent-output-for-leaks
description: Scan text and URLs for credentials, tokens and personal data before an agent pastes logs into a model, a ticket or a chat channel, and get a redacted version back.
api: FluentEDI Tools API
base_url: https://fluentedi.com
authentication: none
operations:
  - secret_scan_post
  - url_scan_get
  - text_unicode_post
  - shell_quote_get
generated: '2026-09-03'
method: generated
source: openapi/fluentedi-openapi.json, https://fluentedi.com/recipes/secrets-leaking-into-agent-logs
---

# Check before you paste

Agents move logs, environment files and stack traces into models, tickets and chat. Anything in a
URL or an environment variable goes with them, and the leak is discovered later by someone else.

## 1. Scan the text

    POST /v1/secret/scan                                   -> operationId secret_scan_post

Run this over any log, diff, config file or stack trace before it leaves the agent's context.

## 2. Scan the URL separately

    GET /v1/url/scan?url=...                               -> operationId url_scan_get

Credentials hide in query strings, fragments and userinfo components. This detects them and returns
a redacted URL you can safely quote in a ticket.

## 3. Two related checks worth running on untrusted text

- `POST /v1/text/unicode` (`text_unicode_post`) — finds invisible characters, bidi overrides and
  script confusables. Text that renders as one thing and parses as another is a prompt-injection and
  homograph vector, not a typography problem.
- `GET /v1/shell/quote` (`shell_quote_get`) — quote a value safely for a shell, and see what would
  have gone wrong unquoted, before constructing a command from model output.

## Important limits

The service is stateless and stores nothing — /privacy states request and response bodies are
processed in memory and discarded, and never used for training. Even so, scanning is not the same as
sanitising: treat a positive finding as a reason to rotate the credential, not merely to redact the
line. A negative result means no known pattern matched, not that the text is safe.

## Reference

- https://fluentedi.com/recipes/secrets-leaking-into-agent-logs
