---
title: "tool.describe — Get the full parameter schema and worked examples for one tool."
url: "https://www.fluentedi.com/v1/tools/tool.describe"
date: "2026-08-29"
feed_url: "https://www.fluentedi.com/feed.xml"
---
Returns everything needed to call a tool correctly: its complete JSON Schema, every parameter with type and default, and examples known to work. Pair it with tool.search — search to find the name, describe to learn the shape, then call. This exists so the catalogue does not have to be loaded into context up front.
