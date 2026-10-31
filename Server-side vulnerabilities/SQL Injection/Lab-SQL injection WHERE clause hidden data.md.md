---
type: lab
platform: PortSwigger
topic: SQL Injection
difficulty: Apprentice
status: solved
date: 2026-10-28
tags: [portswigger, sql-injection]
---

# SQL injection vulnerability in WHERE clause allowing retrieval of hidden data

## 🎯 Goal
Perform a SQL injection attack that causes the application to display one or more unreleased products.

## 🧠 Core idea
The `category` parameter is inserted directly into the SQL query's WHERE clause with no sanitization. The query also filters on `released = 1`, which is what hides unreleased products from normal users. By injecting `OR 1=1` and commenting out the rest of the query, the condition becomes always-true, so the `released = 1` check never actually applies — returning every product, released or not.

## 🔍 Recon
- Selected a product category and noticed the URL/request includes a `category` parameter.
- The lab description reveals the backend query structure (`WHERE category = 'X' AND released = 1`), which tells me exactly where the injection point is and what condition I need to defeat.

## 💥 Exploitation (step by step)
1. Intercepted the category filter request in Burp.
2. Changed the `category` parameter value to `' OR 1=1--`.
3. Forwarded the request.
4. Response now included unreleased products — the `AND released = 1` condition was commented out by `--`, so the query became unconditionally true.

## 🧾 Payload used
```
category=' OR 1=1--
```