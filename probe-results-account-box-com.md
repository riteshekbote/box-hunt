
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

## 2026-08-21 19:01:02 UTC account.box.com
https://account.box.com/login?redirect_url=%2Fsso%3Fredirect_url%3Dhttps%3A%2F%2Fevil.example.com` -> 206
https://account.box.com/sso?redirect_url=https://evil.example.com/path -> 206
https://account.box.com/sso?redirect_url=//evil.example.com -> 206
https://account.box.com/sso?redirect_url=%2F%2Fevil.example.com -> 206
https://account.box.com/login -> 206
https://account.box.com/login/reset?email=test -> 206
https://account.box.com/api/oauth2/token -> HTTP 400
https://account.box.com/login?error=test123&error_description=bhx-desc-test -> 206
https://account.box.com/login?redirect_to=https://evil.example.com -> 206
https://account.box.com/login?language=test -> 206
https://account.box.com/sso/bhx-test123 -> 200
https://account.box.com/api/oauth2/authorize?client_id=test&response_type=code&redirect_uri=https://evil.example.com -> 206

## 2026-08-21 19:23:03 UTC account.box.com
https://account.box.com/api/oauth2/authorize` -> HTTP 404
https://account.box.com/login?redirect_url=%2Fsso%3Fredirect_url%3Dhttps%3A%2F%2Fevil.example.com` -> 206
https://account.box.com/sso?redirect_url=javascript:alert(1 -> 206
https://account.box.com/sso?redirect_url=data:text/html,<script>alert(1 -> 206
https://account.box.com/sso?redirect_url=https://account.box.com.evil.example.com -> 206
https://account.box.com/debug -> 206
https://account.box.com/status -> 206
https://account.box.com/health -> 206
https://account.box.com/metrics -> 206
https://account.box.com/login -> 206
https://account.box.com/sso?redirect_url=https://evil.example.com/path -> 206
https://account.box.com/login?redirect_url=%2Fsso%3Fredirect_url%3Dhttps%3A%2F%2Fevil.example.com%2Fpath -> 206

## 2026-08-21 19:49:37 UTC account.box.com
https://account.box.com/sso?redirect_url=javascript:alert(1 -> 206
https://account.box.com/sso?redirect_url=data:text/html,%3Cscript%3Ealert(1 -> 206
https://account.box.com/sso?redirect_url=https://account.box.com.evil.example.com -> 206
https://account.box.com/debug -> 206
https://account.box.com/status -> 206
https://account.box.com/health -> 206
https://account.box.com/metrics -> 206
https://account.box.com/login?redirect_url=%2Fsso%3Fredirect_url%3Djavascript%3Aalert%281%29 -> 206
https://account.box.com/login?redirect_url=%2Fsso%3Fredirect_url%3Ddata%3Atext%2Fhtml%2C%253Cscript%253Ealert%281%29%253C%2Fscript%253E -> 206
https://account.box.com/login?redirect_url=%2Fsso%3Fredirect_url%3Dhttps%3A%2F%2Faccount.box.com.evil.example.com -> 206
https://account.box.com/login?redirect_url=%2Fdebug -> 206
https://account.box.com/login?redirect_url=%2Fstatus -> 206

## 2026-08-21 19:58:27 UTC account.box.com
https://account.box.com/login?redirect_url=%2Fsso%3Fredirect_url%3Djavascript%3Aalert(1 -> 206
https://account.box.com/sso?redirect_url=https://httpbin.org/redirect-to?url=https://example.com -> 206
https://account.box.com.evil.example.com` -> ERR <urlopen error [Errno -2] Name or service not known>
https://account.box.com/sso?redirect_url=https://evil.example.com/path -> 206
https://account.box.com/sso?redirect_url=javascript:alert(1 -> 206
https://account.box.com/login?redirect_url=%2Fsso%3Fredirect_url%3Djavascript%3Aalert%281%29 -> 206
https://account.box.com/sso?redirect_url=data:text/html,<script>alert(1 -> 206
https://account.box.com/sso?redirect_url=https://account.box.com.evil.example.com -> 206
https://account.box.com/login?redirect_url=%2Fsso%3Fredirect_url%3Dhttps%3A%2F%2Faccount.box.com.evil.example.com -> 206
https://account.box.com/debug -> 206
https://account.box.com/login?redirect_url=%2Fdebug -> 206
https://account.box.com/status -> 206

## 2026-08-21 21:08:33 UTC account.box.com
https://cloud.account.box.com/login -> 206
https://account.box.com/login -> 206
https://account.box.com/sso?redirect_url=https://evil.example.com/callback -> 206
https://account.box.com/login?redirect_url=%2Fsso%3Fredirect_url%3Dhttps%3A%2F%2Fevil.example.com%2Fcallback -> 206
https://cloud.account.box.com/api/2.0/users/me -> HTTP 401
https://cloud.account.box.com/api/2.0/folders/0 -> HTTP 401
https://cloud.account.box.com/sso -> 206

## 2026-08-21 21:29:42 UTC account.box.com
https://cloud.account.box.com/integrations/googleplus/beginAuthorization?redirect_url=https://evil.example.com/path -> 200
https://account.box.com/login?redirect_url=https://evil.example.com/path -> 206
https://account.box.com/sso?redirect_url=https://evil.example.com/path -> 206
https://cloud.account.box.com/.well-known/openid-configuration -> HTTP 404
https://cloud.account.box.com/login -> 206
https://account.box.com/login?redirect_url=/ -> 206
https://accounts.google.com/o/oauth2/v2/auth?response_type=code&access_type=offline&client_id=371608620635-lsbr3prap4hae8kl0netf6r54831t8b4.apps.googleusercontent.com&redirect_uri=https%3A%2F%2Faccount.box.com%2Fintegrations%2Fgoogleplus%2Foauth2Redirect&state=%257B%2522c%2522%253A%25223620c59bdbd8d -> 200

## 2026-08-21 23:14:02 UTC account.box.com
https://account.box.com/api/oauth2/authorize?response_type=code&client_id=test&redirect_uri=https://evil.com -> 206
https://account.box.com/api/oauth2/revoke -> 206
https://account.box.com/api/v2.0/users/me -> HTTP 404
https://account.box.com/login -> 206
https://account.box.com/api/oauth2/token -> HTTP 400
https://account.box.com/api/oauth2/authorize -> 206

## 2026-08-21 23:36:11 UTC account.box.com
https://account.box.com/api/oauth2/authorize?response_type=code&client_id=test&redirect_uri=https://evil.com -> 206
https://account.box.com/api/oauth2/authorize -> 206
