---
type: lab
platform: PortSwigger
topic: Access Control
difficulty: Apprentice
status: solved
date: 2026-10-26
tags: [portswigger, access-control]
---

# User role controlled by request parameter

## 🎯 Goal
Access `/admin` and delete the user `carlos`, even though the panel checks role via a cookie, not a real session check.

## 🧠 Core idea
The app decides whether I'm an admin by trusting a cookie value sent from the client, instead of checking something the server actually controls (like a session tied to a verified role in the database). Since the client can set any cookie value it wants, the "access control" is really just asking the user to self-report their own permissions.

## 🔍 Recon
- Logged in with `wiener:peter`.
- Browsed to `/admin` directly — got denied.
- Turned on Burp Proxy interception, including response interception.
- Logged in again through the login page and forwarded the request in Burp to inspect the response.
- Spotted the response setting a cookie: `Admin=false`.

## 💥 Exploitation (step by step)
1. With response interception on, submitted the login form.
2. Intercepted the login response and found the `Admin=false` cookie being set.
3. Changed the cookie value to `Admin=true` and forwarded it.
4. Browsed to `/admin` — now granted access.
5. Deleted `carlos`.

## 🧾 Payload used
```
Cookie: Admin=true
```