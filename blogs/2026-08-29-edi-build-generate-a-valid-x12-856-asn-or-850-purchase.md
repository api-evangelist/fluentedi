---
title: "edi.build — Generate a valid X12 856 ASN or 850 purchase order from JSON, with correct HL pointers and envelope counts."
url: "https://www.fluentedi.com/v1/tools/edi.build"
date: "2026-08-29"
feed_url: "https://www.fluentedi.com/feed.xml"
---
Compiles structured JSON into standards-valid X12. The parts that get documents rejected are exactly the parts a language model cannot hold in its head: the 856 hierarchy needs sequential HL IDs where every level names its parent's ID, and on a multi-carton, multi-SKU shipment that bookkeeping goes wrong silently. The ISA header is fixed-width — 106 characters exactly, every element space-padded…
