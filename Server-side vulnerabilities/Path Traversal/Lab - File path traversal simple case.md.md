---
type: lab
platform: PortSwigger
topic: Path Traversal
difficulty: Apprentice
status: solved
date: 2026-10-26
tags: [portswigger, path-traversal]
---

# File path traversal, simple case

## 🎯 Goal
Retrieve the contents of `/etc/passwd` by exploiting a path traversal flaw in how product images are displayed.

## 🧠 Core idea
The app takes a `filename` parameter and uses it directly to build a file path on the server, without checking it. If the input isn't sanitized, I can use `../` to climb out of the images folder and read any file the server process can access, like `/etc/passwd`.

## 🔍 Recon
- Opened a product page; noticed the image is loaded via a request with a `filename` parameter (something like `filename=product-name.jpg`).
- This parameter is reflected directly into a file path on the server side, which is the entry point to test.

## 💥 Exploitation (step by step)
1. Turned on Burp Suite and set the browser to intercept through it.
2. Opened a product page and let the image request hit the Proxy tab.
3. Sent the request to Repeater.
4. Changed the `filename` parameter value to `../../../etc/passwd`.
5. Sent the request and checked the response.

## 🧾 Payload used
```
filename=../../../etc/passwd
```