---
title: "api.diff — Diff an API's declared contract against payloads it actually returned; classify the drift."
url: "https://www.fluentedi.com/v1/tools/api.diff"
date: "2026-08-29"
feed_url: "https://www.fluentedi.com/feed.xml"
---
Answers 'is this endpoint keeping its promise' — the failure mode where docs say one thing and the live API returns another, the schema validates, and nothing errors. Give it the declared JSON Schema (or a baseline payload to infer one from) plus one or more observed payloads. Returns a verdict, the inferred observed schema, and classified findings: type_mismatch, missing_field and…
