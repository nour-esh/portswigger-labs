---
type: lab
platform: PortSwigger
topic: OS Command Injection
difficulty: Apprentice
status: solved
date: 2026-10-28
tags: [portswigger, command-injection]
---

# OS command injection, simple case

## 🎯 Goal
Execute the `whoami` command to determine the name of the current user.

## 🧠 Core idea
The stock checker builds a shell command on the server using the `storeID` parameter directly, without sanitizing it, then returns the raw output of that command in the response. Since shell syntax allows chaining commands (`|`, `;`, `&&`), injecting a pipe lets me append my own command to whatever the app was already going to run — and I get to see the output because the app echoes the command's result back.

## 🔍 Recon
- Opened a product page and checked stock, intercepted the request in Burp.
- Noticed `storeID` is a plain parameter feeding into what's presumably a backend shell command (per the lab description) — a prime injection point since shell metacharacters often aren't filtered.

## 💥 Exploitation (step by step)
1. Intercepted the stock check request in Burp.
2. Changed the `storeID` parameter value to `1|whoami`.
3. Forwarded the request.
4. Response contained the stock check output plus the result of `whoami` appended — the current user's name.

## 🧾 Payload used
```
storeId=1|whoami
```