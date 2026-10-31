---
type: lab
platform: PortSwigger
topic: Access Control
difficulty: Apprentice
status: solved
date: 2026-10-26
tags: [portswigger, access-control]
---

# User ID controlled by request parameter, with unpredictable user IDs

## 🎯 Goal
Find `carlos`'s GUID and use it to access his account page, then retrieve and submit his API key.

## 🧠 Core idea
Using GUIDs instead of sequential numbers makes the ID unguessable, but the account page still trusts whatever `id` is passed in the request without checking it belongs to the logged-in user. If the GUID leaks anywhere public, the "unpredictability" protection is gone — it's horizontal privilege escalation either way, just with an extra step to find the ID first.

## 🔍 Recon
- Logged in with `wiener:peter`.
- Browsed the blog and found a post by `carlos`.
- Clicked his name/profile and noticed his GUID exposed in the URL.

## 💥 Exploitation (step by step)
1. Noted `carlos`'s GUID from the blog post URL.
2. Went to my own account page and found the `id` parameter in the request.
3. Replaced my own ID with `carlos`'s GUID.
4. Loaded the page — got his account data, including his API key.
5. Submitted the API key as the solution.

## 🧾 Payload used
```
GET /my-account?id=<carlos-guid>
```