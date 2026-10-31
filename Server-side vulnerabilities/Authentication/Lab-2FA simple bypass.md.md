---
type: lab
platform: PortSwigger
topic: Authentication
difficulty: Apprentice
status: solved
date: 2026-10-27
tags: [portswigger, authentication, 2fa]
---

# 2FA simple bypass

## 🎯 Goal
Access Carlos's account page without having access to his 2FA verification code.

## 🧠 Core idea
The app checks the 2FA code on the verification page, but it doesn't actually enforce that the user must pass that check before reaching `/my-account`. The session is already authenticated after step one (username+password), and 2FA is just a UI gate on top — not a real server-side block. Navigating straight to the protected page skips the check entirely.

## 🔍 Recon
- Logged in with my own credentials (`wiener:peter`) first to see the normal flow: after password login, I was sent to a 2FA verification page, and the code arrived by email.
- Noted the account page URL (`/my-account`) while logged in normally, to know what to navigate to later.

## 💥 Exploitation (step by step)
1. Logged out of my own account.
2. Logged in with the victim's credentials (`carlos:montoya`) — correct password, but 2FA verification page appeared next (no code available for this account).
3. Instead of entering a code, manually changed the URL in the address bar to `/my-account`.
4. Page loaded directly — session was already authenticated enough to grant access, bypassing the 2FA step entirely.

## 🧾 Payload used
```
GET /my-account
```