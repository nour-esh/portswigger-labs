---
type: lab
platform: PortSwigger
topic: SQL Injection
difficulty: Apprentice
status: solved
date: 2026-10-28
tags: [portswigger, sql-injection]
---

# SQL injection vulnerability allowing login bypass

## 🎯 Goal
Perform a SQL injection attack that logs in to the application as the `administrator` user.

## 🧠 Core idea
The login query likely checks both username and password in a single WHERE clause, something like `WHERE username = 'X' AND password = 'Y'`. By injecting a `'` to break out of the username string and commenting out the rest of the query with `--`, the password check never gets evaluated at all — the query just needs a matching username to succeed.

## 🔍 Recon
- Opened the login page and submitted a normal login attempt to capture the request structure.
- Noted the request has separate `username` and `password` parameters, typical of a WHERE clause combining both checks.

## 💥 Exploitation (step by step)
1. Intercepted the login request in Burp.
2. Changed the `username` parameter to `administrator'--`.
3. Left the password field as anything (its value no longer matters since it's commented out).
4. Forwarded the request.
5. Logged in successfully as `administrator` — the password check was bypassed entirely.

## 🧾 Payload used
```
username=administrator'--
```