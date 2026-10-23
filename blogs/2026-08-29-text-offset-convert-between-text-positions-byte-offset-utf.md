---
title: "text.offset — Convert between text positions: byte offset, UTF-16 index, code point, grapheme, and line/column."
url: "https://www.fluentedi.com/v1/tools/text.offset"
date: "2026-08-29"
feed_url: "https://www.fluentedi.com/feed.xml"
---
One position in a string has at least five different numbers depending on who is counting. A UTF-8 byte offset, a UTF-16 code unit index (what JavaScript and LSP use), a code point index (what Python uses), a grapheme index (what a human means by 'the third character'), and a line/column pair. An emoji is one grapheme, two code units and four bytes; a flag is one grapheme and eight bytes.
