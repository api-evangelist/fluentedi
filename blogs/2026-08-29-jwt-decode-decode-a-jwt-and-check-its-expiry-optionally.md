---
title: "jwt.decode — Decode a JWT and check its expiry, optionally verifying an HMAC signature."
url: "https://www.fluentedi.com/v1/tools/jwt.decode"
date: "2026-08-29"
feed_url: "https://www.fluentedi.com/feed.xml"
---
Splits a JSON Web Token, decodes header and payload, and converts every standard time claim (exp, iat, nbf, auth_time) into readable dates with a live expiry verdict. Supply `secret` to verify an HS256/384/512 signature. Decoding never validates trust on its own — an unverified token is just base64 text that anyone can forge.
