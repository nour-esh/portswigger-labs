---
type: lab
platform: PortSwigger
topic: Access Control
difficulty: Apprentice
status: solved
date: 2026-10-26
tags: [portswigger, access-control]
---

# Unprotected admin functionality with unpredictable URL

## 🎯 Goal
Find the admin panel at its unpredictable URL and use it to delete the user `carlos`.

## 🧠 Core idea
Making the admin URL hard to guess doesn't fix the lack of access control — it just shifts the problem to "don't leak the URL anywhere." If the app itself discloses the path (in page source, JS, comments, etc.), the obscurity is worthless since there's still no actual check on who can load that page.

## 🔍 Recon
- Viewed the lab home page's source using browser dev tools / Burp.
- Found a piece of JavaScript in the page that reveals the admin panel's URL.

## 💥 Exploitation (step by step)
1. Opened the page source and located the JS disclosing the admin panel path.
2. Navigated to that URL directly.
3. Loaded the panel with no access control check in the way.
4. Deleted `carlos`.

## 🧾 Payload used
```
GET /<disclosed-admin-path>
```