---
type: lab
platform: PortSwigger
topic: SSRF
difficulty: Apprentice
status: solved
date: 2026-10-28
tags: [portswigger, ssrf]
---

# Basic SSRF against another back-end system

## 🎯 Goal
Scan the internal `192.168.0.X` range for an admin interface on port `8080` using the stock check feature, then use it to delete the user `carlos`.

## 🧠 Core idea
The stock check feature makes a server-side request to whatever URL I give it, with no restriction on target host. Because the request originates from the backend server, it can reach internal systems on the `192.168.0.X` network that aren't exposed to the outside world — including an admin interface on port 8080 that I could never reach directly. This turns the feature into a tool for both scanning the internal network and sending authenticated-looking requests into it.

## 🔍 Recon
- Opened a product page and clicked "Check stock."
- Intercepted the request and found a `stockApi` parameter containing a full internal URL (e.g. `http://192.168.0.1:8080/...`) — confirmation the server fetches whatever URL is supplied here.

## 💥 Exploitation (step by step)
1. Sent the stock check request to Intruder.
2. Changed `stockApi` to `http://192.168.0.1:8080/admin`, then marked just the final octet of the IP for fuzzing: `192.168.0.§1§`.
3. Set payload type to Numbers, range 1 to 255, step 1.
4. Started the attack.
5. Sorted results by Status column — one entry returned `200` (all dead IPs returned errors/timeouts), revealing the live admin interface.
6. Sent that successful request to Repeater.
7. Changed the path in `stockApi` from `/admin` to `/admin/delete?username=carlos`.
8. Sent the request — response was `302`, confirming the delete succeeded.

## 🧾 Payload used
```
stockApi=http://192.168.0.<found-ip>:8080/admin/delete?username=carlos
```