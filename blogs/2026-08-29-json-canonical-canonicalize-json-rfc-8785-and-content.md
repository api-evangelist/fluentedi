---
title: "json.canonical — Canonicalize JSON (RFC 8785) and content-address it with SHA-256 and a CIDv1."
url: "https://www.fluentedi.com/v1/tools/json.canonical"
date: "2026-08-29"
feed_url: "https://www.fluentedi.com/feed.xml"
---
Serializes a JSON document to its one canonical form — keys sorted, whitespace dropped, numbers in ECMAScript form — then hashes it. Two documents that mean the same thing produce the same digest regardless of key order or formatting, which is what makes the digest quotable as a receipt. A log line is a claim that the process which wrote it could also edit; a content address is computed from the…
