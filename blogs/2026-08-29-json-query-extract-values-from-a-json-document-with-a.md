---
title: "json.query — Extract values from a JSON document with a JSONPath expression."
url: "https://www.fluentedi.com/v1/tools/json.query"
date: "2026-08-29"
feed_url: "https://www.fluentedi.com/feed.xml"
---
Runs a JSONPath query and returns the matching values with their concrete paths. Supports property access, array indexing (including negative indices), slices, wildcards, recursive descent (`..`) and filters (`[?(@.price > 10)]`). Pull three fields out of a large API response without carrying the whole document through context.
