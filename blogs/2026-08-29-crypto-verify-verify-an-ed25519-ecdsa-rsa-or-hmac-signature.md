---
title: "crypto.verify — Verify an Ed25519, ECDSA, RSA or HMAC signature against a message and public key."
url: "https://www.fluentedi.com/v1/tools/crypto.verify"
date: "2026-08-29"
feed_url: "https://www.fluentedi.com/feed.xml"
---
Checks a detached signature and returns a single boolean you can act on. Signature verification is not something to reason about — a signature is either valid under the key or it is not, and an agent that accepts an identity claim without checking has accepted anything. Supports Ed25519 (raw 32-byte or SPKI keys), ECDSA P-256/P-384, RSA PKCS#1 v1.5 and PSS, and HMAC-SHA256/512.
