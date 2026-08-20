
## 2026-08-19 09:45:38 UTC account.box.com
https://account.box.com/` -> 206

## 2026-08-20 13:36:52 UTC account.box.com
https://cloud.account.box.com/login?redirect_uri=https://evil.example.com/callback -> 206
https://cloud.account.box.com/login -> 206
https://cloud.account.box.com/login?next=https://evil.example.com -> 206
https://cloud.account.box.com/login?redirect=https://evil.example.com/steal -> 206
https://cloud.account.box.com/login?return_to=https://evil.example.com -> 206
