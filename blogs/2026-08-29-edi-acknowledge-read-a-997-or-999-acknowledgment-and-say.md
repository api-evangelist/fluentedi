---
title: "edi.acknowledge — Read a 997 or 999 acknowledgment and say which documents were accepted, which were rejected, and why."
url: "https://www.fluentedi.com/v1/tools/edi.acknowledge"
date: "2026-08-29"
feed_url: "https://www.fluentedi.com/feed.xml"
---
Decodes a Functional Acknowledgment into an answer rather than a code. A 997 reports its verdict as bare letters and numbers — AK5 of R with error 5, AK3 of 7, AK4 element 2 code 7 — which say nothing without a code table. This resolves every one into plain language, points at the failing segment position and element, and, if you supply the control numbers you sent, reconciles them so you can…
