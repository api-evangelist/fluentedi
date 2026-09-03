---
name: fluentedi-build-856-asn
description: Build a standards-valid X12 856 Advance Ship Notice from structured JSON, verify its SSCC-18 carton labels, and validate the envelope and HL hierarchy before it goes to the retailer.
api: FluentEDI Tools API
base_url: https://fluentedi.com
authentication: none
operations:
  - gs1_checkdigit_get
  - edi_build_post
  - edi_validate_post
  - edi_parse_post
generated: '2026-09-03'
method: generated
source: openapi/fluentedi-openapi.json, https://fluentedi.com/recipes/edi-856-specification
---

# Build an 856 ASN that will not be rejected

An 856 is rejected for bookkeeping, not for content: HL segments whose parent pointers do not line
up, an ISA header that is not exactly 106 characters, or SE01/CTT01/GE01/IEA01 counts that disagree
with what was actually emitted. All of that is arithmetic, and none of it should be written by hand
or by a language model. Compute it.

No API key, no account. Every call is read-only and idempotent, so a failed step can simply be
repeated.

## 1. Verify every carton's SSCC-18 before you build

Each pack in the ASN carries an SSCC-18 whose last digit is a GS1 mod-10 checksum. A spreadsheet
that dropped a leading zero produces a number that looks fine and fails at the retailer.

    GET /v1/gs1/checkdigit?code=00614141123456789        -> operationId gs1_checkdigit_get

Pass `length=18` when the code arrived without its check digit and you want it computed rather than
validated. Do this for every `sscc` you are about to put in the document.

## 2. Build the document

    POST /v1/edi/build                                    -> operationId edi_build_post

Body:

    {
      "document": "856",
      "data": {
        "shipment_id": "...",
        "ship_date": "YYYY-MM-DD",
        "orders": [ { "po_number": "...", "packs": [ { "sscc": "...", "items": [ { "upc": "...", "quantity": 1, "unit": "EA" } ] } ] } ],
        "parties": [ { "role": "...", "name": "...", "id": "..." } ]
      },
      "sender_id": "...", "sender_qualifier": "ZZ",
      "receiver_id": "...", "receiver_qualifier": "ZZ",
      "control_number": 1,
      "test_indicator": true,
      "include_envelope": true
    }

`data` is the only required field. Note the defaults the schema declares: `test_indicator` is
**true**, so the interchange is marked ISA15 = T. Set it to `false` deliberately when you are sending
production traffic — this is the one parameter in the flow with a real-world consequence, and the
API will not decide it for you. `sender_id` and `receiver_id` are capped at 15 characters (ISA06 /
ISA08) and the qualifiers at 2 (ISA05 / ISA07); the builder space-pads them to the fixed ISA widths.

`control_number` seeds ISA13, GS06 and ST02 together. Keep your own counter — the service is
stateless and will happily emit control number 1 twice.

## 3. Validate what you just built

Never trust the build step blind. Feed the output straight back:

    POST /v1/edi/validate                                 -> operationId edi_validate_post
    { "input": "<the X12 text>", "require_levels": ["S","O","P","I"] }

`require_levels` asserts the HL levels a pack-level ASN must contain — Shipment, Order, Pack, Item.
If the retailer's spec calls for a different depth, name theirs instead. The validator checks
envelope integrity and the 856 HL hierarchy, which is where rejections come from.

## 4. Read it back the way the receiver will

    POST /v1/edi/parse                                    -> operationId edi_parse_post
    { "input": "<the X12 text>", "include_segments": true }

Parsing your own output into JSON and comparing it against the source data is the cheapest possible
check that the document says what you meant.

## Error handling

Errors return `{"ok": false, "error": {"code","message"}, "parameters": <schema>, "working_examples": [...]}`.
Code `invalid_input` means read the `parameters` block in the same response and retry — everything
needed to correct the call is already in the failure body. There is no rate limit and no retry
budget to manage, but batch up to 20 calls in one request via `POST /v1/batch` when validating many
cartons at once.

## Reference

- 856 structure and a worked ASN: https://fluentedi.com/recipes/edi-856-specification
- Why HL hierarchy errors happen: https://fluentedi.com/recipes/856-asn-rejected-hl-hierarchy
- The 106-character ISA rule: https://fluentedi.com/recipes/isa-segment-must-be-106-characters
- SSCC-18 check digits: https://fluentedi.com/recipes/sscc-18-check-digit
