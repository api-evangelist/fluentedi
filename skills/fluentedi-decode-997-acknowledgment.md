---
name: fluentedi-decode-997-acknowledgment
description: Turn an X12 997 or 999 Functional Acknowledgment into plain language, reconcile it against the control numbers you sent, and find the documents that were never acknowledged at all.
api: FluentEDI Tools API
base_url: https://fluentedi.com
authentication: none
operations:
  - edi_acknowledge_post
  - edi_acknowledge_get
  - edi_parse_post
generated: '2026-09-03'
method: generated
source: openapi/fluentedi-openapi.json, https://fluentedi.com/recipes/edi-997-rejection-codes
---

# Read a 997 and know what actually happened

A 997 reports its verdict as bare letters and numbers — AK5 of `R` with error `5`, AK3 of `7`, AK4
element 2 code 7 — which mean nothing without the code tables. Worse, the failure that costs money
is silence: a document that was never acknowledged is not a delivered document, and nothing errors.

## 1. Decode, and reconcile in the same call

    POST /v1/edi/acknowledge                              -> operationId edi_acknowledge_post
    { "input": "<raw 997 or 999 text>", "sent": ["0001","0002","0003"] }

`input` is the raw acknowledgment (up to 500,000 characters). `sent` is the list of ST02 transaction
set control numbers **you** transmitted. Always pass it. Without `sent` you learn what the 997 talks
about; with it you learn what the 997 does *not* talk about, which is the whole point — accepted,
rejected, and never came back at all.

The GET form (`edi_acknowledge_get`) takes the same parameters as query arguments and is convenient
for a single small acknowledgment.

## 2. When the 997 itself looks wrong, parse it

    POST /v1/edi/parse                                    -> operationId edi_parse_post
    { "input": "<raw text>", "include_segments": true }

Returns the flat segment list alongside the summary, so you can see the AK1/AK2/AK3/AK4/AK5/AK9
positions the decoder read.

## What to do with the verdict

- **Accepted (AK5 = A)** — done. Record the control numbers as settled.
- **Rejected (AK5 = R or E)** — the decoded response names the failing segment position and element.
  Fix at the source and rebuild; do not hand-patch X12 text.
- **Missing** — a control number in `sent` that appears nowhere in the acknowledgment was not
  processed. Escalate it as unacknowledged rather than assuming success. This is the case a status
  check passes and a human notices six weeks later.

## Error handling

`{"ok": false, ...}` with code `invalid_input` returns the parameter schema and working examples in
the same body; correct and retry. Read-only, idempotent, no key, no rate limit.

## Reference

- Every AK segment and code: https://fluentedi.com/recipes/edi-997-specification
- What AK5, AK3 and AK4 codes mean: https://fluentedi.com/recipes/edi-997-rejection-codes
- SE01 segment count mismatches: https://fluentedi.com/recipes/se01-segment-count-mismatch
