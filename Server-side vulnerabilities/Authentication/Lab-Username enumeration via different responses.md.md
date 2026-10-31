---
type: lab
platform: PortSwigger
topic: Authentication
difficulty: Apprentice
status: solved
date: 2026-10-27
tags: [portswigger, authentication, brute-force]
---

# Username enumeration via different responses

## 🎯 Goal
Enumerate a valid username, brute-force that user's password, then access their account page.

## 🧠 Core idea
The login endpoint has two separate flaws that combine into one attack. First, the error messages leak whether a username exists: an invalid username returns one generic message, but a valid username with a wrong password returns a different message ("Incorrect password" vs "Invalid username"), so the response itself discloses which usernames are real. Second, there's no account lockout or rate limiting on login attempts, so once a valid username is known, the password can simply be brute-forced with no restriction.

## 🔍 Recon
- Opened the login page and submitted a random username/password to see the baseline failure response.
- Noticed the response for an unknown username differs in length/text from the response for a known username with a wrong password — this is the information leak the lab is built around.

## 💥 Exploitation (step by step)
1. Opened Burp Suite, turned on interception, and submitted a login attempt.
2. Sent the captured request to Intruder.
3. Marked the `username` parameter, loaded a username wordlist, ran a Sniper attack.
4. Sorted results by response length — one entry stood out with a different length, and its response said "Incorrect password" instead of "Invalid username" — confirming that username is valid.
5. Sent a fresh login request to Intruder again, this time marking the `password` parameter instead, with the confirmed username fixed in the request.
6. Loaded a password wordlist and ran a Sniper attack.
7. Sorted by status code — one request returned `302 Found` instead of the usual failure status, confirming the correct password.
8. Logged in with that username/password combo and accessed the account page.

## 🧾 Payload used
```
Attack 1 (username enumeration):
username=§FUZZ§&password=invalid

Attack 2 (password brute-force, username fixed):
username=<confirmed-username>&password=§FUZZ§
```