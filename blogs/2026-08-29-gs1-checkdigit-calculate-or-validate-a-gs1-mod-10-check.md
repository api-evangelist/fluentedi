---
title: "gs1.checkdigit — Calculate or validate a GS1 mod-10 check digit for SSCC-18, GTIN-14/13/12/8 and UPC."
url: "https://www.fluentedi.com/v1/tools/gs1.checkdigit"
date: "2026-08-29"
feed_url: "https://www.fluentedi.com/feed.xml"
---
Computes the GS1 mod-10 check digit, or validates a complete code and says what the digit should have been. Also catches the failure behind most real-world barcode and ASN mismatches: a spreadsheet dropping leading zeros or rendering the identifier in scientific notation, silently changing it. Pass a full code to validate, or the payload without its final digit to compute one.
