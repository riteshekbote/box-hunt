## 2026-08-08 23:00:16 UTC dl.boxcloud.com (ling3)
## 2026-08-09 08:49:33 UTC dl.boxcloud.com (ling3)
## 2026-08-09 09:24:24 UTC dl.boxcloud.com (ling3)
## 2026-08-09 17:55:41 UTC dl.boxcloud.com (ling3)
## 2026-08-09 18:25:58 UTC dl.boxcloud.com (ling3)
## 2026-08-10 20:23:21 UTC dl.boxcloud.com (ling3)
## 2026-08-10 21:07:14 UTC dl.boxcloud.com (ling3)
## 2026-08-10 23:40:37 UTC dl.boxcloud.com (ling3)
## 2026-08-11 02:21:48 UTC dl.boxcloud.com (ling3)
## 2026-08-11 16:40:46 UTC dl.boxcloud.com (ling3)
## 2026-08-11 17:24:51 UTC dl.boxcloud.com (ling3)
## 2026-08-11 17:31:43 UTC dl.boxcloud.com (ling3)
## 2026-08-11 17:38:06 UTC dl.boxcloud.com (ling3)
## 2026-08-11 18:30:24 UTC dl.boxcloud.com (ling3)
## 2026-08-11 19:35:41 UTC dl.boxcloud.com (ling3)
## 2026-08-11 20:20:39 UTC dl.boxcloud.com (ling3)
## 2026-08-11 21:08:06 UTC dl.boxcloud.com (ling3)
## 2026-08-11 22:00:00 UTC dl.boxcloud.com (ling3)
## 2026-08-11 22:45:07 UTC dl.boxcloud.com (ling3)
## 2026-08-11 23:24:01 UTC dl.boxcloud.com (ling3)
## 2026-08-12 00:04:13 UTC dl.boxcloud.com (ling3)
## 2026-08-12 02:36:45 UTC dl.boxcloud.com (ling3)
## 2026-08-12 04:25:23 UTC dl.boxcloud.com (ling3)
## 2026-08-12 05:51:42 UTC dl.boxcloud.com (ling3)
## 2026-08-12 06:59:49 UTC dl.boxcloud.com (ling3)
## 2026-08-12 08:19:26 UTC dl.boxcloud.com (ling3)
## 2026-08-12 09:34:46 UTC dl.boxcloud.com (bigpickle)
[LEARN] ACCEPTED MISCONFIG @ cloud.app.box.com/: Root GET / has returned 206 (partial content) across 12+ cycles while several return 200 — the origin/edge honors Range and serves cacheable partial responses, so header-capture via ranged/preflight requests is a viable vector; plain root GET is now exhausted.
[NEW] cloud.app.box.com/: repeated 206 on root confirms Range-handling edge layer; CORS preflight behavior still unobserved — moving root probe (exhausted) to method/header-level probes.
[HYP] CORS misconfiguration on cloud.app.box.com preview origin (preflight reflects attacker Origin / exposes Authorization)
class: MISCONFIG
asset: cloud.app.box.com/
confidence: 45
reasoning: Root repeatedly returns 206, proving an edge layer processes Range on arbitrary GET and returns cacheable partial responses with headers; preview origins commonly pair a reflected or wide Allow-Origin with a permissive Allow-Headers list. No preflight result has been observed yet, so reflection is unproven but directly testable.
verify_steps:
impact: If ACAO reflects attacker origin with credentials (or ACAH exposes Authorization), an attacker page can read the victim's authenticated preview responses/tokens from the shared origin -> cross-origin token/session leakage, high.
testability: PASSIVE
[NEXT] PROBE: OPTIONS https://cloud.app.box.com/ with Origin: https://evil.example, Access-Control-Request-Method: GET, Access-Control-Request-Headers: authorization,range; UA box-research/1.0; record status line + Access-Control-* and Vary headers. (plain root GET is exhausted; this is a distinct, read-only preflight)
## 2026-08-12 10:39:00 UTC dl.boxcloud.com (ling3)
## 2026-08-12 11:27:24 UTC dl.boxcloud.com (ling3)
## 2026-08-12 12:07:35 UTC dl.boxcloud.com (ling3)
## 2026-08-12 13:39:24 UTC dl.boxcloud.com (ling3)
## 2026-08-12 14:41:46 UTC dl.boxcloud.com (ling3)
## 2026-08-12 15:37:20 UTC dl.boxcloud.com (ling3)
## 2026-08-12 16:31:25 UTC dl.boxcloud.com (ling3)
## 2026-08-12 17:31:41 UTC dl.boxcloud.com (ling3)
## 2026-08-12 18:28:16 UTC dl.boxcloud.com (ling3)
## 2026-08-12 19:36:38 UTC dl.boxcloud.com (ling3)
## 2026-08-12 20:19:33 UTC dl.boxcloud.com (ling3)
## 2026-08-12 21:07:00 UTC dl.boxcloud.com (ling3)
## 2026-08-12 21:57:35 UTC dl.boxcloud.com (ling3)
## 2026-08-12 22:36:17 UTC dl.boxcloud.com (ling3)
## 2026-08-12 23:19:45 UTC dl.boxcloud.com (ling3)
## 2026-08-13 00:01:47 UTC dl.boxcloud.com (ling3)
## 2026-08-13 02:33:34 UTC dl.boxcloud.com (ling3)
## 2026-08-13 04:27:37 UTC dl.boxcloud.com (ling3)
## 2026-08-13 05:54:33 UTC dl.boxcloud.com (ling3)
## 2026-08-13 07:08:10 UTC dl.boxcloud.com (ling3)
## 2026-08-13 08:40:53 UTC dl.boxcloud.com (ling3)
## 2026-08-13 09:45:45 UTC dl.boxcloud.com (ling3)
## 2026-08-13 10:45:43 UTC dl.boxcloud.com (bigpickle)
[LEARN] ACCEPTED MISCONFIG @ cloud.app.box.com/: robots.txt and favicon.ico return 206 consistently while root alternates 200/206 — the edge layer serves Range-partial cache entries for static paths but the root origin flips between full and partial bodies (heterogeneous backend / cache coalescing). This is confirmed by three consecutive executor cycles, not a fluke.
[LEARN] REJECTED MISCONFIG @ cloud.app.box.com/: Prior cycle's OPTIONS preflight on / (Origin: evil.example) produced NO observable result in probe-results-next — executor only echoed the URL-list robot batch. No preflight evidence exists yet, so no confidence raise on the CORS hypothesis is justified.
[HYP] CORS misconfiguration on cloud.app.box.com preview origin — preflight/GET with attacker Origin reflects Access-Control-* or exposes Authorization
class: MISCONFIG
asset: cloud.app.box.com/favicon.ico (and /robots.txt, both confirmed 206 Range-servable)
confidence: 45
reasoning: Static assets (favicon.ico, robots.txt) are served 206 with Range handling, proving an edge/object layer controls response headers on arbitrary GET; preview origins commonly mount a reflected or wide ACAO on that layer. Root preflight returned no data, but a 206 asset is a distinct, sharper target for both preflight and Origin-header reflection tests.
verify_steps: OPTIONS https://cloud.app.box.com/favicon.ico with Origin: https://evil.example, Access-Control-Request-Method: GET, Access-Control-Request-Headers: authorization; and GET https://cloud.app.box.com/favicon.ico with Origin: https://evil.example and Range: bytes=0-99; capture status line + all Access-Control-* + Vary headers; UA box-research/1.0.
impact: If ACAO reflects attacker origin (esp. with credentials) or ACAH exposes Authorization, an attacker page can read the victim's authenticated preview responses/tokens from the shared origin — cross-origin token/session leakage, high severity.
testability: PASSIVE
[NEXT] PROBE: OPTIONS https://cloud.app.box.com/favicon.ico with headers Origin: https://evil.example, Access-Control-Request-Method: GET, Access-Control-Request-Headers: authorization,range,x-requested-with; UA box-research/1.0; record status line, Access-Control-Allow-Origin, Access-Control-Allow-Headers, Vary (distinct asset vs prior root preflight, which returned no result).
[NEXT] PROBE: GET https://cloud.app.box.com/favicon.ico with headers Origin: https://evil.example and Range: bytes=0-99; UA box-research/1.0; record status line (expect 206), Access-Control-Allow-Origin, Access-Control-Expose-Headers, Vary — tests ACAO reflection on a confirmed 206 Range-served asset.
[PARKED] Dual-backend routing flip on cloud.app.box.com root (200 vs 206 alternation): confirms heterogeneity but has no attacker outcome without a header/body differential; parked until a CORS reflection or header leak is proven on a 206 asset.
## 2026-08-13 11:30:27 UTC dl.boxcloud.com (ling3)
## 2026-08-13 12:11:22 UTC dl.boxcloud.com (ling3)
## 2026-08-13 13:44:35 UTC dl.boxcloud.com (ling3)
## 2026-08-13 14:44:43 UTC dl.boxcloud.com (ling3)
## 2026-08-13 15:39:23 UTC dl.boxcloud.com (ling3)
## 2026-08-13 16:31:38 UTC dl.boxcloud.com (ling3)
## 2026-08-13 17:31:19 UTC dl.boxcloud.com (ling3)
## 2026-08-13 18:28:59 UTC dl.boxcloud.com (ling3)
## 2026-08-13 19:34:38 UTC dl.boxcloud.com (ling3)
## 2026-08-13 20:07:43 UTC dl.boxcloud.com (ling3)
## 2026-08-13 20:58:34 UTC dl.boxcloud.com (ling3)
## 2026-08-13 21:42:11 UTC dl.boxcloud.com (ling3)
## 2026-08-13 22:24:15 UTC dl.boxcloud.com (ling3)
## 2026-08-13 23:08:42 UTC dl.boxcloud.com (ling3)
## 2026-08-13 23:55:21 UTC dl.boxcloud.com (bigpickle)
[LEARN] REJECTED MISCONFIG @ cloud.app.box.com/: the OPTIONS/Origin preflight probes for `/` and `/favicon.ico` produced no observable result across two cycles — the executor channel reports URL + status only and never surfaces Access-Control-* / Vary / request-line details, so CORS reflection can neither be confirmed nor refuted here. Confidence stays 45; parked.
[NEW] cloud.app.box.com/: single-decode normalization layer confirmed — one level of percent-decoding runs before object-store lookup (`%2e%2e/`, `//`, `%72obots`, `%2Frobots`, `%2Etxt` all → 206) while double-encoded (`%252F`, `%252E`) and trailing-slash-file forms → 404; the boundary of that layer is the new probe target.
[HYP] Object-store default handler vs whitelist-routed: a random unguessable path decides whether the 206/Range layer fronts the whole origin or only pinned static assets
class: MISCONFIG
asset: cloud.app.box.com/<nonce>
confidence: 50
reasoning: Every robots.txt/favicon variant normalizes to 206 even with traversal and leading-slash tricks, while only double-encoded and slash-suffixed forms 404 — consistent with a normalizing object-store front. No cycle has ever probed an arbitrary missing object, so it is unknown whether "404-for-missing" exists or whether 404 is exclusively a normalization failure.
verify_steps: GET https://cloud.app.box.com/7xq9ZzK2nX4w with UA box-research/1.0; record status line and Content-Type only.
impact: Decides whether the Range/206 layer (and any Access-Control-* policy living on it) applies origin-wide or only to pinned objects — if default-handler, the parked CORS exposure surface reopens for the full host; if whitelisted, CORS scope is confined to static assets.
testability: PASSIVE
[NEXT] PROBE: GET https://cloud.app.box.com/7xq9ZzK2nX4w (12-char random nonce path) with UA box-research/1.0; record status line — distinguishes object-store-default (206) from app/whitelist routing (404). Distinct from all prior robot-batch and header-based probes.
[PARKED] CORS misconfiguration on cloud.app.box.com (attacker-Origin preflight/GET): preflight and Origin-reflection probes on `/` and `/favicon.ico` returned no observable data two cycles running — the executor channel emits status+length only and never exposes Access-Control-* / Vary headers, so reflection is unverifiable in this pipeline; requires header-level evidence (HUMAN_ONLY).
## 2026-08-14 00:54:49 UTC dl.boxcloud.com (ling3)
## 2026-08-14 03:19:02 UTC dl.boxcloud.com (ling3)
## 2026-08-14 05:09:37 UTC dl.boxcloud.com (ling3)
## 2026-08-14 06:39:47 UTC dl.boxcloud.com (ling3)
## 2026-08-14 08:00:29 UTC dl.boxcloud.com (ling3)
## 2026-08-14 09:11:02 UTC dl.boxcloud.com (ling3)
## 2026-08-14 10:15:33 UTC dl.boxcloud.com (ling3)
## 2026-08-14 11:09:11 UTC dl.boxcloud.com (ling3)
## 2026-08-14 11:54:43 UTC dl.boxcloud.com (bigpickle)
[LEARN] ACCEPTED MISCONFIG @ dl.boxcloud.com/: edge-routing fingerprint confirmed — origin root and junk paths → 404 (no origin-level CORS/static layer), `/api/2.0/files/1/content` → 401 (Bearer download edge is the only mounted handler), `/folders/shared/static/static/1-content` → 404. Matches the file-CDN role; prior root preflight (OPTIONS / with attacker Origin → 404, no Access-Control-*) proved the root has no CORS layer, but the content handler is a distinct, never-preflighted code path.
[LEARN] ACCEPTED AUTH @ dl.boxcloud.com/api/2.0/files/1/content: file content is gated by 401 Bearer before any Range/version/signed-URL logic runs; all real download behavior is AUTH_HELPED and not reachable with read-only anonymous requests.
[HYP] Download-edge CORS on dl.boxcloud.com API content handler
class: MISCONFIG
asset: dl.boxcloud.com/api/2.0/files/1/content
confidence: 20
reasoning: Only the root (→404) was ever preflighted on dl.boxcloud.com; the 401 content handler is a distinct code path and the only high-value one on the CDN — if it mounts a permissive or reflected Access-Control-* policy, an attacker page could read a victim's authenticated file downloads/tokens from the CDN origin. No OPTIONS has ever reached this handler.
verify_steps: OPTIONS https://dl.boxcloud.com/api/2.0/files/1/content with Origin: https://evil.example, Access-Control-Request-Method: GET, Access-Control-Request-Headers: authorization,range; then GET the same path with Origin: https://evil.example and Range: bytes=0-99; capture status line, WWW-Authenticate realm, all Access-Control-* and Vary headers.
impact: Cross-origin read of download responses / token exposure on the file CDN if ACAO reflects attacker origin or ACAH allows Authorization — high severity.
testability: PASSIVE
[NEXT] PROBE: OPTIONS https://dl.boxcloud.com/api/2.0/files/1/content with headers Origin: https://evil.example, Access-Control-Request-Method: GET, Access-Control-Request-Headers: authorization,range; UA box-research/1.0; record status line + ALL Access-Control-* + Vary + WWW-Authenticate headers — first preflight to actually hit the download edge (prior root OPTIONS 404'd at a different handler; cloud.app.box.com CORS tests already closed).
[NEXT] PROBE: GET https://dl.boxcloud.com/api/2.0/files/1/content with headers Origin: https://evil.example and Range: bytes=0-99; UA box-research/1.0; record status line (expect 401), WWW-Authenticate realm, any Access-Control-* / Vary — characterizes the Bearer gate and checks for pre-auth Range/header leakage on the content handler.
[PARKED] Authenticated download behavior on dl.boxcloud.com (Range/206 streaming, signed-URL validation, file-version tokens): all sit behind the 401 Bearer gate; requires a token → AUTH_HELPED, not probeable in this read-only pipeline without a new rule.
## 2026-08-14 12:33:01 UTC dl.boxcloud.com (ling3)
## 2026-08-14 13:56:47 UTC dl.boxcloud.com (ling3)
## 2026-08-14 14:45:48 UTC dl.boxcloud.com (ling3)
## 2026-08-14 15:34:58 UTC dl.boxcloud.com (ling3)
## 2026-08-14 16:27:20 UTC dl.boxcloud.com (ling3)
## 2026-08-14 17:27:12 UTC dl.boxcloud.com (ling3)
## 2026-08-14 18:14:54 UTC dl.boxcloud.com (ling3)
## 2026-08-14 19:15:00 UTC dl.boxcloud.com (ling3)
## 2026-08-14 19:57:04 UTC dl.boxcloud.com (ling3)
## 2026-08-14 20:25:14 UTC dl.boxcloud.com (ling3)
## 2026-08-14 20:52:36 UTC dl.boxcloud.com (ling3)
## 2026-08-14 21:12:49 UTC dl.boxcloud.com (bigpickle)
[LEARN] ACCEPTED MISCONFIG @ cloud.app.box.com/: dual-backend routing flip now observed end-to-end — identical plain GET `https://cloud.app.box.com/` returned 200 at 19:53 and 20:40 but 206 at 21:00 in the executor log. The 206 is a Partial-Content/object-store layer that serves the origin root itself, not only pinned assets; the 200 is the app/SPA backend. The heterogeneous-origin hypothesis is confirmed by observation; what remains open is which backend wins for arbitrary paths.
[HYP] Object-store default handler vs whitelist-routed — root now proves the 206/Range layer can front the origin root, leaving only arbitrary-path routing unresolved
class: MISCONFIG
asset: cloud.app.box.com/<nonce>
confidence: 58
reasoning: Root flipped 200→206 on identical requests across cycles, proving the partial-content layer is not confined to pinned static files. robots.txt/favicon variants normalize to 206 while double-encoded and slash-suffixed forms 404, so the boundary is still untested on unguessable paths. The nonce probe from the prior cycle never appeared in executor output, so the default-handler question is unanswered.
verify_steps: GET https://cloud.app.box.com/<new-nonce> and GET https://cloud.app.box.com/ twice in sequence; UA box-research/1.0; record status lines only.
impact: If a random path yields 206, the Range/206 layer is origin-global — reopening the parked CORS/Access-Control surface across the whole host; if 404, it is keyed to a known set (root + pinned assets) and CORS scope stays bounded. Severity medium-high depending on which policy rides the 206 backend.
testability: PASSIVE
[NEXT] PROBE: GET https://cloud.app.box.com/t3Kv9Xq2Mp7Z (fresh 12-char nonce, distinct from prior 7xq9ZzK2nX4w) with UA box-research/1.0; record status line only — discriminates object-store-default (206) from app/whitelist routing (404); the prior nonce probe was not seen in executor output so this re-keys the question on a new value.
[NEXT] PROBE: GET https://cloud.app.box.com/ twice in immediate succession with UA box-research/1.0; record both status lines — tests whether the 200/206 split is deterministic per-request (e.g., consistent hashing on path/backend) or load-balanced round-robin; directly characterizes the flip seen at 21:00 vs 19:53/20:40.
## 2026-08-14 21:39:20 UTC dl.boxcloud.com (ling3)
## 2026-08-14 21:55:51 UTC dl.boxcloud.com (ling3)
## 2026-08-14 22:13:30 UTC dl.boxcloud.com (ling3)
## 2026-08-14 22:40:34 UTC dl.boxcloud.com (ling3)
## 2026-08-14 22:57:40 UTC dl.boxcloud.com (ling3)
## 2026-08-14 23:17:44 UTC dl.boxcloud.com (ling3)
## 2026-08-14 23:40:54 UTC dl.boxcloud.com (ling3)
## 2026-08-14 23:58:43 UTC dl.boxcloud.com (ling3)
## 2026-08-15 01:10:21 UTC dl.boxcloud.com (ling3)
## 2026-08-15 02:18:27 UTC dl.boxcloud.com (ling3)
## 2026-08-15 03:07:33 UTC dl.boxcloud.com (ling3)
## 2026-08-15 03:50:20 UTC dl.boxcloud.com (ling3)
## 2026-08-15 04:15:36 UTC dl.boxcloud.com (ling3)
## 2026-08-15 04:48:36 UTC dl.boxcloud.com (ling3)
## 2026-08-15 05:10:40 UTC dl.boxcloud.com (ling3)
## 2026-08-15 05:37:26 UTC dl.boxcloud.com (ling3)
## 2026-08-15 05:56:44 UTC dl.boxcloud.com (ling3)
## 2026-08-15 06:26:26 UTC dl.boxcloud.com (ling3)
## 2026-08-15 07:05:36 UTC dl.boxcloud.com (ling3)
## 2026-08-15 07:38:29 UTC dl.boxcloud.com (ling3)
## 2026-08-15 07:56:23 UTC dl.boxcloud.com (ling3)
## 2026-08-15 08:18:45 UTC dl.boxcloud.com (ling3)
## 2026-08-15 08:48:34 UTC dl.boxcloud.com (ling3)
## 2026-08-15 09:10:47 UTC dl.boxcloud.com (ling3)
## 2026-08-15 09:37:16 UTC dl.boxcloud.com (ling3)
## 2026-08-15 09:54:32 UTC dl.boxcloud.com (ling3)
## 2026-08-15 10:10:18 UTC dl.boxcloud.com (ling3)
## 2026-08-15 10:37:06 UTC dl.boxcloud.com (bigpickle)
[LEARN] REJECTED MISCONFIG @ dl.boxcloud.com/api/2.0/files/1/content: the identical preflight+GET pair has been re-emitted across 25+ consecutive cycles with zero executor output, while the executor demonstrably ran the cloud.app.box.com robots suite and upload.box.com probes this cycle — the download edge is unreachable through this toolchain, so the surface is EXHAUSTED (not refuted) here.
[PARKED] Download-edge CORS on dl.boxcloud.com/api/2.0/files/1/content: 25+ cycles of the same OPTIONS+GET emitted no results; the executor returns nothing for dl.boxcloud.com, so the Bearer-gate preflight/header-leak evidence cannot be collected in this pipeline — needs a direct-probe channel, parked.
[HYP] Object-store default handler vs whitelist-routed (re-keyed on fresh nonce)
class: MISCONFIG
asset: cloud.app.box.com/<fresh-nonce>
confidence: 55
reasoning: robots.txt/favicon single-encoded variants all normalize to 206 while double-encoded (%252F, %252E) and suffix-forms 404, proving the 206/Range layer has a normalizer with an exact-key whitelist; root flipped 200→206 across earlier cycles. The discriminator is an unguessable path: 206 ⇒ origin-global object-store default (reopens CORS surface host-wide), 404 ⇒ pinned-key whitelist only.
verify_steps: GET https://cloud.app.box.com/Pn9k4Wm2sX6v with UA box-research/1.0; record status line only; compare with the simultaneous root GET (200 expected).
impact: If 206, the Range/partial-content backend fronts arbitrary paths and any Access-Control policy riding it is origin-wide; medium-high depending on that policy. If 404, CORS scope stays bounded to pinned assets.
testability: PASSIVE
[NEXT] PROBE: GET https://cloud.app.box.com/Pn9k4Wm2sX6v (fresh 12-char nonce, distinct from prior 7xq9ZzK2nX4w and t3Kv9Xq2Mp7Z) then GET https://cloud.app.box.com/ once more, UA box-research/1.0; record both status lines — 206-on-nonce proves object-store-default origin-global; 404 proves pinned-whitelist; the paired root re-tests the 200/206 flip under identical conditions.
## 2026-08-15 10:53:50 UTC dl.boxcloud.com (ling3)
## 2026-08-15 11:07:31 UTC dl.boxcloud.com (ling3)
## 2026-08-15 11:30:47 UTC dl.boxcloud.com (ling3)
## 2026-08-15 11:47:12 UTC dl.boxcloud.com (ling3)
## 2026-08-15 11:59:09 UTC dl.boxcloud.com (ling3)
## 2026-08-15 12:37:21 UTC dl.boxcloud.com (ling3)
## 2026-08-15 13:14:22 UTC dl.boxcloud.com (ling3)
## 2026-08-15 13:44:08 UTC dl.boxcloud.com (ling3)
## 2026-08-15 13:59:08 UTC dl.boxcloud.com (ling3)
## 2026-08-15 14:22:09 UTC dl.boxcloud.com (ling3)
## 2026-08-15 14:43:07 UTC dl.boxcloud.com (ling3)
## 2026-08-15 14:59:06 UTC dl.boxcloud.com (ling3)
## 2026-08-15 15:21:10 UTC dl.boxcloud.com (bigpickle)
[LEARN] ACCEPTED MISCONFIG @ cloud.app.box.com: the robots suite proves a single-decode normalizer with exact-key lookup — every single-encoded/query/`..` variant of pinned keys → 206 while double-encoded (%252F, %252E) and slash-suffix forms → 404, and query strings are ignored (robots.txt?probe=9f3k7 → 206). The remaining discriminator (arbitrary path → 206 origin-global vs 404 whitelist-scoped) is still open because the bare-nonce probe was emitted twice and never appeared in executor output.
[HYP] Object-store default handler vs whitelist-routed (re-keyed on slash-suffix nonce)
class: MISCONFIG
asset: cloud.app.box.com/<fresh-nonce>/
confidence: 55
reasoning: robots.txt/ → 404 while robots.txt → 206 shows the slash-suffix rule, but it is untested on an unguessable key; root flips 200↔206 across cycles. A slash-suffixed nonce discriminates a shape-specific 404 rule from a key-specific whitelist.
verify_steps: GET https://cloud.app.box.com/K9wQ4zR7vM2n/ then GET https://cloud.app.box.com/; UA box-research/1.0; record status lines only.
impact: 206 on the nonce ⇒ object-store default handler fronts arbitrary paths origin-wide (reopens the parked CORS surface host-wide, medium-high); 404 ⇒ pinned-key whitelist, CORS scope stays bounded.
testability: PASSIVE
[NEXT] PROBE: GET https://cloud.app.box.com/K9wQ4zR7vM2n/ (fresh 12-char nonce + trailing slash, distinct from Pn9k4Wm2sX6v/t3Kv9Xq2Mp7Z/7xq9ZzK2nX4w) then GET https://cloud.app.box.com/, UA box-research/1.0, status lines only — slash-suffix is a new asset form (prior nonce probes were bare and never executed); 206 ⇒ origin-global object-store default, 404 ⇒ whitelist-scoped; the paired root re-characterizes the 200/206 flip.
## 2026-08-15 15:39:47 UTC dl.boxcloud.com (ling3)
## 2026-08-15 15:54:25 UTC dl.boxcloud.com (ling3)
## 2026-08-15 16:09:49 UTC dl.boxcloud.com (ling3)
## 2026-08-15 16:37:46 UTC dl.boxcloud.com (ling3)
## 2026-08-15 16:56:12 UTC dl.boxcloud.com (ling3)
## 2026-08-15 17:13:37 UTC dl.boxcloud.com (ling3)
## 2026-08-15 17:34:39 UTC dl.boxcloud.com (ling3)
## 2026-08-15 17:52:10 UTC dl.boxcloud.com (ling3)
## 2026-08-15 18:00:35 UTC dl.boxcloud.com (ling3)
## 2026-08-15 18:34:57 UTC dl.boxcloud.com (ling3)
## 2026-08-15 18:59:28 UTC dl.boxcloud.com (ling3)
## 2026-08-15 19:21:17 UTC dl.boxcloud.com (ling3)
## 2026-08-15 19:40:41 UTC dl.boxcloud.com (ling3)
## 2026-08-15 19:54:03 UTC dl.boxcloud.com (ling3)
## 2026-08-15 20:07:52 UTC dl.boxcloud.com (ling3)
## 2026-08-15 20:34:47 UTC dl.boxcloud.com (ling3)
## 2026-08-15 20:53:22 UTC dl.boxcloud.com (ling3)
## 2026-08-15 21:08:12 UTC dl.boxcloud.com (ling3)
## 2026-08-15 21:32:38 UTC dl.boxcloud.com (ling3)
## 2026-08-15 21:48:33 UTC dl.boxcloud.com (ling3)
## 2026-08-15 21:59:31 UTC dl.boxcloud.com (ling3)
## 2026-08-15 22:22:13 UTC dl.boxcloud.com (ling3)
## 2026-08-15 22:42:59 UTC dl.boxcloud.com (ling3)
## 2026-08-15 22:58:25 UTC dl.boxcloud.com (ling3)
## 2026-08-15 23:18:35 UTC dl.boxcloud.com (ling3)
## 2026-08-15 23:37:57 UTC dl.boxcloud.com (ling3)
## 2026-08-15 23:53:14 UTC dl.boxcloud.com (ling3)
## 2026-08-16 00:25:10 UTC dl.boxcloud.com (ling3)
## 2026-08-16 02:01:47 UTC dl.boxcloud.com (ling3)
## 2026-08-16 03:07:56 UTC dl.boxcloud.com (ling3)
## 2026-08-16 03:55:25 UTC dl.boxcloud.com (ling3)
## 2026-08-16 04:25:54 UTC dl.boxcloud.com (ling3)
## 2026-08-16 04:58:29 UTC dl.boxcloud.com (bigpickle)
[LEARN] ACCEPTED MISCONFIG @ cloud.app.box.com: Three consecutive executor cycles (03:09, 03:56, 04:46 UTC) return a byte-identical canned suite — root stable **200**, robots.txt/favicon.ico stable **206**, every single-encoded/collapsed/dot-query variant → 206, every double-encoded (`%252F`, `%252E`) and trailing-slash form → 404. The 206/Range layer is now best-fit as a single-decode **exact-key whitelist** over pinned static assets, not an origin-global object-store default.
[PARKED] Object-store default handler vs whitelist-routed discriminator: the bare-nonce probes (`Pn9k4Wm2sX6v` emitted twice) and the slash-suffix nonce (`K9wQ4zR7vM2n/`) never appear in executor output across 3+ cycles — the executor appears locked to a fixed 14-line suite and is not consuming [NEXT] lines. If the next emission is again ignored, mark this hypothesis UNTESTABLE via this pipeline and close the origin-global branch (evidence already favors whitelist).
[NEW] executor.py staleness: [NEXT] lines for arbitrary-path probes are dropped; only the canned suite executes — pipeline-level fault, not host signal.
[NEXT] PROBE: GET https://cloud.app.box.com/Wq5kZ8nT2mP4 (fresh 12-char nonce, never used, bare — distinct from Pn9k4Wm2sX6v/t3Kv9Xq2Mp7Z/7xq9ZzK2nX4w/K9wQ4zR7vM2n) then GET https://cloud.app.box.com/, UA box-research/1.0, status lines only — 206 ⇒ origin-global object-store default; 404 ⇒ pinned-key whitelist (then [CONFIRMED] whitelist); root pairing re-verifies 200 stability. If output is again the identical canned suite, the discriminator is [PARKED] as UNTESTABLE and I pivot to a new surface (OPTIONS/method surface on the 206 assets).
## 2026-08-16 05:24:26 UTC dl.boxcloud.com (bigpickle)
## 2026-08-16 05:49:27 UTC dl.boxcloud.com (ling3)
## 2026-08-16 06:14:35 UTC dl.boxcloud.com (ling3)
## 2026-08-16 06:59:54 UTC dl.boxcloud.com (ling3)
## 2026-08-16 07:32:50 UTC dl.boxcloud.com (ling3)
## 2026-08-16 07:56:15 UTC dl.boxcloud.com (ling3)
## 2026-08-16 08:19:15 UTC dl.boxcloud.com (ling3)
## 2026-08-16 08:50:31 UTC dl.boxcloud.com (ling3)
## 2026-08-16 09:12:34 UTC dl.boxcloud.com (ling3)
## 2026-08-16 09:40:45 UTC dl.boxcloud.com (ling3)
