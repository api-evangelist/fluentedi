---
name: fluentedi-delivery-window-arithmetic
description: Establish the actual current time, and determine whether an instant falls inside a delivery or ship window across timezones — including by how much it misses — instead of reasoning about dates.
api: FluentEDI Tools API
base_url: https://fluentedi.com
authentication: none
operations:
  - time_now_get
  - time_convert_get
  - time_add_get
  - time_diff_get
  - time_window_get
  - cron_next_get
  - time_zones_get
generated: '2026-09-03'
method: generated
source: openapi/fluentedi-openapi.json, https://fluentedi.com/recipes/agent-timezone-and-deadline-errors
---

# Never reason about a deadline — compute it

A language model has no clock, and its intuition about whether an instant falls inside a window is
unreliable in exactly the way that produces no error: the window comparison silently succeeds
against the wrong bounds, the ship window is missed, and the only symptom is a chargeback later.

## 1. Get the real time first

    GET /v1/time/now?timezone=America/New_York             -> operationId time_now_get

Returns UTC offset, ISO week, day of year, weekend flag and DST state. Pass `also` as a JSON array
to get several zones in one call:
`?timezone=America/Los_Angeles&also=["Europe/London","Asia/Kolkata"]`.

Timezones must be IANA identifiers. An unknown one returns `invalid_input` and points you at
`GET /v1/time/zones` (`time_zones_get`), which lists and searches every supported identifier.

## 2. Test the window, do not compare strings

    GET /v1/time/window                                    -> operationId time_window_get

This is the operation that matters. It answers the three questions a deadline actually poses: is
this instant inside the window, how long until it opens or closes, and — if it is outside — by how
much it missed. It can also compare two windows for overlap. Use it instead of assembling a
comparison from timestamps you formatted yourself.

## 3. Supporting arithmetic

- `GET /v1/time/convert` (`time_convert_get`) — a stated local time in other zones, or a Unix
  timestamp into a human zone.
- `GET /v1/time/add` (`time_add_get`) — calendar-aware offsets, including `business_days`, for
  net-30 style dates.
- `GET /v1/time/diff` (`time_diff_get`) — duration between two dates in every unit.
- `GET /v1/cron/next` (`cron_next_get`) — validate a cron expression, get it explained in English,
  and list its next fire times before you trust a schedule.

## Batching

When a shipment has many windows to test, POST up to 20 calls at once:

    POST /v1/batch
    { "calls": [ { "tool": "time.window", "args": { ... } }, ... ] }

More than 20 returns HTTP 400 with code `too_many_calls`; split the batch.

## Reference

- https://fluentedi.com/recipes/agent-timezone-and-deadline-errors
