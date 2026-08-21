
## 2026-08-19 09:45:38 UTC account.box.com
https://account.box.com/` -> 206

## 2026-08-20 13:36:52 UTC account.box.com
https://cloud.account.box.com/login?redirect_uri=https://evil.example.com/callback -> 206
https://cloud.account.box.com/login -> 206
https://cloud.account.box.com/login?next=https://evil.example.com -> 206
https://cloud.account.box.com/login?redirect=https://evil.example.com/steal -> 206
https://cloud.account.box.com/login?return_to=https://evil.example.com -> 206

## 2026-08-21 18:52:24 UTC account.box.com
https://account.box.com/api/oauth2/token -> HTTP 400
https://cloud.account.box.com/login -> 206
https://cloud.account.box.com/login?redirect_url=%2Fcanvas -> 206
https://account.box.com/login -> 206
https://account.box.com/api/2.0/users/me -> HTTP 401
https://account.box.com$path -> ERR <urlopen error [Errno -2] Name or service not known>
https://account.box.com/api/oauth2/authorize -> 206
https://account.box.com/api/oauth2/authorize?client_id=test&response_type=code -> 206
