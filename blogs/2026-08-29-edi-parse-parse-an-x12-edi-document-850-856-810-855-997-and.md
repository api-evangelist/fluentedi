---
title: "edi.parse — Parse an X12 EDI document (850, 856, 810, 855, 997 and others) into structured JSON."
url: "https://www.fluentedi.com/v1/tools/edi.parse"
date: "2026-08-29"
feed_url: "https://www.fluentedi.com/feed.xml"
---
Reads a raw ASC X12 interchange and returns it as JSON: delimiters taken from the ISA header, the ISA/GS/ST envelope decoded, every segment split into named elements, and a document-specific summary - purchase order lines for an 850, the HL shipment hierarchy and SSCCs for an 856, invoice totals for an 810. EDI is positional and delimiter-sensitive, and unreadable without a spec table; reading…
