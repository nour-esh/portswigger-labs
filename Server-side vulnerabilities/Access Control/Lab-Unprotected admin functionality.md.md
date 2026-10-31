---
type: lab
platform: PortSwigger
topic: Access Control
difficulty: Apprentice
status: solved
date: 2026-10-26
tags: [portswigger, access-control]
---

# Unprotected admin functionality

## 🎯 Goal
Delete the user `carlos` by finding and accessing an unprotected admin panel.

## 🧠 Core idea
The admin panel isn't linked anywhere in the app, but "hidden" isn't the same as "protected" — there's no access control check on it, so anyone who finds the URL can use it directly. Security through obscurity fails as soon as the path leaks somewhere, like `robots.txt`.

## 🔍 Recon
- Checked `robots.txt` by appending `/robots.txt` to the lab URL.
- Found a `Disallow` line revealing the path to the admin panel (`/administrator-panel`) — it's listed there so search engines don't index it, but that doesn't stop a person from visiting it directly.

## 💥 Exploitation (step by step)
1. Replaced `/robots.txt` in the URL bar with `/administrator-panel`.
2. Loaded the panel directly — no login or role check required.
3. Found the option to delete `carlos` and used it.

## 🧾 Payload used
```
GET /administrator-panel
```