---
name: fluentedi-repair-and-hash-json
description: Recover usable JSON from a model's malformed output, then canonicalize it under RFC 8785 and content-address it with SHA-256 and a CIDv1 so two systems can agree they hold the same document.
api: FluentEDI Tools API
base_url: https://fluentedi.com
authentication: none
operations:
  - json_repair_post
  - json_canonical_post
  - json_query_get
  - json_diff_post
  - json_schema_post
  - api_diff_post
generated: '2026-09-03'
method: generated
source: openapi/fluentedi-openapi.json, https://fluentedi.com/recipes/llm-produced-invalid-json
---

# Repair it, then make it comparable

Models emit JSON wrapped in prose, fenced in markdown, with trailing commas, single quotes, Python
literals, or simply cut off. All of that is mechanically fixable, and guessing at the fix inside the
model is how a second broken document gets produced.

## 1. Repair

    POST /v1/json/repair                                   -> operationId json_repair_post

When the document cannot be repaired, the response pinpoints the exact line and column where it
broke — which is the difference between a retry with information and a retry with hope.

## 2. Canonicalize and content-address

    POST /v1/json/canonical                                -> operationId json_canonical_post
    { "json": {...}, "include_canonical": true }

RFC 8785 JSON Canonicalization Scheme, plus a SHA-256 digest and a CIDv1. Two services that
canonicalize before hashing agree on whether they hold the same document; two that hash raw bytes
disagree over key order and whitespace. Set `include_canonical` false when you only want the digest.

## 3. Interrogate and compare

- `GET /v1/json/query?json=...&path=$.items[*].id` (`json_query_get`) — JSONPath extraction rather
  than string slicing.
- `POST /v1/json/diff` (`json_diff_post`) — structural difference as RFC 6902-style operations.
- `POST /v1/json/schema` (`json_schema_post`) — infer a schema from an example, useful for building
  the baseline the next step needs.

## 4. Catch an API that stopped keeping its promise

    POST /v1/api/diff                                      -> operationId api_diff_post

Give it a declared JSON Schema (or a known-good baseline payload) plus one or more payloads the
endpoint actually returned. It classifies the drift: `type_mismatch`, `missing_field` and
`enum_violation` are `block`; `nullable_in_practice`, `format_mismatch`, `undeclared_field` and
`extra_field` are `warn` — each with a JSON Pointer path and how many samples it affected. Declared
keywords outside the checked subset come back in `ignored_keywords` rather than being silently
trusted. This is the failure where the docs say one thing, the live API returns another, and nothing
raises an error.

## Notes

Stateless: nothing you send is stored, so you hold the baseline. Every call is idempotent and
unauthenticated. Payload ceilings are declared in the contract (1,000,000 characters for the JSON
tools).
