---
type: lab
platform: PortSwigger
topic: SSRF
difficulty: Apprentice
status: solved
date: 2026-10-27
tags: [portswigger, ssrf]
---

# Basic SSRF against the local server

## 🎯 Goal
Access the admin panel and delete the user `carlos` by exploiting SSRF in the stock-check feature.

## 🧠 Core idea
The "check stock" feature makes a server-side HTTP request to a URL supplied in the request, with no validation of what that URL points to. Since the request originates from the server itself, it can reach internal resources (like `localhost`) that aren't normally accessible from outside — this is classic SSRF: tricking the server into making requests on my behalf to places I couldn't reach directly.

## 🔍 Recon
- Opened a product page and clicked "Check stock."
- Intercepted the request in Burp and found it includes a full URL parameter (pointing to an internal stock-check endpoint) — a URL parameter the server fetches is the signal this is worth testing for SSRF.

## 💥 Exploitation (step by step)
1. Opened Burp Suite, turned on interception.
2. Clicked "Check stock" on a product and caught the request.
3. Changed the stock-check URL parameter to `http://localhost/admin`.
4. Forwarded the request — the response revealed the admin panel's contents, confirming the server fetched an internal-only page on my behalf.
5. Explored the admin panel's structure in the response to find the delete-user action and its parameters.
6. Changed the URL parameter again to point at the delete endpoint with `carlos` as the target.
7. Forwarded the request — `carlos` was deleted.

## 🧾 Payload used
```
stockApi=http://localhost/admin/delete?username=carlos
```