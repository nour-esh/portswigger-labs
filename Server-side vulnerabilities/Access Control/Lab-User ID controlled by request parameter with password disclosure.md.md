---
type: lab
platform: PortSwigger
topic: Access Control
difficulty: Apprentice
status: solved
date: 2026-10-26
tags: [portswigger, access-control]
---

# User ID controlled by request parameter with password disclosure

## 🎯 Goal
Retrieve the administrator's password via the account page, then use it to log in and delete `carlos`.

## 🧠 Core idea
The account page prefills a masked password input with the real password value in the HTML/response — masking is only a UI concern, the actual password is still sent to the client. Combined with the same `id` parameter trust issue as before, switching `id` to `administrator` leaks the admin's real password straight into the response.

## 🔍 Recon
- Logged in with `wiener:peter`, opened the account page.
- Noticed the password field is masked in the UI but likely present in the raw response — a good sign the value is sent, not withheld.

## 💥 Exploitation (step by step)
1. Changed the `id` parameter in the URL to `administrator`.
2. Sent the request through Burp and inspected the raw response body.
3. Found the administrator's actual password in the response (not just asterisks).
4. Logged out, logged back in as `administrator` using that password.
5. Deleted `carlos`.

## 🧾 Payload used
```
GET /my-account?id=administrator
```