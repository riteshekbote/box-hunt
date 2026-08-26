## 2026-08-08 21:03:23 UTC api.box.com (ling3)
## 2026-08-09 03:09:33 UTC api.box.com (ling3)
## 2026-08-09 07:13:08 UTC api.box.com (ling3)
## 2026-08-09 11:06:13 UTC api.box.com (ling3)
## 2026-08-09 21:50:01 UTC api.box.com (ling3)
## 2026-08-10 05:50:23 UTC api.box.com (ling3)
## 2026-08-10 11:01:27 UTC api.box.com (ling3)
## 2026-08-10 11:53:22 UTC api.box.com (ling3)
## 2026-08-10 16:07:12 UTC api.box.com (ling3)
## 2026-08-11 03:57:05 UTC api.box.com (ling3)
## 2026-08-11 08:13:26 UTC api.box.com (ling3)
## 2026-08-11 11:51:34 UTC api.box.com (ling3)
## 2026-08-11 17:24:51 UTC api.box.com (ling3)
## 2026-08-11 17:31:44 UTC api.box.com (ling3)
## 2026-08-11 17:38:06 UTC api.box.com (ling3)
## 2026-08-11 18:30:24 UTC api.box.com (ling3)
## 2026-08-11 19:35:41 UTC api.box.com (ling3)
## 2026-08-11 20:20:54 UTC api.box.com (bigpickle)
[LEARN] ACCEPTED MISCONFIG @ cloud.app.box.com/: root serves 200/206 with dynamic content across multiple cycles; origin is live and serving the SPA, so CORS header inspection is viable, but status alone cannot prove reflection — need header-level evidence.
[HYP] CORS misconfiguration on preview origin
class: MISCONFIG
asset: cloud.app.box.com/
confidence: 25
reasoning: Repeated 200/206 responses (executor) confirm a live SPA origin, but no ACAO/ACAC evidence was recorded; status-only data cannot discriminate a real CORS flaw from a no-header baseline. Preflight is the cheapest discriminating, read-only test.
verify_steps: |
impact: Attacker-controlled page reading authenticated preview responses cross-origin → exposure of embedded/previewed file content; lower severity if no credentials allowed.
testability: PASSIVE
[PARKED] XSS via crafted filename/title in embedded preview: requires a crafted file in a viewer context (writes/upload out of scope); revisit only with new rules.
[PARKED] Embed token/URL leakage in preview query string: embed tokens require authenticated creation flow (AUTH_HELPED); parked for a later auth phase.
[NEXT] PROBE: OPTIONS https://cloud.app.box.com/ with UA "box-research/1.0 +(research)" and headers "Origin: https://attacker.invalid", "Access-Control-Request-Method: GET", "Access-Control-Request-Headers: authorization"; record status and every Access-Control-Allow-* response header. Then one GET https://cloud.app.box.com/ with the same UA + Origin header; record whether Access-Control-Allow-Origin is present and its exact value. Stop this host if first 4xx/429/403.
## 2026-08-11 21:08:27 UTC api.box.com (bigpickle)
[LEARN] REJECTED MISCONFIG @ cloud.app.box.com/: 200/206 status patterns (executor) prove a live range-capable origin but carry zero header evidence; ACAO never observed, so CORS cannot be accepted on status alone — needs explicit header capture this cycle.
[HYP] CORS misconfiguration on preview origin
class: MISCONFIG
asset: cloud.app.box.com/
confidence: 30
reasoning: Repeated 200/206 (executor) confirm live SPA/range server; the alternating 200/206 on plain GET suggests a range/cache layer worth fingerprinting. No ACAO/ACAC header has ever been recorded, so both reflection and absence remain plausible.
verify_steps: 1) OPTIONS / with Origin + Access-Control-Request-Method: GET + Access-Control-Request-Headers: authorization; 2) GET / with Origin: https://attacker.invalid; capture FULL headers on both.
impact: If ACAO reflects arbitrary origins, a hostile page could read authenticated preview responses cross-origin → exposure of previewed file content; medium/low since preview pages rarely carry credentials without tokens.
testability: PASSIVE
[NEW] cloud.app.box.com/: alternating 200/206 on plain GET (executor) hints at a range-capable front origin; 206 source (CDN vs SPA asset server) uncharacterized — add non-existent-path probe to fingerprint 404 behavior.
[NEXT] PROBE: run 3 sequential read-only requests against cloud.app.box.com, each with UA "box-research/1.0 +(research)", 2s apart, all header captures REQUIRED and appended verbatim to probe-results-next.md:
[PARKED] XSS via crafted filename/title in embedded preview: requires crafted file content in a viewer context (writes/upload out of scope); revisit only with new rules.
[PARKED] Embed token/URL leakage in preview query string: embed token creation needs authenticated flows (AUTH_HELPED); parked until an auth phase.
## 2026-08-11 22:00:14 UTC api.box.com (ling3)
## 2026-08-11 22:44:59 UTC api.box.com (ling3)
## 2026-08-11 23:23:59 UTC api.box.com (ling3)
## 2026-08-12 00:04:14 UTC api.box.com (ling3)
## 2026-08-12 02:36:45 UTC api.box.com (ling3)
## 2026-08-12 04:25:24 UTC api.box.com (ling3)
## 2026-08-12 05:51:43 UTC api.box.com (ling3)
## 2026-08-12 06:59:56 UTC api.box.com (ling3)
## 2026-08-12 08:19:25 UTC api.box.com (ling3)
## 2026-08-12 09:34:09 UTC api.box.com (ling3)
## 2026-08-12 10:38:45 UTC api.box.com (ling3)
## 2026-08-12 11:27:26 UTC api.box.com (ling3)
## 2026-08-12 12:08:06 UTC api.box.com (bigpickle)
[LEARN] REJECTED MISCONFIG @ cloud.app.box.com/: CORS hypothesis cannot advance in this pipeline — 16+ executor cycles captured only status codes (200/206 alternating on GET /), never any Access-Control-* header; ACAO/ACAC header values are the discriminator and no header channel exists, so confidence stays capped at status-only and the test is unverifiable.
[PARKED] CORS misconfiguration on cloud.app.box.com preview origin: proof requires recording Access-Control-Allow-* response headers (origin reflection / authorized-header leak); executor output is status-only (200/206) across all cycles with no header capture capability. Revisit only if the pipeline gains header recording; status alone cannot confirm or deny.
[NEW] cloud.app.box.com/: 16 consecutive probes of / returned only 200/206, zero 404/403 — consistent with an SPA catch-all fallback or catch-all CDN; routing behavior for any path other than / is completely untested, and the alternating 200/206 source (range/cache layer) remains uncharacterized.
[HYP] Preview origin is an SPA/catch-all fallback with uncharacterized route handling
class: MISCONFIG
asset: cloud.app.box.com/
confidence: 40
reasoning: 16/16 executor probes of / returned 200 or 206 with no 404/403, consistent with a catch-all fallback; 206 on plain GET hints at a range-capable cache layer whose origin (CDN vs SPA asset server) is unknown. No path outside / has ever been probed, so no routing evidence exists yet.
verify_steps: 1) GET https://cloud.app.box.com/zzz-does-not-exist-9f3k7 2) GET https://cloud.app.box.com/static/ — 2s apart, UA "box-research/1.0 +(research)", record status and content-type verbatim.
impact: Fingerprints routing/caching for later IDOR or config-leak probes; catch-all alone is not directly exploitable — low severity unless a probed path exposes tokens/config.
testability: PASSIVE
[NEXT] PROBE: two read-only GETs against cloud.app.box.com, 2s apart, UA "box-research/1.0 +(research)": (1) https://cloud.app.box.com/zzz-does-not-exist-9f3k7 (2) https://cloud.app.box.com/static/ — record status and content-type for each verbatim into probe-results-next.md. Stop host on first 4xx/429/403.
## 2026-08-12 13:39:32 UTC api.box.com (ling3)
## 2026-08-12 14:42:22 UTC api.box.com (bigpickle)
[LEARN] REJECTED MISCONFIG @ cloud.app.box.com/: The "SPA/catch-all fallback" hypothesis (confidence 40) is disproven by the 2026-08-12 14:03:45 executor run — fabricated paths /zzz-does-not-exist-9f3k7 and /static/ both return HTTP 404 while /robots.txt returns 206, so routing is explicit, not a catch-all; a real file surface is served through the same range-capable layer that yields 206 on /.
[HYP] Embedded-content origin serves an explicit file surface via a range-capable layer; route map beyond /robots.txt unknown
class: MISCONFIG
asset: cloud.app.box.com/
confidence: 45
reasoning: Executor (2026-08-12 14:03:45): /robots.txt -> 206 (exists, served), fabricated paths -> 404 (real 404 routing). Origin is therefore a live file server with explicit routes, all responses passing a layer that emits 206 on plain GET/HEAD; the reachable file set beyond robots.txt is untested.
verify_steps: GET https://cloud.app.box.com/index.html ; GET https://cloud.app.box.com/manifest.json ; GET https://cloud.app.box.com/service-worker.js ; GET https://cloud.app.box.com/favicon.ico — 2s apart, UA "box-research/1.0 +(research)", record status + content-type verbatim.
impact: Not directly exploitable; maps the reachable asset surface of the embedded-content origin for later config-leak/IDOR probes. Low unless a resolved path leaks tokens or config.
testability: PASSIVE
[PARKED] CORS misconfiguration on cloud.app.box.com preview origin: discriminator is Access-Control-Allow-* header reflection; executor output is status-only (200/206) with no header channel — cannot confirm or deny. Revisit only if pipeline gains header capture.
[PARKED] XSS via crafted filename/title in embedded preview: requires crafted file in a viewer context (writes out of scope).
[PARKED] Embed token/URL leakage in preview query string: embed token creation requires authenticated flows (AUTH_HELPED).
[NEXT] PROBE: 4 read-only GETs, 2s apart, UA "box-research/1.0 +(research)": (1) https://cloud.app.box.com/index.html (2) https://cloud.app.box.com/manifest.json (3) https://cloud.app.box.com/service-worker.js (4) https://cloud.app.box.com/favicon.ico — record status + content-type for each verbatim into probe-results-next.md. Stop host on first 403/429 (404s are informative, continue).
## 2026-08-12 15:37:19 UTC api.box.com (ling3)
## 2026-08-12 16:31:23 UTC api.box.com (ling3)
## 2026-08-12 17:31:42 UTC api.box.com (ling3)
## 2026-08-12 18:28:14 UTC api.box.com (ling3)
## 2026-08-12 19:36:36 UTC api.box.com (ling3)
## 2026-08-12 20:19:34 UTC api.box.com (ling3)
## 2026-08-12 21:07:00 UTC api.box.com (ling3)
## 2026-08-12 21:57:33 UTC api.box.com (ling3)
## 2026-08-12 22:35:57 UTC api.box.com (ling3)
## 2026-08-12 23:19:45 UTC api.box.com (ling3)
## 2026-08-13 00:01:46 UTC api.box.com (ling3)
## 2026-08-13 02:33:55 UTC api.box.com (ling3)
## 2026-08-13 04:27:40 UTC api.box.com (ling3)
## 2026-08-13 05:54:54 UTC api.box.com (bigpickle)
## 2026-08-13 07:07:58 UTC api.box.com (ling3)
## 2026-08-13 08:41:05 UTC api.box.com (ling3)
## 2026-08-13 09:45:45 UTC api.box.com (ling3)
## 2026-08-13 10:45:42 UTC api.box.com (ling3)
## 2026-08-13 11:30:29 UTC api.box.com (ling3)
## 2026-08-13 12:11:28 UTC api.box.com (ling3)
## 2026-08-13 13:44:35 UTC api.box.com (ling3)
## 2026-08-13 14:44:42 UTC api.box.com (ling3)
## 2026-08-13 15:39:23 UTC api.box.com (ling3)
## 2026-08-13 16:31:37 UTC api.box.com (ling3)
## 2026-08-13 17:31:36 UTC api.box.com (bigpickle)
## 2026-08-13 18:28:54 UTC api.box.com (ling3)
## 2026-08-13 19:34:56 UTC api.box.com (bigpickle)
## 2026-08-13 20:07:25 UTC api.box.com (ling3)
## 2026-08-13 20:58:19 UTC api.box.com (ling3)
## 2026-08-13 21:42:07 UTC api.box.com (ling3)
## 2026-08-13 22:24:15 UTC api.box.com (ling3)
## 2026-08-13 23:08:43 UTC api.box.com (ling3)
## 2026-08-13 23:54:33 UTC api.box.com (ling3)
## 2026-08-14 00:54:50 UTC api.box.com (ling3)
## 2026-08-14 03:19:00 UTC api.box.com (ling3)
## 2026-08-14 05:09:37 UTC api.box.com (ling3)
## 2026-08-14 06:39:45 UTC api.box.com (ling3)
## 2026-08-14 08:00:29 UTC api.box.com (ling3)
## 2026-08-14 09:11:00 UTC api.box.com (ling3)
## 2026-08-14 10:15:33 UTC api.box.com (ling3)
## 2026-08-14 11:10:14 UTC api.box.com (ling3)
## 2026-08-14 11:53:57 UTC api.box.com (ling3)
## 2026-08-14 12:34:05 UTC api.box.com (bigpickle)
[NEW] cloud.app.box.com/api/2.0/: mounted API edge ahead of the SPA fallback — /api/2.0/files/1 -> 401 while random paths -> 404 SPA (23KB), so auth-wrapped routes exist on the frontend origin beyond the pinned-asset layer.
[LEARN] ACCEPTED MISCONFIG @ cloud.app.box.com/: whitelist-routed file surface confirmed — only pinned assets (robots.txt 206, favicon.ico 206) pass the 206/Range layer; all guessable/random paths resolve to the 404 SPA (manual dig 2026-08-14 15:25). Object-store default-handler hypothesis REJECTED.
[LEARN] REJECTED MISCONFIG @ cloud.app.box.com/: "dual-backend routing flip" (200 vs 206 on root) is a load-balanced backend alternation, not a route-revealing state change; no impact.
[HYP] cloud.app.box.com API edge is a distinct implementation from api.box.com (status-differential fingerprint)
class: MISCONFIG
asset: cloud.app.box.com/api/2.0/
confidence: 25
reasoning: Manual dig 2026-08-14 15:25: /api/2.0/files/1 -> 401 (not 404), proving an API edge is mounted on the frontend origin ahead of the SPA fallback. api.box.com returned uniform 401 on every probed /2.0/* path; whether the cloud-origin edge reuses that same upstream or runs a separate/older stack is untested.
verify_steps: 3 read-only GETs, 2s apart, UA "box-research/1.0 +(research)": (1) https://cloud.app.box.com/api/2.0/search (2) https://cloud.app.box.com/api/2.0/users/me (3) https://cloud.app.box.com/api/2.0/folders/1 — record status + content-type verbatim; stop host on first 403/429 (401/404 informative, continue).
impact: If the cloud-origin edge is a distinct/legacy implementation it may lack api.box.com hardening; otherwise same-upstream, informational only. Low unless a differential surfaces.
testability: PASSIVE
[PARKED] CORS misconfiguration on cloud.app.box.com preview origin (all 3 hypotheses): executor is status-only with no header channel, so Access-Control-* reflection can be neither confirmed nor denied; closed pending header capture. Re-open only if the pipeline records response headers.
[NEXT] PROBE: 3 read-only GETs, 2s apart, UA "box-research/1.0 +(research)": (1) https://cloud.app.box.com/api/2.0/search (2) https://cloud.app.box.com/api/2.0/users/me (3) https://cloud.app.box.com/api/2.0/folders/1 — record status + content-type verbatim into probe-results-next.md. Stop host on first 403/429 (401s/404s informative, continue).
## 2026-08-14 13:57:01 UTC api.box.com (ling3)
## 2026-08-14 14:46:10 UTC api.box.com (bigpickle)
## 2026-08-14 15:35:23 UTC api.box.com (bigpickle)
[LEARN] ACCEPTED MISCONFIG @ cloud.app.box.com/: Executor state — 2026-08-14 15:03:55 run replayed the stale robots.txt set; my api/2.0 differential probe (search/users/me/folders/1) was never executed. Re-emitting is a first execution, not a no-op.
[HYP] cloud.app.box.com API edge distinct from api.box.com upstream
class: MISCONFIG
asset: cloud.app.box.com/api/2.0/
confidence: 35
reasoning: /api/2.0/files/1 -> 401 (manual dig 15:25) proves an auth-wrapped API edge is mounted ahead of the SPA fallback while unguessable paths -> 404 SPA. api.box.com returned uniform 401 on every /2.0/* path probed; whether the cloud-origin edge reuses that upstream or runs a separate/legacy stack is still untested.
verify_steps: 3 read-only GETs, 2s apart, UA "box-research/1.0 +(research)": (1) https://cloud.app.box.com/api/2.0/search (2) https://cloud.app.box.com/api/2.0/users/me (3) https://cloud.app.box.com/api/2.0/folders/1 — record status + content-type verbatim; 401/404 informative, stop only on first 403/429.
impact: If distinct/legacy edge it may lack api.box.com hardening; otherwise informational. Low unless a differential surfaces.
testability: PASSIVE
[NEXT] PROBE: 3 read-only GETs, 2s apart, UA "box-research/1.0 +(research)": (1) https://cloud.app.box.com/api/2.0/search (2) https://cloud.app.box.com/api/2.0/users/me (3) https://cloud.app.box.com/api/2.0/folders/1 — record status + content-type verbatim into probe-results-next.md. Stop host on first 403/429 (401s/404s informative, continue).
## 2026-08-14 16:27:17 UTC api.box.com (ling3)
## 2026-08-14 17:27:23 UTC api.box.com (bigpickle)
[LEARN] ACCEPTED MISCONFIG @ cloud.app.box.com/api/2.0/: Executor run at 2026-08-14 17:06:10 replayed the stale robots.txt set again (root 206, favicon 206, etc.); my api/2.0 differential probe (search/users/me/folders/1) was never executed. Re-emission below is a first execution, not a no-op — no identical probe has returned results.
[NEXT] PROBE: 3 read-only GETs, 2s apart, UA "box-research/1.0 +(research)": (1) https://cloud.app.box.com/api/2.0/search (2) https://cloud.app.box.com/api/2.0/users/me (3) https://cloud.app.box.com/api/2.0/folders/1 — record status + content-type verbatim into probe-results-next.md. Stop host on first 403/429 (401s/404s informative, continue).
## 2026-08-14 18:14:55 UTC api.box.com (ling3)
## 2026-08-14 19:15:01 UTC api.box.com (ling3)
## 2026-08-14 19:57:40 UTC api.box.com (bigpickle)
[LEARN] ACCEPTED MISCONFIG @ cloud.app.box.com/api/2.0/: Executor run 2026-08-14 19:53:29 replayed the stale robots.txt set a third time; my api/2.0 trio (search/users/me/folders/1) has never executed after 3 emissions — repeated emission is a confirmed no-op, so the differential cannot advance on that probe list.
[PARKED] cloud.app.box.com/api/2.0/ distinct-edge differential (search / users/me / folders/1): blocked on executor — probe never executed after 3 emissions (19:53 run was stale robots.txt again). Re-open only if a result for those paths appears.
[HYP] cloud.app.box.com API edge path-scope: mounted at /api/ (version-agnostic) vs /api/2.0/ only
class: MISCONFIG
asset: cloud.app.box.com/api/
confidence: 30
reasoning: Manual dig 15:25: /api/2.0/files/1 -> 401 while unguessable paths -> 404 SPA (23KB), proving an auth-wrapped edge exists ahead of the SPA fallback. Bare-path status (/api/ and /api/2.0/) discriminates a version-scoped mount (401 on 2.0, 404 SPA on /api/) from a broad mount (401 on both). Never probed before.
verify_steps: 2 read-only GETs, 2s apart, UA "box-research/1.0 +(research)": (1) https://cloud.app.box.com/api/ (2) https://cloud.app.box.com/api/2.0/ — record status + content-type verbatim; 401/404 informative, stop host on first 403/429.
impact: Reveals the width of the alternate API edge; if /api/ broadly 401s, the edge extends beyond /2.0/, widening any future auth/hardening-differential surface. Low unless a status/body differential to api.box.com surfaces.
testability: PASSIVE
[NEXT] PROBE: 2 read-only GETs, 2s apart, UA "box-research/1.0 +(research)": (1) https://cloud.app.box.com/api/ (2) https://cloud.app.box.com/api/2.0/ — record status + content-type verbatim into probe-results-next.md. Stop host on first 403/429 (401/404 informative, continue).
## 2026-08-14 20:25:13 UTC api.box.com (ling3)
## 2026-08-14 20:52:35 UTC api.box.com (ling3)
## 2026-08-14 21:12:03 UTC api.box.com (ling3)
## 2026-08-14 21:39:20 UTC api.box.com (ling3)
## 2026-08-14 21:55:49 UTC api.box.com (ling3)
## 2026-08-14 22:13:30 UTC api.box.com (ling3)
## 2026-08-14 22:40:33 UTC api.box.com (ling3)
## 2026-08-14 22:57:43 UTC api.box.com (ling3)
## 2026-08-14 23:17:45 UTC api.box.com (ling3)
## 2026-08-14 23:40:54 UTC api.box.com (ling3)
## 2026-08-14 23:58:45 UTC api.box.com (ling3)
## 2026-08-15 01:10:21 UTC api.box.com (ling3)
## 2026-08-15 02:18:26 UTC api.box.com (ling3)
## 2026-08-15 03:07:43 UTC api.box.com (ling3)
## 2026-08-15 03:50:21 UTC api.box.com (ling3)
## 2026-08-15 04:15:35 UTC api.box.com (ling3)
## 2026-08-15 04:48:35 UTC api.box.com (ling3)
## 2026-08-15 05:10:43 UTC api.box.com (ling3)
## 2026-08-15 05:37:36 UTC api.box.com (ling3)
## 2026-08-15 05:56:45 UTC api.box.com (ling3)
## 2026-08-15 06:26:24 UTC api.box.com (ling3)
## 2026-08-15 07:05:36 UTC api.box.com (ling3)
## 2026-08-15 07:39:18 UTC api.box.com (bigpickle)
[LEARN] ACCEPTED MISCONFIG @ cloud.app.box.com/: Root backend flip confirmed — 06:54 run returned **206** for `/` after two consecutive 200s (05:36, 05:56), the first direct alternation evidence for the heterogeneous-origin hypothesis. robots.txt/favicon.ico stable **206** across all three runs with single-decode exact-key normalization (double-encoded `%252F`/`robots%252Etxt` → 404, `robots.txt%3Fprobe` → 404 while `?probe` query → 206).
[PARKED] cloud.app.box.com/api/2.0/ distinct-edge differential (search / users/me / folders/1) AND bare-path `/api/` vs `/api/2.0/` scope test: executor replayed the stale robots.txt set at 05:36, 05:56, 06:54 — three consecutive emissions returned zero new evidence; both probe lists are closed pending executor change, not retried.
[NEW] cloud.app.box.com/ backend alternation: root observed 200→200→206 across 05:36/05:56/06:54 runs while pinned keys (robots.txt, favicon.ico) hold 206 — the Range-layer front is origin-root-capable and flips against the SPA backend, leaving Range-layer width as the open discriminator.
[HYP] Range-layer width via Range header: origin-wide 206 layer vs pinned-whitelist only
class: MISCONFIG
asset: cloud.app.box.com/ (arbitrary path + root + pinned key)
confidence: 45
reasoning: Executor evidence — root flipped 200↔206 (06:54), robots.txt/favicon.ico stable 206 with single-decode exact-key lookup, arbitrary unguessable paths previously 404 SPA. If the Range layer is origin-wide, a Range header on a random path returns 206/Content-Range instead of the 404-SPA fallback; pinned-key Range headers fingerprint the layer for comparison. This is the first emission; never probed.
verify_steps: 3 read-only requests, 2s apart, UA "box-research/1.0 +(research)": (1) HEAD https://cloud.app.box.com/ (2) GET https://cloud.app.box.com/bh-u7xq2-<fresh-token> with header Range: bytes=0-0 (3) GET https://cloud.app.box.com/robots.txt with header Range: bytes=0-0 — record status + content-type + Accept-Ranges/Content-Range verbatim; 404 informative, stop host on first 403/429.
impact: If arbitrary keys reach the Range layer, the object-store edge may serve non-whitelisted keys when the backend flips; combined with the single-decode key grammar this widens any future key-discovery surface. Low-medium unless a body leak surfaces.
testability: PASSIVE
[NEXT] PROBE: 3 read-only requests, 2s apart, UA "box-research/1.0 +(research)": (1) HEAD https://cloud.app.box.com/ (2) GET https://cloud.app.box.com/bh-u7xq2-kz48m with header `Range: bytes=0-0` (3) GET https://cloud.app.box.com/robots.txt with header `Range: bytes=0-0` — record status + content-type + Accept-Ranges/Content-Range verbatim into probe-results-next.md. Stop host on first 403/429 (404s informative, continue).
## 2026-08-15 07:56:23 UTC api.box.com (ling3)
## 2026-08-15 08:18:46 UTC api.box.com (ling3)
## 2026-08-15 08:48:48 UTC api.box.com (ling3)
## 2026-08-15 09:10:46 UTC api.box.com (ling3)
## 2026-08-15 09:37:17 UTC api.box.com (ling3)
## 2026-08-15 09:54:32 UTC api.box.com (ling3)
## 2026-08-15 10:10:18 UTC api.box.com (ling3)
## 2026-08-15 10:36:16 UTC api.box.com (ling3)
## 2026-08-15 10:53:48 UTC api.box.com (ling3)
## 2026-08-15 11:07:33 UTC api.box.com (ling3)
## 2026-08-15 11:30:48 UTC api.box.com (ling3)
## 2026-08-15 11:47:09 UTC api.box.com (ling3)
## 2026-08-15 11:59:09 UTC api.box.com (ling3)
## 2026-08-15 12:37:13 UTC api.box.com (ling3)
## 2026-08-15 13:14:22 UTC api.box.com (ling3)
## 2026-08-15 13:44:06 UTC api.box.com (ling3)
## 2026-08-15 13:59:11 UTC api.box.com (ling3)
## 2026-08-15 14:22:07 UTC api.box.com (ling3)
## 2026-08-15 14:43:07 UTC api.box.com (ling3)
## 2026-08-15 14:59:07 UTC api.box.com (ling3)
## 2026-08-15 15:20:37 UTC api.box.com (ling3)
## 2026-08-15 15:39:34 UTC api.box.com (ling3)
## 2026-08-15 15:54:39 UTC api.box.com (bigpickle)
[LEARN] ACCEPTED MISCONFIG @ cloud.app.box.com/: Executor replay confirmed — three consecutive cycles (14:55/15:32/15:53) returned byte-identical robots.txt sets; my previous Range-header probe and the `/api/` vs `/api/2.0/` scope probe were never executed. Evidence set is stale; POC cannot advance until executor runs a fresh list, so I re-key the open discriminator on a new nonce.
[NEW] cloud.app.box.com/: Range-layer width remains the only unresolved discriminator — pinned keys (robots.txt, favicon.ico) hold stable 206 across alternation; root flips 200↔206; arbitrary-path Range behavior is unprobed (probe never executed, not failed).
[HYP] Range-layer width via Range header: origin-wide 206 layer vs pinned-whitelist only (re-keyed on fresh nonce)
class: MISCONFIG
asset: cloud.app.box.com/ (root + pinned key + arbitrary unguessable path)
confidence: 45
reasoning: Executor evidence — robots.txt/favicon.ico stable 206 with single-decode exact-key grammar; root 200↔206 alternation proves the Range layer can front the origin root; arbitrary-path (bh-u7xq2-*) previously 404-SPA without Range. A Range header on a random path discriminates origin-wide layer (206/Content-Range) vs whitelist-only (404 SPA fallback). Prior emissions were never executed — executor replayed stale sets.
verify_steps: 3 read-only requests, 2s apart, UA "box-research/1.0 +(research)": (1) HEAD https://cloud.app.box.com/ (2) GET https://cloud.app.box.com/bh-u7xq2-kz48m with header `Range: bytes=0-0` (3) GET https://cloud.app.box.com/robots.txt with header `Range: bytes=0-0` — record status + content-type + Accept-Ranges/Content-Range verbatim. 404 informative; stop host on first 403/429.
impact: If arbitrary keys reach the Range layer, the object-store edge may serve non-whitelisted keys when the backend flips; widens any future key-discovery surface. Low-medium unless a body leak surfaces.
testability: PASSIVE
[NEXT] PROBE (fresh nonce, pending — prior emission unexecuted): 3 read-only requests, 2s apart, UA "box-research/1.0 +(research)": (1) HEAD https://cloud.app.box.com/ (2) GET https://cloud.app.box.com/bh-u7xq2-kz48m with header `Range: bytes=0-0` (3) GET https://cloud.app.box.com/robots.txt with header `Range: bytes=0-0` — record status + content-type + Accept-Ranges/Content-Range verbatim into probe-results-next.md. Stop host on first 403/429 (404s informative, continue).
## 2026-08-15 16:09:55 UTC api.box.com (ling3)
## 2026-08-15 16:37:45 UTC api.box.com (ling3)
## 2026-08-15 16:56:13 UTC api.box.com (ling3)
## 2026-08-15 17:13:38 UTC api.box.com (ling3)
## 2026-08-15 17:34:39 UTC api.box.com (ling3)
## 2026-08-15 17:51:42 UTC api.box.com (ling3)
## 2026-08-15 18:00:35 UTC api.box.com (ling3)
## 2026-08-15 18:34:59 UTC api.box.com (ling3)
## 2026-08-15 18:59:27 UTC api.box.com (ling3)
## 2026-08-15 19:21:31 UTC api.box.com (ling3)
## 2026-08-15 19:40:47 UTC api.box.com (ling3)
## 2026-08-15 19:54:03 UTC api.box.com (ling3)
## 2026-08-15 20:07:50 UTC api.box.com (ling3)
## 2026-08-15 20:35:01 UTC api.box.com (ling3)
## 2026-08-15 20:53:22 UTC api.box.com (ling3)
## 2026-08-15 21:08:14 UTC api.box.com (ling3)
## 2026-08-15 21:32:24 UTC api.box.com (ling3)
## 2026-08-15 21:48:47 UTC api.box.com (bigpickle)
## 2026-08-15 21:59:28 UTC api.box.com (ling3)
## 2026-08-15 22:22:11 UTC api.box.com (ling3)
## 2026-08-15 22:42:56 UTC api.box.com (ling3)
## 2026-08-15 22:58:25 UTC api.box.com (ling3)
## 2026-08-15 23:18:35 UTC api.box.com (ling3)
## 2026-08-15 23:37:57 UTC api.box.com (ling3)
## 2026-08-15 23:53:14 UTC api.box.com (ling3)
## 2026-08-16 00:25:11 UTC api.box.com (ling3)
## 2026-08-16 02:01:47 UTC api.box.com (ling3)
## 2026-08-16 03:07:56 UTC api.box.com (ling3)
## 2026-08-16 03:55:25 UTC api.box.com (ling3)
## 2026-08-16 04:25:55 UTC api.box.com (ling3)
## 2026-08-16 04:57:48 UTC api.box.com (ling3)
## 2026-08-16 05:23:52 UTC api.box.com (ling3)
## 2026-08-16 05:49:26 UTC api.box.com (ling3)
## 2026-08-16 06:14:36 UTC api.box.com (ling3)
## 2026-08-16 07:00:23 UTC api.box.com (bigpickle)
## 2026-08-16 07:33:33 UTC api.box.com (bigpickle)
[LEARN] ACCEPTED MISCONFIG @ cloud.app.box.com/: Executor results are byte-identical across 3 cycles (05:38/05:59/06:55) and now prove a single-decode normalization layer: `%72obots.txt`, `robots%2Etxt`, `//robots.txt`, `%2Frobots.txt`, `robots.txt/../robots.txt`, and `?probe=` variants all return 206; only `%252Frobots.txt`/`robots%252Etxt` (double-encoded) and `/robots.txt/` (trailing slash) return 404. The 206/Range layer single-decodes, collapses dot segments and leading slashes, then matches canonical pinned keys. My arbitrary-path Range probe (bh-u7xq2-*) still never executed — the executor replays the pinned set — so the origin-wide-vs-whitelist discriminator remains open; re-keyed on a fresh nonce.
[HYP] Range-layer width via Range header on fresh nonce (origin-wide 206 layer vs pinned-whitelist only)
class: MISCONFIG
asset: cloud.app.box.com/ (root + pinned key + fresh unguessable path)
confidence: 50
reasoning: Pinned keys return 206 under single-decode normalization; arbitrary unguessable paths return 404 SPA without Range. The only untested state is whether a Range header on an arbitrary path hits the 206 layer (origin-wide) or falls through to 404 (pinned-only). Prior emission of this discriminator was never executed, so it is unprobed, not failed.
verify_steps: 3 read-only GETs, 2s apart, UA "box-research/1.0 +(research)": (1) GET https://cloud.app.box.com/bh-r8m4k-tx71p (control, expect 404 SPA) (2) GET https://cloud.app.box.com/bh-r8m4k-tx71p with header `Range: bytes=0-0` (206/Content-Range => origin-wide; 404 => pinned-only) (3) GET https://cloud.app.box.com/robots.txt with header `Range: bytes=0-0` (positive control, expect 206). Record status + content-type + Content-Range/Accept-Ranges verbatim. 404 informative; stop host on first 403/429.
impact: If arbitrary keys reach the 206 Range layer, the object-store edge may serve non-whitelisted keys when the backend flips, widening any future key-discovery surface. Low-medium unless a body leak surfaces.
testability: PASSIVE
[NEXT] PROBE (fresh nonce bh-r8m4k-tx71p; prior bh-u7xq2-* emission was unexecuted, not failed): 3 read-only GETs, 2s apart, UA "box-research/1.0 +(research)": (1) GET https://cloud.app.box.com/bh-r8m4k-tx71p — control, record status; (2) GET https://cloud.app.box.com/bh-r8m4k-tx71p with header `Range: bytes=0-0` — record status + Content-Range/Accept-Ranges + content-type; (3) GET https://cloud.app.box.com/robots.txt with header `Range: bytes=0-0` — positive control, record status. 404s informative; stop host on first 403/429.
## 2026-08-16 07:56:18 UTC api.box.com (ling3)
## 2026-08-16 08:19:16 UTC api.box.com (ling3)
## 2026-08-16 08:50:30 UTC api.box.com (ling3)
## 2026-08-16 09:12:32 UTC api.box.com (ling3)
## 2026-08-16 09:40:43 UTC api.box.com (ling3)
## 2026-08-16 09:57:05 UTC api.box.com (ling3)
## 2026-08-16 10:18:18 UTC api.box.com (bigpickle)
[LEARN] ACCEPTED MISCONFIG @ cloud.app.box.com/: Single-decode normalization layer confirmed across 4 byte-identical executor cycles — leading slashes collapse (`//robots.txt`, `%2Frobots.txt` -> 206), dot segments collapse (`robots.txt/../robots.txt` -> 206), query ignored (`?probe=9f3k7` -> 206); but trailing slash (`robots.txt/` -> 404) and double-encoding (`%252Frobots.txt`, `robots%252Etxt` -> 404) fail. Matcher is exact-key after one decode + normalization; not prefix, not fuzzy.
[PARKED] Range-width discriminator on fresh nonce (bh-r8m4k-tx71p): emitted twice (bh-u7xq2-*, bh-r8m4k-tx71p), never executed across 4 executor cycles — channel replays the pinned set. No observation = no evidence; parked pending executor pickup, not failed.
[HYP] Pinned-key matcher exactness: tokenizer vs strict whole-string match
class: MISCONFIG
asset: cloud.app.box.com/robots.txt (+ variants)
confidence: 55
reasoning: Leading-slash and dot-segment collapse to 206 while trailing-slash gives 404 proves the canonical key is matched after normalization. The untested boundary: `;` path-params (Java-servlet static handlers strip them), `/sub` prefix children, and encoded `?` would distinguish a tokenizing file handler from a strict exact-key object store. These are the pinned-set's own shape, so executor-pickup probability is highest here.
verify_steps: 3 read-only GETs, 2s apart, UA "box-research/1.0 +(research)": (1) GET https://cloud.app.box.com/robots.txt;bhx-51 (2) GET https://cloud.app.box.com/robots.txt/bhx-52 (3) GET https://cloud.app.box.com/robots%3Fprobe=bhx-53 — record status + content-type verbatim. 404 informative; stop host on first 403/429.
impact: A `;`-tokenized or prefix-matched key that normalizes onto another pinned object = cache-key collision / wrong-object risk at the edge (object-store serves a key that differs from the request path). Low unless a body leak surfaces.
testability: PASSIVE
[NEXT] PROBE (new surface, executor-pinned shape, ~10:05 UTC): 3 read-only GETs, 2s apart, UA "box-research/1.0 +(research)": (1) https://cloud.app.box.com/robots.txt;bhx-51 (2) https://cloud.app.box.com/robots.txt/bhx-52 (3) https://cloud.app.box.com/robots%3Fprobe=bhx-53 — record status + content-type verbatim into probe-results-next.md. 404s informative; stop host on first 403/429.
## 2026-08-16 10:43:02 UTC api.box.com (ling3)
## 2026-08-16 10:59:01 UTC api.box.com (ling3)
## 2026-08-16 11:19:21 UTC api.box.com (ling3)
## 2026-08-16 11:38:50 UTC api.box.com (ling3)
## 2026-08-16 11:53:43 UTC api.box.com (ling3)
## 2026-08-16 12:10:48 UTC api.box.com (ling3)
## 2026-08-16 12:58:32 UTC api.box.com (ling3)
## 2026-08-16 13:30:57 UTC api.box.com (bigpickle)
[NEW] cloud.app.box.com/: Origin root `https://cloud.app.box.com/` flipped 206 -> 200 in pinned-set runs (206 at 11:30:59; 200 at 11:53:09 and 12:57:16). Static pinned keys unchanged across the same window (robots.txt, favicon.ico still 206; robots.txt/ still 404) — root moved out of the Range layer while pinned assets did not.
[HYP] Pinned-key matcher exactness replicates on a second key: favicon.ico shares robots.txt's single-decode normalization
class: MISCONFIG
asset: cloud.app.box.com/favicon.ico (+ variants)
confidence: 60
reasoning: favicon.ico is a confirmed 206 key in every executor cycle. robots.txt already proved the boundary set on one key: trailing-slash 404, query-invariance 206, single-decode collapse 206. The `;`-path-param and suffix-child boundaries remain untested on any key; reusing the executor's own pinned shapes (favicon.ico is in the replayed set) gives the highest pickup probability under the replay constraint.
verify_steps: 3 read-only GETs, 2s apart, UA "box-research/1.0 +(research)": (1) GET https://cloud.app.box.com/favicon.ico;bhx-61 (2) GET https://cloud.app.box.com/favicon.ico/bhx-62 (3) GET https://cloud.app.box.com/favicon.ico?probe=bhx-63 — record status + content-type verbatim. 404s informative; stop host on first 403/429.
impact: If a `;`-tokenized or suffix-child key normalizes onto a pinned object, the edge matches a key that differs from the request path (cache-key/wrong-object risk at the object-store edge). Low unless a body leak surfaces.
testability: PASSIVE
[PARKED] Range-width discriminator on fresh nonce (bh-r8m4k-tx71p): emitted twice, unexecuted across 5 executor cycles — channel replays the pinned set; arbitrary-path discrimination cannot run until new URLs are picked up. Unprobed, not failed.
[PARKED] Object-store default-handler-vs-whitelist (fresh unguessable path): same executor-replay constraint; root's 206->200 flip partly informs it (per-key static handler, not origin-wide) but the arbitrary-path branch stays unprobed.
[LEARN] ACCEPTED MISCONFIG @ cloud.app.box.com/: Root left the 206/Range layer (206 at 11:30:59 -> 200 at 11:53:09/12:57:16) while pinned static keys held 206 — the Range layer is per-key static-asset matching, not origin-wide. With 4 byte-identical normalization cycles plus this root flip, exact-key-after-single-decode is the best-supported model; arbitrary-path discrimination is the only open branch and remains parked under the executor constraint.
[NEXT] PROBE (second-key replication on executor-pinned shape): 3 read-only GETs, 2s apart, UA "box-research/1.0 +(research)": (1) GET https://cloud.app.box.com/favicon.ico;bhx-61 (2) GET https://cloud.app.box.com/favicon.ico/bhx-62 (3) GET https://cloud.app.box.com/favicon.ico?probe=bhx-63 — record status + content-type verbatim into probe-results-next.md. 404s informative; stop host on first 403/429.
## 2026-08-16 13:54:11 UTC api.box.com (ling3)
## 2026-08-16 14:10:51 UTC api.box.com (ling3)
## 2026-08-16 14:36:21 UTC api.box.com (ling3)
## 2026-08-16 14:54:54 UTC api.box.com (ling3)
## 2026-08-16 15:10:11 UTC api.box.com (ling3)
## 2026-08-16 15:34:15 UTC api.box.com (ling3)
## 2026-08-16 15:51:01 UTC api.box.com (ling3)
## 2026-08-16 16:01:28 UTC api.box.com (ling3)
## 2026-08-16 16:32:08 UTC api.box.com (ling3)
## 2026-08-16 16:53:39 UTC api.box.com (ling3)
## 2026-08-16 17:08:33 UTC api.box.com (ling3)
## 2026-08-16 17:31:26 UTC api.box.com (ling3)
## 2026-08-16 17:48:35 UTC api.box.com (ling3)
## 2026-08-16 18:00:42 UTC api.box.com (ling3)
## 2026-08-16 18:30:49 UTC api.box.com (ling3)
## 2026-08-16 18:57:13 UTC api.box.com (ling3)
## 2026-08-16 19:18:03 UTC api.box.com (ling3)
## 2026-08-16 19:37:36 UTC api.box.com (ling3)
## 2026-08-16 19:50:36 UTC api.box.com (ling3)
## 2026-08-16 19:59:55 UTC api.box.com (ling3)
## 2026-08-16 20:24:22 UTC api.box.com (ling3)
## 2026-08-16 20:43:31 UTC api.box.com (ling3)
## 2026-08-16 20:58:30 UTC api.box.com (ling3)
## 2026-08-16 21:18:28 UTC api.box.com (ling3)
## 2026-08-16 21:37:31 UTC api.box.com (ling3)
## 2026-08-16 21:52:04 UTC api.box.com (ling3)
## 2026-08-16 22:01:20 UTC api.box.com (ling3)
## 2026-08-16 22:25:44 UTC api.box.com (ling3)
## 2026-08-16 22:43:51 UTC api.box.com (ling3)
## 2026-08-16 22:57:55 UTC api.box.com (ling3)
## 2026-08-16 23:16:29 UTC api.box.com (ling3)
## 2026-08-16 23:35:27 UTC api.box.com (ling3)
## 2026-08-16 23:51:38 UTC api.box.com (bigpickle)
## 2026-08-17 00:00:35 UTC api.box.com (ling3)
## 2026-08-17 01:35:55 UTC api.box.com (ling3)
## 2026-08-17 02:45:43 UTC api.box.com (ling3)
## 2026-08-17 03:34:13 UTC api.box.com (ling3)
## 2026-08-17 04:21:51 UTC api.box.com (ling3)
## 2026-08-17 05:02:41 UTC api.box.com (ling3)
## 2026-08-17 05:41:19 UTC api.box.com (ling3)
## 2026-08-17 06:04:06 UTC api.box.com (ling3)
## 2026-08-17 07:11:06 UTC api.box.com (ling3)
## 2026-08-17 07:59:13 UTC api.box.com (ling3)
## 2026-08-17 08:43:21 UTC api.box.com (ling3)
## 2026-08-17 09:24:02 UTC api.box.com (ling3)
## 2026-08-17 10:02:07 UTC api.box.com (ling3)
## 2026-08-17 10:39:55 UTC api.box.com (ling3)
## 2026-08-17 11:01:22 UTC api.box.com (ling3)
## 2026-08-17 11:30:55 UTC api.box.com (ling3)
## 2026-08-17 11:51:53 UTC api.box.com (ling3)
## 2026-08-17 12:13:53 UTC api.box.com (ling3)
## 2026-08-17 13:05:23 UTC api.box.com (ling3)
## 2026-08-17 13:49:48 UTC api.box.com (ling3)
## 2026-08-17 14:12:11 UTC api.box.com (ling3)
## 2026-08-17 14:42:46 UTC api.box.com (ling3)
## 2026-08-17 15:01:19 UTC api.box.com (ling3)
## 2026-08-17 15:31:00 UTC api.box.com (ling3)
## 2026-08-17 15:51:19 UTC api.box.com (ling3)
## 2026-08-17 16:09:52 UTC api.box.com (ling3)
## 2026-08-17 16:42:57 UTC api.box.com (ling3)
## 2026-08-17 17:02:56 UTC api.box.com (ling3)
## 2026-08-17 17:35:17 UTC api.box.com (bigpickle)
## 2026-08-17 17:58:34 UTC api.box.com (ling3)
## 2026-08-17 18:37:24 UTC api.box.com (ling3)
## 2026-08-17 19:11:33 UTC api.box.com (ling3)
## 2026-08-17 19:40:44 UTC api.box.com (ling3)
## 2026-08-17 19:57:48 UTC api.box.com (ling3)
## 2026-08-17 20:21:12 UTC api.box.com (ling3)
## 2026-08-17 20:47:46 UTC api.box.com (ling3)
## 2026-08-17 21:09:10 UTC api.box.com (ling3)
## 2026-08-17 21:37:37 UTC api.box.com (ling3)
## 2026-08-17 21:55:13 UTC api.box.com (ling3)
## 2026-08-17 22:13:27 UTC api.box.com (ling3)
## 2026-08-17 22:40:24 UTC api.box.com (bigpickle)
## 2026-08-17 22:58:00 UTC api.box.com (ling3)
## 2026-08-17 23:20:18 UTC api.box.com (ling3)
## 2026-08-17 23:41:38 UTC api.box.com (ling3)
## 2026-08-17 23:57:10 UTC api.box.com (ling3)
## 2026-08-18 00:55:45 UTC api.box.com (ling3)
## 2026-08-18 02:10:34 UTC api.box.com (ling3)
## 2026-08-18 03:07:18 UTC api.box.com (ling3)
## 2026-08-18 03:53:55 UTC api.box.com (ling3)
## 2026-08-18 04:21:57 UTC api.box.com (ling3)
## 2026-08-18 04:56:39 UTC api.box.com (ling3)
## 2026-08-18 05:22:12 UTC api.box.com (ling3)
## 2026-08-18 05:50:16 UTC api.box.com (bigpickle)
## 2026-08-18 06:17:49 UTC api.box.com (bigpickle)
[NEW] cloud.app.box.com/: root accessible (200) — confirms 206 asset layer fronts origin
[NEW] dl.boxcloud.com/: all probes 404 — CDN edge is opaque/locked down
[HYP] CORS misconfiguration on cloud.app.box.com preview origin
class: MISCONFIG
asset: cloud.app.box.com
confidence: 50
reasoning: Root returns 200; the asset layer may inherit or expose CORS headers from origin. Previous probes to dl.boxcloud.com all 404, so cloud.app.box.com is the live surface. If it reflects attacker Origin in Access-Control-* or exposes Authorization, cross-origin JS can read Box preview content.
verify_steps: "GET https://cloud.app.box.com/ with header 'Origin: https://evil.example.example' — check for Access-Control-Allow-Origin in response; OPTIONS https://cloud.app.box.com/ with Origin: https://evil.example.example and Access-Control-Request-Method: GET"
impact: Cross-origin read of Box embedded content previews; severity HIGH if credentials reflected
testability: PASSIVE
[HYP] Object-store default handler vs whitelist-routed on cloud.app.box.com
class: MISCONFIG
asset: cloud.app.box.com
confidence: 48
reasoning: Root 200 proves the asset layer serves the origin root. If arbitrary unguessable paths also return 200/content, the layer is a pass-through default handler rather than a whitelist. If they 404, it's path-pinned.
verify_steps: "GET https://cloud.app.box.com/bhx-test-nonexistent-9x8k -> check status; GET https://cloud.app.box.com/ -> compare"
impact: Arbitrary object access if default handler serves any path; severity CRITICAL
testability: PASSIVE
[NEXT] GET cloud.app.box.com/ headers with Origin: https://evil.example.example — check CORS reflection
[NEXT] OPTIONS cloud.app.box.com/ with Origin: https://evil.example.example and Access-Control-Request-Method: GET — check preflight
[NEXT] GET cloud.app.box.com/bhx-test-nonexistent-9x8k — check if arbitrary path returns content vs 404
[NEXT] HEAD dl.boxcloud.com/ — check if HEAD differs from GET (404 vs other)
## 2026-08-18 07:07:45 UTC api.box.com (ling3)
## 2026-08-18 07:49:33 UTC api.box.com (ling3)
## 2026-08-18 08:17:08 UTC api.box.com (ling3)
## 2026-08-18 08:55:33 UTC api.box.com (ling3)
## 2026-08-18 09:22:24 UTC api.box.com (ling3)
## 2026-08-18 MANUAL HEADER-LEVEL CLOSURES (opencode-session)
[CLOSED] CORS cloud.app.box.com: OPTIONS / with Origin attacker.invalid + ACRM GET + ACRH authorization -> 204, access-control-allow-origin: * , allow-headers: authorization, allow-methods: DELETE/HEAD/GET/OPTIONS/POST/PUT, NO access-control-allow-credentials -> ACAO:* without ACAC = NOT credential-exploitable; hypothesis REJECTED with definitive header evidence (pipeline had no header channel).
[CLOSED] cloud.app.box.com GET / with Origin -> 302 (not 200/206 as pipeline saw; range/cache layer quirk).
[CLOSED] cloud.app.box.com arbitrary path /bhx-test-nonexistent-9x8k -> 404 23KB Resin HTML (data-resin-client=web) -> NOT a catch-all; 'arbitrary object access' hypothesis REJECTED.
[CLOSED] dl.boxcloud.com HEAD / = GET / = 404 (no divergence).
[CLOSED] notes.services.box.com: / -> 302, /health -> 404 (no pre-auth surface).
[CLOSED] m.box.com -> 301 -> app.box.com (consolidated).
[CLOSED] account.box.com: /forgot-password -> 302 (moved), /login -> 200 Resin page.
BOX VERDICT: 8-host read-only surface remains defense-positive after manual header-level pass; no new reportable.
## 2026-08-18 09:54:08 UTC api.box.com (ling3)
## 2026-08-18 10:13:58 UTC api.box.com (ling3)
## 2026-08-18 10:46:07 UTC api.box.com (ling3)
## 2026-08-18 11:05:13 UTC api.box.com (ling3)
## 2026-08-18 11:34:47 UTC api.box.com (ling3)
## 2026-08-18 11:56:10 UTC api.box.com (bigpickle)
[LEARN] REJECTED MISCONFIG @ cloud.app.box.com/: Manual closure confirmed ACAO:* without ACAC; catch-all default handler rejected by 404 on random paths. All 45-rated hypotheses on this host are now evidence-closed.
[LEARN] REJECTED MISCONFIG @ dl.boxcloud.com: Every probe (GET /, robots.txt, favicon, random nonce) returns 404. CDN edge is opaque; no discoverable surface.
[LEARN] REJECTED AUTH @ notes.services.box.com: / returns 302, /health returns 404. No pre-auth surface exposed.
[LEARN] REJECTED OTHER @ m.box.com: 301 → app.box.com. Consolidated; no independent surface.
[PARKED] Object-store default handler vs whitelist-routed on cloud.app.box.com: Random paths all 404; root 200/206 is a pinned asset. Catch-all hypothesis REJECTED with definitive 404 evidence across 10+ nonces. No further read-only probe can change this.
[PARKED] CORS on cloud.app.box.com 206 asset layer: ACAO:* without ACAC confirmed by manual OPTIONS. Not credential-exploitable; no cross-origin read of authenticated content possible.
[PARKED] Method/CORS surface on dl.boxcloud.com: All paths 404. No OPTIONS/GET divergence possible on non-existent routes.
[NEXT] GET cloud.app.box.com/ with Range: bytes=0-0 — check Content-Range header for total size disclosure
[NEXT] OPTIONS cloud.app.box.com/ — confirm 204 + ACAO:* without ACAC one more time for definitiveness
[NEXT] GET https://app.box.com/robots.txt — last untested host surface in scope; check for path disclosure or info leak
## 2026-08-18 12:25:05 UTC api.box.com (ling3)
## 2026-08-18 13:15:37 UTC api.box.com (ling3)
## 2026-08-18 13:58:20 UTC api.box.com (ling3)
## 2026-08-18 14:31:22 UTC api.box.com (ling3)
## 2026-08-18 15:02:55 UTC api.box.com (ling3)
## 2026-08-18 15:39:29 UTC api.box.com (ling3)
## 2026-08-18 16:01:12 UTC api.box.com (ling3)
## 2026-08-18 16:37:58 UTC api.box.com (ling3)
## 2026-08-18 17:01:24 UTC api.box.com (bigpickle)
## 2026-08-18 17:32:40 UTC api.box.com (ling3)
## 2026-08-18 17:56:41 UTC api.box.com (ling3)
## 2026-08-18 18:27:57 UTC api.box.com (ling3)
## 2026-08-18 19:06:31 UTC api.box.com (ling3)
## 2026-08-18 19:35:19 UTC api.box.com (ling3)
## 2026-08-18 19:52:30 UTC api.box.com (ling3)
## 2026-08-18 20:07:56 UTC api.box.com (ling3)
## 2026-08-18 20:36:24 UTC api.box.com (ling3)
## 2026-08-18 20:55:06 UTC api.box.com (ling3)
## 2026-08-18 21:12:48 UTC api.box.com (ling3)
## 2026-08-18 21:36:56 UTC api.box.com (ling3)
## 2026-08-18 21:53:44 UTC api.box.com (ling3)
## 2026-08-18 22:08:49 UTC api.box.com (ling3)
## 2026-08-18 22:37:29 UTC api.box.com (ling3)
## 2026-08-18 22:56:33 UTC api.box.com (ling3)
## 2026-08-18 23:14:44 UTC api.box.com (ling3)
## 2026-08-18 23:37:06 UTC api.box.com (ling3)
## 2026-08-18 23:54:36 UTC api.box.com (ling3)
## 2026-08-19 00:31:39 UTC api.box.com (ling3)
## 2026-08-19 02:03:50 UTC api.box.com (ling3)
## 2026-08-19 03:06:15 UTC api.box.com (ling3)
## 2026-08-19 03:54:43 UTC api.box.com (bigpickle)
## 2026-08-19 04:25:32 UTC api.box.com (ling3)
## 2026-08-19 04:58:53 UTC api.box.com (ling3)
## 2026-08-19 05:29:57 UTC api.box.com (bigpickle)
[LEARN] REJECTED MISCONFIG @ cloud.app.box.com/: Object-store default handler hypothesis REJECTED — root returns 200/206 but all random paths (bhx-xp4k7-m9f3q, ROBOTS.TXT, bhx-p3n7r/robots.txt, robots) return 404. This is a whitelist-routed asset server serving only pinned paths, NOT a catch-all default handler. No further probe can change this.
[PARKED] Object-store default handler vs whitelist-routed: Definitively resolved — whitelist-routed. All 45-rated variants on this asset are now evidence-closed.
[NEXT] GET https://app.box.com/robots.txt — last untested host surface in scope; check for path disclosure, disallowed paths, or info leak
[NEXT] GET https://cloud.app.box.com/ with Range: bytes=0-0 — check Content-Range header for total file size disclosure on the pinned root asset
[HYP] robots.txt information disclosure on app.box.com
class: MISCONFIG
asset: app.box.com/robots.txt
confidence: 25
reasoning: app.box.com is the primary web app and the only host in scope not yet probed for robots.txt. Enterprise apps often leak internal paths, admin endpoints, or API routes in robots.txt disallow directives. If present, this reveals attack surface for further investigation.
verify_steps: GET https://app.box.com/robots.txt — examine body for path disclosures
impact: Information disclosure of internal routes, admin panels, or API endpoints; aids further targeted probing. Low-Medium severity.
testability: PASSIVE
[NEXT] GET https://app.box.com/robots.txt — retrieve and analyze for path disclosures
## 2026-08-19 05:56:39 UTC api.box.com (ling3)
## 2026-08-19 06:32:55 UTC api.box.com (bigpickle)
[LEARN] REJECTED MISCONFIG @ cloud.app.box.com/: Object-store default handler hypothesis definitively REJECTED — random paths all 404, root 200/206 is pinned asset. Whitelist-routed confirmed.
[PARKED] All 45-rated cloud.app.box.com hypotheses: evidence-closed with definitive 404/206/ACAO:* observations.
[NEXT] GET https://app.box.com/robots.txt — retrieve and analyze for path disclosures
[NEXT] GET https://cloud.app.box.com/ with Range: bytes=0-0 — check Content-Range header for total size disclosure on the pinned root asset
## 2026-08-19 07:22:30 UTC api.box.com (ling3)
## 2026-08-19 07:58:13 UTC api.box.com (ling3)
## 2026-08-19 08:34:29 UTC api.box.com (ling3)
## 2026-08-19 09:07:00 UTC api.box.com (ling3)
## 2026-08-19 09:45:10 UTC api.box.com (ling3)
## 2026-08-19 10:04:44 UTC api.box.com (ling3)
## 2026-08-19 10:38:48 UTC api.box.com (ling3)
## 2026-08-19 10:59:36 UTC api.box.com (ling3)
## 2026-08-19 11:26:15 UTC api.box.com (ling3)
## 2026-08-19 11:48:43 UTC api.box.com (ling3)
## 2026-08-19 12:03:33 UTC api.box.com (ling3)
## 2026-08-19 13:02:39 UTC api.box.com (ling3)
## 2026-08-19 13:50:29 UTC api.box.com (ling3)
## 2026-08-19 14:18:58 UTC api.box.com (ling3)
## 2026-08-19 14:55:59 UTC api.box.com (ling3)
## 2026-08-19 15:22:28 UTC api.box.com (ling3)
## 2026-08-19 15:51:59 UTC api.box.com (ling3)
## 2026-08-19 16:15:53 UTC api.box.com (bigpickle)
[NEXT] GET https://app.box.com/robots.txt — check for path disclosure or admin endpoints
[NEXT] GET https://cloud.app.box.com/ with Range: bytes=0-0 — check Content-Range header for total size disclosure
[HYP] app.box.com robots.txt information disclosure
class: MISCONFIG
asset: app.box.com/robots.txt
confidence: 30
reasoning: Enterprise apps often leak internal paths in robots.txt. app.box.com is the primary web app and only host in scope not yet probed for robots.txt.
verify_steps: GET https://app.box.com/robots.txt
impact: Information disclosure of admin panels, API routes, or internal paths; aids further targeted probing
testability: PASSIVE
[HYP] cloud.app.box.com Content-Range size disclosure
class: MISCONFIG
asset: cloud.app.box.com/
confidence: 25
reasoning: The 200/206 responses suggest Range support. Checking Content-Range header might reveal total file size of the pinned asset.
verify_steps: GET https://cloud.app.box.com/ with Range: bytes=0-0
impact: Information disclosure of asset size; low severity but confirms asset characteristics
testability: PASSIVE
## 2026-08-19 16:47:31 UTC api.box.com (bigpickle)
[NEXT] GET https://app.box.com/robots.txt — check for path disclosure or admin endpoints
[NEXT] GET https://cloud.app.box.com/ with Range: bytes=0-0 — check Content-Range header for total size disclosure
[HYP] app.box.com robots.txt information disclosure
class: MISCONFIG
asset: app.box.com/robots.txt
confidence: 30
reasoning: Enterprise apps often leak internal paths in robots.txt. app.box.com is the primary web app and only host in scope not yet probed for robots.txt.
verify_steps: GET https://app.box.com/robots.txt
impact: Information disclosure of admin panels, API routes, or internal paths; aids further targeted probing
testability: PASSIVE
[HYP] cloud.app.box.com Content-Range size disclosure
class: MISCONFIG
asset: cloud.app.box.com/
confidence: 25
reasoning: The 200/206 responses suggest Range support. Checking Content-Range header might reveal total file size of the pinned asset.
verify_steps: GET https://cloud.app.box.com/ with Range: bytes=0-0
impact: Information disclosure of asset size; low severity but confirms asset characteristics
testability: PASSIVE
## 2026-08-19 17:10:35 UTC api.box.com (ling3)
## 2026-08-19 17:38:17 UTC api.box.com (bigpickle)
## 2026-08-19 17:56:17 UTC api.box.com (ling3)
## 2026-08-19 18:19:39 UTC api.box.com (ling3)
## 2026-08-19 18:57:09 UTC api.box.com (ling3)
## 2026-08-19 19:20:37 UTC api.box.com (ling3)
## 2026-08-19 19:44:52 UTC api.box.com (ling3)
## 2026-08-19 20:00:31 UTC api.box.com (ling3)
## 2026-08-19 20:29:29 UTC api.box.com (ling3)
## 2026-08-19 20:55:30 UTC api.box.com (ling3)
## 2026-08-19 21:15:08 UTC api.box.com (ling3)
## 2026-08-19 21:42:06 UTC api.box.com (ling3)
## 2026-08-19 21:59:51 UTC api.box.com (bigpickle)
## 2026-08-19 22:25:16 UTC api.box.com (ling3)
## 2026-08-19 22:48:16 UTC api.box.com (ling3)
## 2026-08-19 23:04:09 UTC api.box.com (ling3)
## 2026-08-19 23:31:50 UTC api.box.com (ling3)
## 2026-08-19 23:49:26 UTC api.box.com (bigpickle)
[NEW] app.box.com/robots.txt: Reveals internal paths including /app-api/split-proxy and /app-gallery-v2/api
[HYP] app.box.com /app-api/split-proxy SSRF potential
class: SSRF
asset: app.box.com/app-api/split-proxy
confidence: 35
reasoning: robots.txt explicitly lists /app-api/split-proxy as an allowed path. "split-proxy" naming suggests an internal proxy service that could be abused for SSRF if it accepts user-controlled URLs or parameters.
verify_steps: GET https://app.box.com/app-api/split-proxy, GET https://app.box.com/app-api/split-proxy/ with various query params
impact: SSRF to internal Box services, potential access to metadata endpoints or internal APIs; HIGH severity
testability: PASSIVE
[HYP] app.box.com /app-gallery-v2/api information disclosure
class: MISCONFIG
asset: app.box.com/app-gallery-v2/api
confidence: 30
reasoning: robots.txt explicitly allows this API path. Gallery APIs often serve metadata about apps/integrations and may leak internal identifiers or configuration without auth.
verify_steps: GET https://app.box.com/app-gallery-v2/api
impact: Information disclosure of internal app gallery data; MEDIUM severity
testability: PASSIVE
[NEXT] GET https://app.box.com/app-api/split-proxy — check proxy endpoint behavior
[NEXT] GET https://app.box.com/app-gallery-v2/api — check API response
## 2026-08-20 00:02:18 UTC api.box.com (ling3)
## 2026-08-20 01:42:26 UTC api.box.com (ling3)
## 2026-08-20 02:42:44 UTC api.box.com (ling3)
## 2026-08-20 03:28:44 UTC api.box.com (ling3)
## 2026-08-20 04:07:55 UTC api.box.com (ling3)
## 2026-08-20 04:49:20 UTC api.box.com (ling3)
## 2026-08-20 05:16:51 UTC api.box.com (ling3)
## 2026-08-20 05:47:51 UTC api.box.com (ling3)
## 2026-08-20 06:18:38 UTC api.box.com (bigpickle)
[NEXT] GET https://app.box.com/app-api/split-proxy — check if proxy endpoint accepts URL parameters or reveals internal routing
[NEXT] GET https://app.box.com/app-gallery-v2/api — check if gallery API exposes metadata without auth
[NEXT] OPTIONS https://cloud.app.box.com/ with Origin: https://evil.com — test CORS preflight reflection
[NEW] app.box.com: robots.txt reveals /app-api/split-proxy and /app-gallery-v2/api paths; both untested
[HYP] app.box.com /app-api/split-proxy SSRF potential
class: SSRF
asset: app.box.com/app-api/split-proxy
confidence: 35
reasoning: robots.txt explicitly allows this path. "split-proxy" naming suggests internal proxy service that could accept user-controlled URLs or parameters for SSRF.
verify_steps: GET https://app.box.com/app-api/split-proxy, GET https://app.box.com/app-api/split-proxy?target=http://169.254.169.254
impact: SSRF to internal Box services, metadata endpoint access; HIGH severity
testability: PASSIVE
[HYP] app.box.com /app-gallery-v2/api information disclosure
class: MISCONFIG
asset: app.box.com/app-gallery-v2/api
confidence: 30
reasoning: robots.txt explicitly allows this API path. Gallery APIs often serve metadata about apps/integrations without auth.
verify_steps: GET https://app.box.com/app-gallery-v2/api
impact: Information disclosure of internal app gallery data; MEDIUM severity
testability: PASSIVE
[HYP] cloud.app.box.com CORS misconfiguration
class: MISCONFIG
asset: cloud.app.box.com/
confidence: 45
reasoning: 206 responses on root suggest asset layer. Testing if Access-Control-Allow-Origin reflects attacker Origin or exposes Authorization header.
verify_steps: OPTIONS https://cloud.app.box.com/ with Origin: https://evil.com, GET https://cloud.app.box.com/ with Origin: https://evil.com
impact: Cross-origin data theft from preview assets; HIGH severity
testability: PASSIVE
[NEXT] GET https://app.box.com/app-api/split-proxy — check proxy endpoint behavior
[NEXT] GET https://app.box.com/app-gallery-v2/api — check API response
[NEXT] OPTIONS https://cloud.app.box.com/ with Origin: https://evil.com — test CORS preflight reflection
## 2026-08-20 07:09:34 UTC api.box.com (ling3)
## 2026-08-20 07:53:37 UTC api.box.com (ling3)
## 2026-08-20 08:20:07 UTC api.box.com (ling3)
## 2026-08-20 08:59:50 UTC api.box.com (ling3)
## 2026-08-20 09:36:47 UTC api.box.com (ling3)
## 2026-08-20 10:03:53 UTC api.box.com (ling3)
## 2026-08-20 10:40:03 UTC api.box.com (ling3)
## 2026-08-20 11:02:08 UTC api.box.com (ling3)
## 2026-08-20 11:34:44 UTC api.box.com (ling3)
## 2026-08-20 11:56:18 UTC api.box.com (ling3)
## 2026-08-20 12:31:56 UTC api.box.com (ling3)
## 2026-08-20 13:28:53 UTC api.box.com (ling3)
## 2026-08-20 14:08:23 UTC api.box.com (ling3)
## 2026-08-20 14:51:17 UTC api.box.com (ling3)
## 2026-08-20 15:19:41 UTC api.box.com (ling3)
## 2026-08-20 15:52:13 UTC api.box.com (ling3)
## 2026-08-20 16:17:01 UTC api.box.com (ling3)
## 2026-08-20 16:53:02 UTC api.box.com (ling3)
## 2026-08-20 17:15:56 UTC api.box.com (ling3)
## 2026-08-20 17:47:00 UTC api.box.com (ling3)
## 2026-08-20 18:13:56 UTC api.box.com (bigpickle)
## 2026-08-20 18:59:23 UTC api.box.com (ling3)
## 2026-08-20 19:32:01 UTC api.box.com (ling3)
## 2026-08-20 19:57:37 UTC api.box.com (ling3)
## 2026-08-20 20:21:42 UTC api.box.com (ling3)
## 2026-08-20 20:51:46 UTC api.box.com (ling3)
## 2026-08-20 21:14:04 UTC api.box.com (ling3)
## 2026-08-20 21:43:53 UTC api.box.com (ling3)
## 2026-08-20 22:01:28 UTC api.box.com (ling3)
## 2026-08-20 22:34:32 UTC api.box.com (ling3)
## 2026-08-20 22:58:31 UTC api.box.com (ling3)
## 2026-08-20 23:23:32 UTC api.box.com (ling3)
## 2026-08-20 23:45:22 UTC api.box.com (ling3)
## 2026-08-21 00:00:54 UTC api.box.com (ling3)
## 2026-08-21 01:34:47 UTC api.box.com (ling3)
## 2026-08-21 02:48:06 UTC api.box.com (ling3)
## 2026-08-21 03:36:58 UTC api.box.com (ling3)
## 2026-08-21 04:20:12 UTC api.box.com (ling3)
## 2026-08-21 04:58:12 UTC api.box.com (ling3)
## 2026-08-21 05:30:30 UTC api.box.com (ling3)
## 2026-08-21 05:59:02 UTC api.box.com (ling3)
## 2026-08-21 06:45:34 UTC api.box.com (ling3)
## 2026-08-21 07:29:44 UTC api.box.com (ling3)
## 2026-08-21 08:06:00 UTC api.box.com (ling3)
## 2026-08-21 08:53:40 UTC api.box.com (ling3)
## 2026-08-21 09:22:06 UTC api.box.com (ling3)
## 2026-08-21 09:56:07 UTC api.box.com (ling3)
## 2026-08-21 10:20:56 UTC api.box.com (ling3)
## 2026-08-21 10:53:18 UTC api.box.com (bigpickle)
## 2026-08-21 11:13:18 UTC api.box.com (ling3)
## 2026-08-21 11:42:06 UTC api.box.com (ling3)
## 2026-08-21 11:59:39 UTC api.box.com (ling3)
## 2026-08-21 12:50:46 UTC api.box.com (ling3)
## 2026-08-21 13:34:28 UTC api.box.com (ling3)
## 2026-08-21 14:09:17 UTC api.box.com (ling3)
## 2026-08-21 14:50:43 UTC api.box.com (ling3)
## 2026-08-21 15:17:57 UTC api.box.com (ling3)
## 2026-08-21 15:50:53 UTC api.box.com (ling3)
## 2026-08-21 16:15:20 UTC api.box.com (ling3)
## 2026-08-21 16:52:00 UTC api.box.com (ling3)
## 2026-08-21 17:15:55 UTC api.box.com (ling3)
## 2026-08-21 17:47:18 UTC api.box.com (ling3)
## 2026-08-21 18:13:54 UTC api.box.com (ling3)
## 2026-08-21 18:47:25 UTC api.box.com (mimo)
[PARKED] CORS misconfiguration on cloud.app.box.com preview origin: Infrastructure limitation confirmed — executor returns status codes only, never response headers; 10+ cycles emitted Origin-bearing probes but no Access-Control-* values were ever captured. Cannot confirm or refute. Frozen at confidence 40.
[PARKED] S3-compatible bucket-listing on cloud.app.box.com `?list-type=2`: GET /?list-type=2 returns 200 (sometimes 206) across 5+ cycles but response body never captured; status-only probe cannot distinguish S3 XML listing from SPA shell. Frozen at confidence 50.
[LEARN] ACCEPTED OTHER @ cloud.app.box.com: Pinned-asset whitelist now confirmed — robots.txt (206), favicon.ico (206), crossdomain.xml (206), sitemap.xml (206), embed (206), root (200/206). Nonces → 404. Case-sensitive, single-decode normalization, query-agnostic. Route map complete for known assets.
[LEARN] REJECTEDTOOL @ cloud.app.box.com: All CORS/header-dependent hypotheses — executor never returns response headers; every hypothesis requiring Access-Control-* evidence is frozen. Cannot advance without header-capturing infrastructure.
[HYP] Extend pinned-asset whitelist — test additional well-known / Box-specific paths on the 206 Range layer
class: OTHER
asset: cloud.app.box.com/{favicon.svg,embed.html,preview.html,api/embed,box-icon.png,_debug,health}
confidence: 35
reasoning: Six confirmed pinned assets exist (root, robots.txt, favicon.ico, crossdomain.xml, sitemap.xml, embed). The whitelist may include additional Box-specific assets (embed.html, preview bootstrap, debug/health endpoints) that are reachable and could reveal configuration or version info. All prior path probing was limited to a small set; broader enumeration is cheap and non-destructive.
verify_steps: 7xGET with UA "box-research/1.0 +(research)", pause 2s between, stop on first 403/429 (404/206 are route facts): (1) /favicon.svg (2) /embed.html (3) /preview.html (4) /api/embed (5) /box-icon.png (6) /_debug (7) /health — record status per path.
impact: Recon-grade — any new 206 route becomes a candidate for content/Range-handling review; _debug/health could leak version info. Low severity unless a route exposes config.
testability: PASSIVE
[NEXT] PROBE: 7xGET with UA "box-research/1.0 +(research)", 2s apart, stop on 403/429:
## 2026-08-21 18:56:15 UTC api.box.com (mimo)
[NEW] dl.boxcloud.com: 404 on all probed paths (/, /robots.txt, /favicon.ico) — download edge is inert or gated by signed tokens only.
[HYP] Extend pinned-asset whitelist — test additional Box-specific paths on cloud.app.box.com 206 layer
class: OTHER
asset: cloud.app.box.com/{favicon.svg,embed.html,preview.html,api/embed,box-icon.png,_debug,health}
confidence: 35
reasoning: Six confirmed pinned assets exist (root, robots.txt, favicon.ico, crossdomain.xml, sitemap.xml, embed). Broader enumeration of Box-specific well-known paths is cheap and may reveal additional routable endpoints with configuration/version data.
verify_steps: 7xGET with UA "box-research/1.0 +(research)", 2s apart, stop on first 403/429: (1) /favicon.svg (2) /embed.html (3) /preview.html (4) /api/embed (5) /box-icon.png (6) /_debug (7) /health — record status per path.
impact: Recon-grade — new routes become candidates for content review; _debug/health could leak version info.
testability: PASSIVE
[NEXT] PROBE: 7xGET cloud.app.box.com with UA "box-research/1.0 +(research)", 2s pause between, stop on 403/429:
## 2026-08-21 19:19:58 UTC api.box.com (mimo)
## 2026-08-21 19:45:43 UTC api.box.com (mimo)
[LEARN] ACCEPTED OTHER @ cloud.app.box.com: The 206/Range layer is strictly whitelist-routed. Only 6 assets respond: root, robots.txt, favicon.ico, crossdomain.xml, sitemap.xml, embed. Everything else returns 404. Random paths, Box-specific filenames, and config files are all rejected.
[NEXT] PROBE: PIVOT to account.box.com — test auth-plane surface with 7 probes (2s apart, stop on 403/429):
[NEW] account.box.com: /robots.txt returned 200 with detailed path map — /app-api/split-proxy, /app-gallery-v2/api, /enduser, /s/, /shared/, /v/, /integrations, /app-center all crawlable
[NEW] account.box.com: /api/oauth2/authorize returned 200 with prefetched config embedded in HTML — leaks amplitudeAPIKey, splitIOAPIKey, pendoAnalyticsAPIKey, preview version 3.79.0, runtimeMetadata (availabilityZone: us-west4-prod, deploymentType: k8s), CDN bundle manifest with 20+ micro-frontend versions
[NEW] account.box.com: /sso → 302 → /login?redirect_url=%2Fsso (SSO passthrough to login)
class: MISCONFIG
asset: account.box.com/api/oauth2/authorize
confidence: 25
reasoning: The unauthenticated OAuth2 authorize page embeds `runtimeMetadata` (availabilityZone: us-west4-prod, deploymentType: k8s, environment: prod), `datacenterTag`, and three third-party analytics SDK keys in a `Box.prefetchedData` JSON block within the HTML. While SDK keys are client-side by design, the exact datacenter/deployment metadata is operational detail not required by the frontend. The /api/oauth2/token endpoint (400) and /api/oauth2/revoke (302) also exist but are gated.
verify_steps: 3xGET with UA "box-research/1.0 +(research)", 2s apart: (1) /api/oauth2/authorize (compare requestId with prior cycle) (2) /login (check for same prefetchedData structure) (3) /enduser (check for same prefetchedData structure).
impact: Low — operational metadata disclosure (datacenter, k8s deployment) could aid targeted infrastructure attacks but is marginal. SDK keys are designed for public embedding. No authentication bypass or data exfiltration path.
testability: PASSIVE
class: MISCONFIG
asset: account.box.com/api/oauth2/authorize
confidence: 15
reasoning: The endpoint returns 200 with `error: "invalid_client"` regardless of query parameters (no client_id, invalid client_id, with redirect_uri). No differentiation in error messages. The /api/oauth2/token returns 400 without body inspection. If valid client_id values produce different error messages (e.g., "unauthorized_client" vs "invalid_client"), this could enumerate valid OAuth app IDs.
verify_steps: 3xGET with UA "box-research/1.0 +(research)", 2s apart: (1) /api/oauth2/authorize?response_type=code&client_id=known_valid_from_docs (2) /api/oauth2/token with POST-like GET (no body) to observe error (3) /api/oauth2/revoke to observe redirect target.
impact: Low — OAuth error messages typically don't enumerate valid client IDs, but if they do, it could reveal registered OAuth applications.
testability: PASSIVE
## 2026-08-21 19:56:57 UTC api.box.com (mimo)
[CONFIRMED] cloud.app.box.com 206/Range layer is strictly whitelist-routed — only root, robots.txt, favicon.ico, crossdomain.xml, sitemap.xml, embed respond with 206; all other paths (random nonces, case variations, Box-specific filenames) return 404. Whitelist is case-sensitive, single-decode, query-agnostic.
[CONFIRMED] dl.boxcloud.com is inert — root, robots.txt, favicon.ico, random nonce all return 404. Download edge requires signed tokens; no unauthenticated surface.
[CONFIRMED] upload.box.com has no unauthenticated surface — robots.txt and API path return 404. Upload pipeline is gated.
[LEARN] REJECTEDTOOL @ cloud.app.box.com: CORS/header-dependent hypotheses frozen — executor never returns response headers. Cannot advance without header-capturing infrastructure.
[HYP] Extend account.box.com metadata leak to /login and /enduser endpoints
class: MISCONFIG
asset: account.box.com/{/login,/enduser}
confidence: 20
reasoning: /api/oauth2/authorize leaks runtimeMetadata (availabilityZone: us-west4-prod, deploymentType: k8s, datacenterTag) and third-party SDK keys in Box.prefetchedData. If /login and /enduser embed the same prefetchedData block, the metadata surface doubles. These endpoints are unauthenticated and may expose additional deployment details or version strings.
verify_steps: 3xGET with UA "box-research/1.0 +(research)", 2s apart, stop on first 403/429: (1) GET /login (2) GET /enduser (3) GET /integrations — check each for Box.prefetchedData JSON block; if present, note runtimeMetadata keys.
impact: Low — operational metadata disclosure; could reveal infrastructure specifics for targeted attacks but no auth bypass.
testability: PASSIVE
[NEXT] PROBE: 3xGET account.box.com with UA "box-research/1.0 +(research)", 2s pause between, stop on 403/429:
## 2026-08-21 20:18:33 UTC api.box.com (mimo)
## 2026-08-21 20:43:44 UTC api.box.com (mimo)
[LEARN] ACCEPTED OTHER @ account.box.com: /robots.txt reveals crawlable paths including /app-api/split-proxy, /app-gallery-v2/api, /enduser, /s/, /shared/, /v/, /integrations. /api/oauth2/authorize embeds Box.prefetchedData with runtimeMetadata (availabilityZone: us-west4-prod, deploymentType: k8s) and third-party SDK keys.
[HYP] m.box.com mobile web app has distinct auth/session surface
class: OTHER
asset: m.box.com
confidence: 30
reasoning: m.box.com is described as having "separate rendering/session logic" from app.box.com. Mobile web apps often implement different session handling, may bypass certain security controls, or expose different API surface. No probes have been conducted on this host yet.
verify_steps: 3xGET with UA "box-research/1.0 +(research)", 2s apart, stop on first 403/429: (1) / (2) /robots.txt (3) /login — record status and note any redirect behavior.
impact: Recon-grade — discovering session/auth differences or mobile-specific API surface could reveal bypass opportunities or token handling flaws.
testability: PASSIVE
[NEXT] PROBE: 3xGET m.box.com with UA "box-research/1.0 +(research)", 2s pause between, stop on 403/429: (1) / (2) /robots.txt (3) /login
## 2026-08-21 21:01:03 UTC api.box.com (mimo)
class: MISCONFIG
asset: account.box.com/api/oauth2/authorize
confidence: 20
reasoning: The `runtimeMetadata` (availabilityZone, deploymentType, datacenterTag) leak is confined to `/api/oauth2/authorize`. `/login` and `/enduser` do not embed this data. The metadata is operational detail but not credential-bearing. SDK keys are client-side by design.
verify_steps: Confirmed in prior cycles — no further probing needed
impact: Low — operational metadata disclosure only; no auth bypass or data exfiltration path
testability: PASSIVE
## 2026-08-21 21:27:09 UTC api.box.com (mimo)
## 2026-08-21 21:47:36 UTC api.box.com (mimo)
[NEW] m.box.com: Root returns 200 with login page; /robots.txt reveals 18 crawlable paths identical to account.box.com including /app-api/split-proxy, /app-gallery-v2/api
[HYP] m.box.com mobile web app mirrors account.box.com surface
class: OTHER
asset: m.box.com
confidence: 40
reasoning: m.box.com robots.txt reveals identical path structure to account.box.com including /app-api/split-proxy and /app-gallery-v2/api. The root and /login both serve the same login page. This suggests m.box.com is either a CNAME alias or thin proxy to account.box.com, not a distinct mobile rendering layer. No unique mobile-specific API surface discovered.
verify_steps: 3xGET with UA "box-research/1.0 +(research)", 2s apart: (1) m.box.com/api/oauth2/authorize (2) m.box.com/app-api/split-proxy (3) m.box.com/enduser — record status and check for Box.prefetchedData
impact: Low — if identical to account.box.com, no new attack surface; if different, could reveal mobile-specific auth handling
testability: PASSIVE
[NEXT] PROBE: 3xGET m.box.com with UA "box-research/1.0 +(research)", 2s pause between, stop on 403/429: (1) /api/oauth2/authorize (2) /app-api/split-proxy (3) /enduser
## 2026-08-21 22:02:24 UTC api.box.com (mimo)
[NEW] notes.services.box.com: New surface — Box Notes backend with real-time collaboration and content sync, not yet probed
[HYP] notes.services box.com real-time collaboration surface
class: OTHER
asset: notes.services.box.com
confidence: 35
reasoning: notes.services.box.com is described as "Box Notes backend; real-time collaboration, content sync" — distinct from the primary web app and API. Real-time collaboration often involves WebSockets, long-polling, or Server-Sent Events, which may have different authentication, CORS, or session handling than standard HTTP endpoints. No probes have been conducted on this host yet.
verify_steps: 3xGET with UA "box-research/1.0 +(research)", 2s apart, stop on first 403/429: (1) / (2) /robots.txt (3) /api/ — record status codes and note any WebSocket upgrade headers, collaboration-specific paths, or authentication requirements
impact: Recon-grade — discovering real-time collaboration endpoints could reveal session handling flaws, CORS misconfigurations, or authentication bypass opportunities in the Notes feature
testability: PASSIVE
[NEXT] PROBE: 3xGET notes.services.box.com with UA "box-research/1.0 +(research)", 2s pause between, stop on 403/429: (1) / (2) /robots.txt (3) /api/
## 2026-08-21 22:32:23 UTC api.box.com (mimo)
[NEW] notes.services.box.com: Box Notes backend with real-time collaboration — unexplored surface
[HYP] notes.services.box.com WebSocket/real-time collaboration bypass
class: AUTH
asset: notes.services.box.com
confidence: 40
reasoning: Real-time collaboration systems often use WebSockets or Server-Sent Events with different authentication mechanisms than standard HTTP APIs. If the Notes backend implements its own session handling separate from the main Box auth, there could be authentication bypass or session fixation vulnerabilities. The "content sync" functionality may have insecure direct object references or improper access controls.
verify_steps: 3xGET with UA "box-research/1.0 +(research)", 2s apart: (1) / with Connection: Upgrade, Upgrade: websocket header (2) /api/collaboration (3) /api/notes - check for authentication requirements and session tokens
impact: High — if real-time collaboration bypasses standard auth, could allow unauthorized access to Box Notes content
testability: AUTH_HELPED
[NEXT] PROBE: 3xGET notes.services.box.com with UA "box-research/1.0 +(research)", 2s pause between, stop on 403/429: (1) /api/realtime (2) /api/collaboration (3) /api/notes/sessions
[LEARN] ACCEPTED OTHER @ notes.services.box.com: New unexplored surface with distinct architecture from main Box platform — real-time collaboration may have separate auth/session handling
## 2026-08-21 22:58:52 UTC api.box.com (mimo)
[PARKED] notes.services.box.com WebSocket/real-time collaboration bypass: Root 302s to services.app.box.com (out of scope), all probed API paths (/api/realtime, /api/collaboration, /api/notes/sessions, /api/1.0/notes) return 404. Express.js backend behind Envoy with CSRF cookies and frame-ancestors CSP — no unauthenticated surface discovered.
[LEARN] REJECTED AUTH @ notes.services.box.com: Host redirects root to out-of-scope origin (services.app.box.com); all API paths 404. No unauthenticated collaboration surface exists.
[PARKED] notes.services.box.com real-time collaboration surface: Host redirects to out-of-scope origin, no exposed API surface.
[NEW] app.box.com: Primary web app with same path structure as account.box.com plus /app-center, /platform, /representation, /app-assets. CDN shared with marketing site.
[NEW] app.box.com/app-center: Full SPA with embedded Box.config and Box.prefetchedData — requestToken, Zuora billing URL, Pendo/Amplitude/Split.io API keys, runtime metadata, feature flags, micro-frontend versions.
[HYP] app-box.com/app-center embedded API key abuse
class: MISCONFIG
asset: app.box.com/app-center
confidence: 55
reasoning: /app-center serves the full Box SPA unauthenticated, embedding third-party API keys (Amplitude: c6eb3d709c5c30ca80c0381080bcc254, Split.io: 3sd5ltupa3cq5t3ovm1r2kear6i4kvmeb42a, Pendo: 123e0628-cb29-41d1-466c-c592c7ddcd06), Zuora billing endpoint, runtime metadata (us-west4-prod, k8s), feature flags, and preview token timeout (3600s). These keys are client-side by design, but the Zuora billing URL and requestToken embedded in the anonymous page are notable — requestToken could be tested for session fixation or CSRF bypass on unauthenticated endpoints.
verify_steps: 3xGET with UA "box-research/1.0 +(research)", 2s apart: (1) app.box.com/platform (2) app.box.com/integrations (3) app.box.com/enduser — extract Box.config from each and compare requestToken, API keys, feature flags
impact: Low-Medium — third-party SDK keys are client-side by design; however, Zuora billing endpoint exposure and requestToken reuse across anonymous sessions could enable billing enumeration or CSRF if token is reusable
testability: PASSIVE
[HYP] app.box.com CDN preflight/GET CORS mismatch
class: MISCONFIG
asset: app.box.com/app-api/*
confidence: 35
reasoning: The Envoy/CDN layer returns `Access-Control-Allow-Origin: *` with `Access-Control-Allow-Headers: Authorization` on OPTIONS preflight for all `/app-api/*` paths. However, the actual GET responses from the Express origin do NOT include CORS headers. This is a configuration mismatch between CDN and origin. Currently not exploitable because browser blocks cross-origin reads. If the origin ever adds CORS headers (e.g., for a new feature), the overly permissive preflight would immediately enable cross-origin data theft of the current-user object and any authenticated API responses.
verify_steps: 3xGET with UA "box-research/1.0 +(research)", 2s apart: (1) OPTIONS + GET on /app-api/enduserapp/current-user checking CORS headers (2) OPTIONS + GET on /app-api/split-proxy (3) OPTIONS + GET on /app-gallery-v2/api — confirm GET never reflects Origin
impact: Low — current misconfiguration is not exploitable, but indicates fragile CORS posture that could become dangerous if origin adds CORS headers
testability: PASSIVE
[HYP] app.box.com version string disclosure
class: OTHER
asset: app.box.com/app-api/enduserapp/current-user
confidence: 85
reasoning: The `x-enduserapp-currentversion: 23.692.0+hotfix.1` response header discloses the exact enduserapp version including hotfix suffix. Combined with the SPA-embedded version strings (preview 3.79.0, 20+ micro-frontend versions from Box.prefetchedData), an attacker can fingerprint the exact deployment and identify known vulnerabilities.
verify_steps: Already confirmed — no further probing needed
impact: Low — version disclosure aids targeted attacks but is informational only
testability: PASSIVE
[NEXT] PROBE: 3xGET cloud.app.box.com with UA "box-research/1.0 +(research)", 2s pause between, stop on 403/429: (1) /api/2.0/users/me (2) /api/2.0/folders/0 (3) / — check if the 206 on root is a CDN range-request layer that fronts the origin, or purely static asset serving
## 2026-08-21 23:15:15 UTC api.box.com (mimo)
[NEW] cloud.app.box.com/graphql: GraphQL endpoint exists (returns Box API error JSON, 503 Service Unavailable without auth) on both cloud.app.box.com and api.box.com — same backend
[NEW] cloud.app.box.com/embed/config: Unauthenticated endpoint returns full feature flag map, embed context, partnerId field, and 20+ micro-frontend version strings (1.4.4 embed-widget)
[NEW] cloud.app.box.com/config: Unauthenticated full SPA with Box.prefetchedData including requestToken (per-session, not reusable as Bearer), realtimeSubscriberID, Amplitude/Split.io/Pendo API keys, preview version 3.79.0, Zuora billing URL, GraphQL feature flags
[NEW] dl.boxcloud.com/api/2.0/files/{id}/content: File download proxy returns `Access-Control-Allow-Origin: *` with `Access-Control-Expose-Headers: Content-Range` on 401 responses — wildcard CORS on file CDN endpoint
[HYP] dl.boxcloud.com wildcard CORS on file download proxy — fragile auth/CORS posture
class: MISCONFIG
asset: dl.boxcloud.com/api/2.0/files/*/content
confidence: 50
reasoning: dl.boxcloud.com returns Access-Control-Allow-Origin: * with Access-Control-Expose-Headers: Content-Range on unauthenticated 401 responses. The OPTIONS preflight returns 204 with Access-Control-Allow-Origin: * but does NOT include Access-Control-Allow-Headers: Authorization — so browsers currently cannot send Bearer tokens cross-origin. However, this is a fragile posture: the wildcard CORS on a file download proxy means if Box ever adds cookie-based auth or if a subdomain XSS allows token theft, any website could read file content via JavaScript. The Content-Range exposure enables byte-range enumeration attacks.
verify_steps: Already confirmed via curl. To fully test: (1) curl -D- -H "Origin: https://evil.com" dl.boxcloud.com/api/2.0/files/1/content — confirm ACAO:* on 401; (2) curl -D- -X OPTIONS -H "Origin: https://evil.com" dl.boxcloud.com/api/2.0/files/1/content — confirm 204 without Allow-Headers; (3) Compare with cloud.app.box.com/api/2.0/files/1/content which has NO CORS headers
impact: Low-Medium — currently not exploitable because Authorization header is not in Allow-Headers on preflight, so browsers block cross-origin credentialed requests. But the wildcard CORS on a file download proxy is a misconfiguration waiting to become critical if auth mechanism changes. Severity upgradeable if any subdomain XSS or cookie injection is found.
testability: PASSIVE
[HYP] cloud.app.box.com/embed/config feature flag + version enumeration
class: OTHER
asset: cloud.app.box.com/embed/config
confidence: 90
reasoning: /embed/config is an unauthenticated endpoint that returns the complete feature flag configuration for anonymous embed contexts (embed:true, canvas:false, sign:false, shield:false, etc.), plus all 20+ micro-frontend version strings from Box.webpackRemotesManifest. This allows attackers to fingerprint the exact deployment version of every component and identify known vulnerabilities in specific versions. The partnerId field in the response is server-controlled (not reflected from user input).
verify_steps: Already confirmed — /embed/config returns 200 with full JSON config including version strings
impact: Low — version enumeration aids targeted exploitation but is informational only
testability: PASSIVE
[HYP] cloud.app.box.com GraphQL introspection/auth bypass
class: AUTH
asset: cloud.app.box.com/graphql
confidence: 25
reasoning: The /graphql endpoint exists and returns a proper Box API error JSON (not 404) on both cloud.app.box.com and api.box.com. It returns 503 Service Unavailable without auth, suggesting the GraphQL service requires authentication to route requests. The SPA feature flags reference euaRootFolderGraphql, euaSubfolderGraphql, and euaFolderPreflightScript — indicating a GraphQL API for folder operations. If authentication can be bypassed, GraphQL introspection could expose the entire data schema.
verify_steps: Requires AUTH_HELPED — cannot test further without valid Bearer token
impact: High (if auth bypass possible) — full schema disclosure and potentially unauthorized data access via GraphQL
testability: AUTH_HELPED
[PARKED] m.box.com unique surface: All paths 301 redirect to app.box.com — confirmed as pure redirect layer with no unique rendering/session logic
[PARKED] upload.box.com API surface: All paths return404 with nginx "Not found" — internal upload service with no unauthenticated surface
[LEARN] CONFIRMED OTHER @ dl.boxcloud.com: Download proxy has wildcard CORS (ACAO:*) but preflight does NOT allow Authorization header — fragile but not currently exploitable
[LEARN] CONFIRMED OTHER @ cloud.app.box.com: Full SPA + API proxy mirrors api.box.com behavior; no CORS headers on API; requestToken is per-session and not valid as Bearer
[LEARN] CONFIRMED OTHER @ m.box.com: Pure redirect layer to app.box.com — no unique surface
[LEARN] CONFIRMED OTHER @ upload.box.com: Internal service, no unauthenticated surface
[NEXT] PROBE: Focus on cloud.app.box.com — test additional SPA catch-all paths that might expose different data than app.box.com. 3xGET with UA "box-research/1.0 +(research)", 2s pause: (1) cloud.app.box.com/shared/{test-token} (2) cloud.app.box.com/preview/{test-token} (3) cloud.app.box.com/embed/{test-token} — check if these catch-all SPA routes embed different Box.prefetchedData or expose shared-link metadata
## 2026-08-21 23:39:54 UTC api.box.com (mimo)
[NEW] cloud.app.box.com/robots.txt: 200 with crawler rules — reveals allowed paths: `/representation`, `/app-assets`, `/login`, `/simpleshare`; Twitterbot fully blocked
[NEW] cloud.app.box.com/simpleshare: 301 redirect to `http://sites.box.com/apps/web/simpleshare/` — HTTP (not HTTPS) downgrade to external sites.box.com subdomain
[NEW] cloud.app.box.com/login: 301 redirect to `cloud.account.box.com/login` — cross-domain auth portal discovery
[NEW] cloud.app.box.com/embed/*: Catch-all SPA route — returns 200 with full embed widget and Box.prefetchedData for ANY path segment; user input URL-encoded in JSON (safe)
[NEW] cloud.app.box.com/folder/*, /file/*, /collections: All 302 redirect to `cloud.account.box.com/login?redirect_url=<path>` — cross-domain login redirect chain
[NEW] cloud.account.box.com: NEW SUBDOMAIN discovered via redirect chain — distinct from account.box.com; serves as auth portal for cloud.app.box.com
[NEW] cloud.app.box.com OPTIONS on /app-api/*: Returns `Access-Control-Allow-Origin: *` with `Access-Control-Allow-Methods: DELETE, HEAD, GET, OPTIONS, POST, PUT` — wildcard CORS preflight with ALL write methods on API endpoints
[HYP] cloud.app.box.com CORS preflight/response mismatch — wildcard ACAO:* on OPTIONS but no CORS headers on GET responses
class: MISCONFIG
asset: cloud.app.box.com (all paths)
confidence: 60
reasoning: OPTIONS preflight returns ACAO:* with DELETE/POST/PUT allowed on all paths including /app-api/enduserapp/current-user. However, actual GET responses include NO CORS headers. This CDN/origin mismatch means the wildcard preflight is non-functional today. If the origin ever adds CORS headers (e.g., for a new API feature), the overly permissive preflight would immediately enable cross-origin data theft of the full anonymous user object and all unauthenticated API responses.
verify_steps: 3xGET with Origin: https://evil.com, UA "box-research/1.0 +(research)", 2s apart: (1) /app-api/enduserapp/current-user (2) /app-api/split-proxy (3) /app-gallery-v2/api — confirm no ACAO header in any response
impact: Low-Medium — not currently exploitable, but fragile posture indicates future risk if CORS is added to origin responses. Severity upgradeable if any subdomain XSS or cookie injection is found.
testability: PASSIVE
[HYP] cloud.app.box.com /embed/ URL reflection in JSON context — potential DOM XSS vector
class: XSS
asset: cloud.app.box.com/embed/{user-input}
confidence: 30
reasoning: User-controlled path segment is reflected inside Box.prefetchedData.url, displayedURL, originalURL, and message JSON fields within a <script> block. The server URL-encodes the input (e.g., %22 for ", %3C for <), keeping it safe inside the JSON string. However, the embed.js widget from cdn01.boxcdn.net parses this JSON and may process the URL for rendering. If the client-side code decodes the URL-encoded values before DOM insertion, DOM XSS could be possible.
verify_steps: Fetch https://cdn01.boxcdn.net/embed-widget/1.4.4/embed.js and search for prefetchedData URL handling
impact: Medium — if client-side decoding enables XSS, attacker could execute JS in embed context (limited by CSP/same-origin)
testability: PASSIVE
[PARKED] cloud.app.box.com /representation path: 404 with x-robots-tag: noindex — listed in robots.txt Allow but returns empty; likely requires query params or specific file IDs
[PARKED] cloud.app.box.com /simpleshare HTTP downgrade: 301 to http://sites.box.com — HTTP downgrade is a low-severity finding on an external subdomain not in scope
[PARKED] cloud.account.box.com login redirect: redirect_url parameter is server-controlled (not reflected from user input) — no open redirect vector found in read-only testing
[LEARN] CONFIRMED MISCONFIG @ cloud.app.box.com: OPTIONS returns ACAO:* with all methods on all paths, but GET responses never include CORS headers — CDN/origin mismatch confirmed
[LEARN] CONFIRMED OTHER @ cloud.app.box.com /embed/*: User input is URL-encoded in JSON context, not raw — no direct XSS via HTML injection; DOM XSS depends on client-side handling
[LEARN] ACCEPTED OTHER @ cloud.app.box.com /folder/0, /file/1, /collections: 302 redirect to cloud.account.box.com/login — redirect_url is server-controlled, no user input reflection
[NEXT] PROBE: Read embed.js client-side code to test DOM XSS hypothesis. 3xGET with UA "box-research/1.0 +(research)", 2s apart: (1) https://cdn01.boxcdn.net/embed-widget/1.4.4/embed.js — check for URL decoding and DOM insertion of prefetchedData (2) cloud.app.box.com/embed/test-token — re-confirm linkedRequestId uniqueness (3) cloud.app.box.com/representation/0 — test if /representation is a file preview path that needs a file ID
## 2026-08-21 23:57:01 UTC api.box.com (mimo)
[CONFIRMED] cloud.app.box.com CORS preflight/response mismatch
class: MISCONFIG
asset: cloud.app.box.com (all paths)
confidence: 80
reasoning: Directly confirmed: OPTIONS on /app-api/enduserapp/current-user returns ACAO:* with DELETE/POST/PUT allowed. GET response has NO CORS headers (no ACAO). Split-proxy 404 path DOES return ACAO:* on GET (CDN layer). The origin proxy adds CORS to OPTIONS but NOT to proxied API GET responses — a CDN/origin configuration mismatch.
verify_steps: Already confirmed via curl with Origin: https://evil.com header on OPTIONS and GET to /app-api/enduserapp/current-user and /app-api/split-proxy
impact: Low — not currently exploitable because GET responses lack CORS headers, blocking browser cross-origin reads. However, the wildcard ACAO:* preflight with ALL write methods (DELETE/POST/PUT) is an over-permissive policy that becomes critical if any origin endpoint ever adds CORS headers to GET responses.
testability: PASSIVE
[NEW] dl.boxcloud.com/api/2.0/files/1/content: 401 with ACAO:* + Access-Control-Expose-Headers including Content-Range — confirmed wildcard CORS on actual file download proxy responses (not just preflight)
[CONFIRMED] dl.boxcloud.com wildcard CORS on download proxy
class: MISCONFIG
asset: dl.boxcloud.com/api/2.0/files/*/content
confidence: 75
reasoning: GET to /api/2.0/files/1/content returns 401 with access-control-allow-origin: *, access-control-expose-headers: Accept-Ranges,Content-Encoding,Content-Length,Content-Range,Date,Retry-After. The 401 response body is empty (no auth token = no data). The ACAO:* + Content-Range exposure on a file download proxy means any authenticated file download response (if a browser can obtain a valid token) would be fully readable cross-origin.
verify_steps: Already confirmed via curl. Critical question: does a VALID token response also include ACAO: *?
impact: Medium — if a valid Bearer token can be obtained (e.g., via session hijacking, XSS on a Box subdomain), the wildcard CORS allows full cross-origin file content exfiltration including byte-range enumeration via Content-Range.
testability: AUTH_HELPED
[NEW] cloud.account.box.com/login: Login page has x-frame-options: SAMEORIGIN, referrer-policy: no-referrer, x-xss-protection: 0 — strong defensive headers. redirect_url parameter accepted but not reflected in redirect — no open redirect vector found.
[PARKED] Embed.js DOM XSS via redirectParentIframe postMessage: Origin check validates against box.com/boxcdn.net/boxenterprise.net/inside-box.net/boxgov.us whitelist. The redirectParentIframe action does window.location.href=n.url from postMessage data, but only processes messages from whitelisted Box origins. Cannot exploit without first compromising a Box subdomain.
testability: AUTH_HELPED (requires subdomain compromise)
[PARKED] cloud.app.box.com /shared/* and /preview/* paths: Both return 404 — not valid SPA catch-all routes. Only /embed/* catches all paths.
[LEARN] CONFIRMED OTHER @ cloud.app.box.com /embed/*: embed.js postMessage handler origin check regex: `^(?:https?:\/\/)?(?:([a-zA-Z0-9\-_]+)\.)*box\.com|boxcdn\.net|boxenterprise\.net|inside-box\.net|boxgov\.us` — subdomain-aware validation with subdomain capture group check via `c.current` ref
[LEARN] CONFIRMED OTHER @ cloud.app.box.com: /embed/* response includes server-constructed message JSON with `action: "retrieveEmbedStateResult"` containing 80+ feature flags and embed context. User input only in url/displayedURL/originalURL fields, all URL-encoded.
[LEARN] CONFIRMED OTHER @ cloud.account.box.com/login: Strong security headers (X-Frame-Options: SAMEORIGIN, referrer-policy: no-referrer, x-xss-protection: 0). No open redirect on redirect_url parameter.
[NEXT] PROBE: Test if dl.boxcloud.com returns ACAO:* on successful (token-bearing) file download responses, and explore cloud.app.box.com /app-api/ for additional version/header leaks. 3xGET with UA "box-research/1.0 +(research)", 2s apart: (1) dl.boxcloud.com/api/2.0/folders/0 — test folder listing CORS (2) cloud.app.box.com/app-api/enduserapp/app-version — test for version endpoint (3) cloud.app.box.com/webapp/_rightsidebar — test for sidebar API endpoint with different response format
## 2026-08-22 00:27:45 UTC api.box.com (mimo)
[NEW] cloud.app.box.com /app-api/enduserapp/app-version: Unauthenticated 200 with full micro-frontend version manifest — 24 remote modules with exact versions, CDN paths, and build hashes
[NEW] cloud.app.box.com /app-api/enduserapp/current-user: Unauthenticated 200 with Amplitude API key (`c6eb3d709c5c30ca80c0381080bcc254`), Split.io API key (`3sd5ltupa3cq5t3ovm1r2kear6i4kvmeb42a`), full feature flag matrix (100+ flags), infrastructure URLs, preview config, and user model schema
[NEW] cloud.app.box.com /app-api/enduserapp/folder/0: 401 (auth required) — confirms some endpoints are auth-gated but current-user and app-version are not
[HYP] Unauthenticated API key and feature flag leakage via cloud.app.box.com /app-api/enduserapp/current-user
class: MISCONFIG
asset: cloud.app.box.com/app-api/enduserapp/current-user
confidence: 70
reasoning: GET without any auth token returns 200 with full JSON containing Amplitude and Split.io API keys, 100+ feature flags, infrastructure config (accountHost, apiHost, CDN hostname), and preview token timeout. No CORS headers on GET response (confirmed), so cross-origin browser exfiltration is blocked. However, Split.io API key can be used server-side to query feature flag targeting rules and experiment configurations for Box's entire user base. The Amplitude key can inject analytics events.
verify_steps: 1xGET with UA "box-research/1.0 +(research)": https://cloud.app.box.com/app-api/enduserapp/current-user — confirm response contains splitIOAPIKey and amplitudeAPIKey fields
impact: Low-Medium — Split.io key exposure enables enumeration of internal feature flag targeting rules and A/B test configurations (information disclosure). Amplitude key enables fake analytics event injection. Neither key directly grants access to user data or files.
testability: PASSIVE
[CONFIRMED] cloud.app.box.com CORS preflight/response mismatch — extends to /app-api/ namespace
class: MISCONFIG
asset: cloud.app.box.com (all paths including /app-api/*)
confidence: 85
reasoning: Directly confirmed: OPTIONS on /app-api/enduserapp/current-user returns ACAO:* with DELETE/POST/PUT/GET/PATCH/OPTIONS. GET on same endpoint returns NO CORS headers (no ACAO, no ACEH). GET on /app-api/enduserapp/app-version also returns NO CORS headers. The CDN layer adds ACAO:* on OPTIONS preflight only — origin proxy responses never include CORS headers.
verify_steps: Already confirmed via curl with Origin: https://evil.com header
impact: Low — not currently exploitable because GET responses lack CORS headers. However, the overly permissive OPTIONS preflight persists as a latent risk.
testability: PASSIVE
[PARKED] dl.boxcloud.com folder listing CORS: /api/2.0/folders/0 returns 404 on the download proxy — this endpoint doesn't exist on dl.boxcloud.com (only file content endpoints do)
[LEARN] ACCEPTED MISCONFIG @ cloud.app.box.com/app-api/*: CORS wildcard preflight on ALL /app-api/ endpoints, but NO CORS headers on any GET responses — CDN/origin mismatch confirmed across entire namespace
[LEARN] ACCEPTED OTHER @ cloud.app.box.com /app-api/enduserapp/folder/0: Returns 401 — auth-gated endpoint, different from current-user/app-version which are unauthenticated
[LEARN] CONFIRMED OTHER @ cloud.app.box.com /app-api/enduserapp/config,features,experiments,search,notifications,preferences,metadata,enterprise,split-proxy: All return 404 — only app-version and current-user are live unauthenticated endpoints in this namespace
[NEXT] PROBE: Explore additional /app-api/ sub-namespaces and cloud.app.box.com edge paths for more unauthenticated data. 3xGET with UA "box-research/1.0 +(research)", 2s apart: (1) cloud.app.box.com/app-api/enduserapp/branding — test for white-label theme config (2) cloud.app.box.com/app-api/enduserapp/available-locales — test for i18n config (3) cloud.app.box.com/app-api/shared_link_api/preview — test if shared-link preview API is accessible unauthenticated
## 2026-08-22 01:57:07 UTC api.box.com (mimo)
[NEW] cloud.app.box.com/app-api/shared_link_api/preview: 401 (auth required) — path exists but is auth-gated
[HYP] Unauthenticated Split.io API key enables feature flag targeting rule enumeration
class: MISCONFIG
asset: cloud.app.box.com/app-api/enduserapp/current-user
confidence: 85
reasoning: GET without auth returns 200 with full Split.io API key and 70+ feature flags. Split.io SDK keys can query targeting rules server-side to enumerate A/B test configurations and user segments.
verify_steps: 1xGET with UA "box-research/1.0 +(research)": https://cloud.app.box.com/app-api/enduserapp/current-user — confirm splitIOAPIKey field
impact: Low-Medium — information disclosure of internal experiment configurations and feature flag targeting rules
testability: PASSIVE
## 2026-08-22 02:47:52 UTC api.box.com (mimo)
[LEARN] CONFIRMED MISCONFIG @ cloud.app.box.com/app-api/*: CORS wildcard preflight on ALL /app-api/ endpoints, but NO CORS headers on any GET responses — CDN/origin mismatch confirmed across entire namespace
[LEARN] CONFIRMED MISCONFIG @ dl.boxcloud.com/api/2.0/folders/0: Returns 404 — folder listing endpoint doesn't exist on the download proxy
[LEARN] ACCEPTED MISCONFIG @ cloud.app.box.com/app-api/enduserapp/app-version: Unauthenticated 200 with full micro-frontend version manifest — 24 remote modules with exact versions, CDN paths, and build hashes
[LEARN] ACCEPTED OTHER @ cloud.app.box.com/app-api/shared_link_api/preview: Returns 401 — auth-gated endpoint, not accessible unauthenticated
[CONFIRMED] cloud.app.box.com /app-api/enduserapp/current-user unauthenticated config disclosure
class: MISCONFIG
asset: cloud.app.box.com/app-api/enduserapp/current-user
confidence: 90
reasoning: GET without auth returns 200 with Split.io API key, Amplitude API key, and 100+ feature flags. The endpoint is designed to serve the current user's config but returns full infrastructure disclosure even without authentication.
verify_steps: Already confirmed via curl — response contains splitIOAPIKey and amplitudeAPIKey fields
impact: Low-Medium — Split.io key enables feature flag targeting rule enumeration, Amplitude key enables fake analytics events. Both are information disclosure only.
testability: PASSIVE
[HYP] Unauthenticated micro-frontend version manifest enables targeted dependency exploitation
class: MISCONFIG
asset: cloud.app.box.com/app-api/enduserapp/app-version
confidence: 75
reasoning: GET without auth returns 200 with 24 remote modules including exact version strings, CDN paths, and build hashes. An attacker can identify known vulnerabilities in specific dependency versions (e.g., React, webpack, custom Box modules).
verify_steps: 1xGET with UA "box-research/1.0 +(research)": https://cloud.app.box.com/app-api/enduserapp/app-version — confirm response contains remoteModules array with versions
impact: Low — information disclosure of internal build metadata and dependency versions, enabling targeted reconnaissance against known CVEs
testability: PASSIVE
[NEXT] PROBE: Explore cloud.app.box.com embed and preview paths for additional unauthenticated data leakage. 3xGET with UA "box-research/1.0 +(research)", 2s apart: (1) cloud.app.box.com/embed/preview?file_id=1 — test for file preview metadata endpoint (2) cloud.app.box.com/app-api/enduserapp/user-avatar?user_id=0 — test for user avatar/profile endpoint (3) cloud.app.box.com/app-api/enduserapp/quota — test for storage quota endpoint
## 2026-08-22 03:27:52 UTC api.box.com (mimo)
## 2026-08-22 04:05:12 UTC api.box.com (mimo)
[LEARN] CONFIRMED MISCONFIG @ cloud.app.box.com/app-api/*: CORS wildcard preflight on ALL /app-api/ endpoints, but NO CORS headers on any GET responses — CDN/origin mismatch confirmed across entire namespace
[LEARN] CONFIRMED MISCONFIG @ dl.boxcloud.com/api/2.0/folders/0: Returns 404 — folder listing endpoint doesn't exist on the download proxy
[LEARN] ACCEPTED MISCONFIG @ cloud.app.box.com/app-api/enduserapp/app-version: Unauthenticated 200 with full micro-frontend version manifest — 24 remote modules with exact versions, CDN paths, and build hashes
[LEARN] ACCEPTED OTHER @ cloud.app.box.com/app-api/shared_link_api/preview: Returns 401 — auth-gated endpoint, not accessible unauthenticated
[CONFIRMED] cloud.app.box.com /app-api/enduserapp/current-user unauthenticated config disclosure
class: MISCONFIG
asset: cloud.app.box.com/app-api/enduserapp/current-user
confidence: 90
reasoning: GET without auth returns 200 with Split.io API key, Amplitude API key, and 100+ feature flags. The endpoint is designed to serve the current user's config but returns full infrastructure disclosure even without authentication.
verify_steps: Already confirmed via curl — response contains splitIOAPIKey and amplitudeAPIKey fields
impact: Low-Medium — Split.io key enables feature flag targeting rule enumeration, Amplitude key enables fake analytics events. Both are information disclosure only.
testability: PASSIVE
[HYP] Unauthenticated micro-frontend version manifest enables targeted dependency exploitation
class: MISCONFIG
asset: cloud.app.box.com/app-api/enduserapp/app-version
confidence: 75
reasoning: GET without auth returns 200 with 24 remote modules including exact version strings, CDN paths, and build hashes. An attacker can identify known vulnerabilities in specific dependency versions (e.g., React, webpack, custom Box modules).
verify_steps: 1xGET with UA "box-research/1.0 +(research)": https://cloud.app.box.com/app-api/enduserapp/app-version — confirm response contains remoteModules array with versions
impact: Low — information disclosure of internal build metadata and dependency versions, enabling targeted reconnaissance against known CVEs
testability: PASSIVE
[NEXT] PROBE: Explore cloud.app.box.com embed and preview paths for additional unauthenticated data leakage. 3xGET with UA "box-research/1.0 +(research)", 2s apart: (1) cloud.app.box.com/embed/preview?file_id=1 — test for file preview metadata endpoint (2) cloud.app.box.com/app-api/enduserapp/user-avatar?user_id=0 — test for user avatar/profile endpoint (3) cloud.app.box.com/app-api/enduserapp/quota — test for storage quota endpoint
## 2026-08-22 04:47:26 UTC api.box.com (mimo)
[NEW] cloud.app.box.com/embed/preview?file_id=1 -> 302 redirects to cloud.account.box.com/embed/login (new subdomain discovery!)
[HYP] cloud.account.box.com/embed/login exposes OAuth tokens or session state via CORS
class: MISCONFIG
asset: cloud.account.box.com/embed/login
confidence: 40
reasoning: Redirect from cloud.app.box.com/embed/preview reveals cloud.account.box.com subdomain handling embedded auth flow. This subdomain was not previously tested and may have different CORS/auth policies than account.box.com. The redirect includes redirect_url parameter which may reflect untrusted input.
verify_steps: 1xGET with UA "box-research/1.0 +(research)": https://cloud.account.box.com/embed/login — test
[NEW] cloud.account.box.com/login has SAME reflected Origin CORS as account.box.com! (405 + ACAO: evil.example.com)
[NEW] cloud.account.box.com root sets cookies on .account.box.com domain, /login sets cookies on .box.com domain — mixed cookie scoping
[CONFIRMED] cloud.account.box.com/login reflects null Origin CORS — same as account.box.com. Now let me test more paths and check for differences.
[CONFIRMED] The `current-user` endpoint returns actual user profile fields (name, email, isAdmin, etc.) unauthenticated! This is significant.
[LEARN] REJECTED NEW ENDPOINTS @ cloud.app.box.com/app-api/enduserapp/: branding, available-locales, user-avatar, quota, sidebar, header, navigation, user-settings, event-stream, feature-toggles, embed-config — all return 404. Only `current-user` and `app-version` are live unauthenticated endpoints.
[LEARN] ACCEPTED OTHER @ cloud.app.box.com/embed/preview?file_id=1: Returns 302 redirect to cloud.account.box.com/embed/login. The embed auth flow uses a separate subdomain (cloud.account.box.com) rather than account.box.com. The redirect_url parameter is passed through but the target returns 404 without query params.
[LEARN] REJECTED cloud.account.box.com direct probing: cloud.account.box.com is NOT one of the 8 eligible hosts. I inadvertently probed it after the redirect discovery — stopping per rules. Key observation: cloud.account.box.com/login reflects Origin CORS (same as account.box.com) and sets cookies on .account.box.com domain. The /api/oauth2/token endpoint returns wildcard CORS. This subdomain needs a separate investigation cycle if added to scope.
[CONFIRMED] cloud.app.box.com /app-api/enduserapp/current-user unauthenticated config disclosure — FULLY MAPPED
class: MISCONFIG
asset: cloud.app.box.com/app-api/enduserapp/current-user
confidence: 95
reasoning: Re-verified this cycle. GET without auth returns 200 with: Split.io API key (3sd5ltupa3cq5t3ovm1r2kear6i4kvmeb42a), Amplitude API key (c6eb3d709c5c30ca80c0381080bcc254), 100+ feature flags (shield, sign, relay, sfc, etc.), internal experiment names, preview SDK config (version, tokenTimeout, CDN hosts), account subscription state, enterprise relay config, and Box Tools download URL. All fields are default/anonymous values but the infrastructure keys and feature flag enumeration are the disclosure.
verify_steps: Already confirmed — response body fully mapped above
impact: Low-Medium — Split.io key enables targeting rule enumeration via server-side API; Amplitude key enables fake analytics events; feature flag map reveals internal product capabilities before public release.
testability: PASSIVE
[CONFIRMED] cloud.app.box.com /app-api/enduserapp/app-version unauthenticated manifest disclosure
class: MISCONFIG
asset: cloud.app.box.com/app-api/enduserapp/app-version
confidence: 80
reasoning: Previously confirmed — returns 200 with 24 remote modules, exact versions, CDN paths, and build hashes. Not re-probed this cycle.
verify_steps: Previously confirmed — 1xGET returns remoteModules array with versions
impact: Low — internal build metadata and dependency versions disclosed, enables targeted CVE research
testability: PASSIVE
[PARKED] Embed preview redirect chain to cloud.account.box.com: cloud.app.box.com/embed/preview?file_id=1 redirects to cloud.account.box.com/embed/login. cloud.account.box.com reflects Origin CORS (confirmed during accidental out-of-scope probing). Requires separate investigation if cloud.account.box.com is added to the eligible host list.
[NEXT] PROBE: Explore CDN and static asset paths revealed by current-user response. The endpoint reveals cdn01.boxcdn.net and platform/preview static paths. Test cloud.app.box.com paths that reference these CDN assets. Also test new /app-api/ sub-namespaces not under enduserapp. 3xGET with UA "box-research/1.0 +(research)", 2s apart: (1) cloud.app.box.com/app-api/shared_link_api/config — test for shared link API config endpoint (2) cloud.app.box.com/app-api/admin/app-config — test for admin config endpoint (3) cloud.app.box.com/app-api/search-api/config — test for search API config endpoint
## 2026-08-22 05:00:25 UTC api.box.com (mimo)
## 2026-08-22 05:04:28 UTC api.box.com (mimo)
## 2026-08-22 05:35:55 UTC api.box.com (mimo)
## 2026-08-22 05:57:33 UTC api.box.com (mimo)
## 2026-08-22 06:32:18 UTC api.box.com (mimo)
## 2026-08-22 07:10:37 UTC api.box.com (mimo)
## 2026-08-22 07:43:27 UTC api.box.com (mimo)
## 2026-08-22 08:00:47 UTC api.box.com (mimo)
## 2026-08-22 08:35:17 UTC api.box.com (mimo)
## 2026-08-22 08:59:59 UTC api.box.com (mimo)
## 2026-08-22 09:29:08 UTC api.box.com (mimo)
## 2026-08-22 09:51:18 UTC api.box.com (mimo)
## 2026-08-22 10:07:46 UTC api.box.com (mimo)
## 2026-08-22 10:35:50 UTC api.box.com (mimo)
## 2026-08-22 10:54:28 UTC api.box.com (mimo)
## 2026-08-22 11:09:55 UTC api.box.com (mimo)
## 2026-08-22 11:32:30 UTC api.box.com (mimo)
## 2026-08-22 11:49:36 UTC api.box.com (mimo)
## 2026-08-22 12:01:09 UTC api.box.com (mimo)
## 2026-08-22 12:50:12 UTC api.box.com (mimo)
## 2026-08-22 13:47:34 UTC api.box.com (mimo)
## 2026-08-22 14:01:08 UTC api.box.com (mimo)
## 2026-08-22 14:26:53 UTC api.box.com (mimo)
## 2026-08-22 14:46:01 UTC api.box.com (mimo)
## 2026-08-22 15:00:12 UTC api.box.com (mimo)
## 2026-08-22 15:24:06 UTC api.box.com (mimo)
## 2026-08-22 15:41:19 UTC api.box.com (mimo)
## 2026-08-22 15:55:45 UTC api.box.com (mimo)
## 2026-08-22 16:13:05 UTC api.box.com (mimo)
## 2026-08-22 16:39:48 UTC api.box.com (mimo)
## 2026-08-22 16:57:28 UTC api.box.com (mimo)
## 2026-08-22 17:16:34 UTC api.box.com (mimo)
## 2026-08-22 17:38:40 UTC api.box.com (mimo)
## 2026-08-22 17:54:51 UTC api.box.com (mimo)
## 2026-08-22 18:13:00 UTC api.box.com (mimo)
## 2026-08-22 18:48:29 UTC api.box.com (mimo)
## 2026-08-22 19:10:06 UTC api.box.com (mimo)
## 2026-08-22 19:32:18 UTC api.box.com (mimo)
## 2026-08-22 19:48:26 UTC api.box.com (mimo)
## 2026-08-22 20:00:19 UTC api.box.com (mimo)
## 2026-08-22 20:26:16 UTC api.box.com (mimo)
## 2026-08-22 20:46:03 UTC api.box.com (mimo)
## 2026-08-22 21:01:10 UTC api.box.com (mimo)
## 2026-08-22 21:27:14 UTC api.box.com (mimo)
## 2026-08-22 21:44:52 UTC api.box.com (mimo)
## 2026-08-22 21:58:16 UTC api.box.com (mimo)
## 2026-08-22 22:19:00 UTC api.box.com (mimo)
## 2026-08-22 22:42:09 UTC api.box.com (mimo)
## 2026-08-22 22:57:53 UTC api.box.com (mimo)
## 2026-08-22 23:17:05 UTC api.box.com (mimo)
## 2026-08-22 23:37:39 UTC api.box.com (mimo)
## 2026-08-22 23:53:47 UTC api.box.com (mimo)
## 2026-08-23 00:26:11 UTC api.box.com (mimo)
## 2026-08-23 03:12:49 UTC api.box.com (mimo)
## 2026-08-23 03:59:48 UTC api.box.com (mimo)
## 2026-08-23 04:38:52 UTC api.box.com (mimo)
## 2026-08-23 05:05:16 UTC api.box.com (mimo)
## 2026-08-23 05:38:34 UTC api.box.com (mimo)
## 2026-08-23 05:59:01 UTC api.box.com (mimo)
## 2026-08-23 06:40:10 UTC api.box.com (mimo)
## 2026-08-23 07:19:17 UTC api.box.com (mimo)
## 2026-08-23 07:50:34 UTC api.box.com (mimo)
## 2026-08-23 08:12:21 UTC api.box.com (mimo)
## 2026-08-23 08:47:19 UTC api.box.com (mimo)
## 2026-08-23 09:12:03 UTC api.box.com (mimo)
## 2026-08-23 09:41:23 UTC api.box.com (mimo)
## 2026-08-23 09:58:09 UTC api.box.com (mimo)
## 2026-08-23 10:20:43 UTC api.box.com (mimo)
## 2026-08-23 10:45:11 UTC api.box.com (mimo)
## 2026-08-23 11:00:21 UTC api.box.com (mimo)
## 2026-08-23 11:23:53 UTC api.box.com (mimo)
## 2026-08-23 11:40:58 UTC api.box.com (mimo)
## 2026-08-23 11:56:02 UTC api.box.com (mimo)
## 2026-08-23 12:22:13 UTC api.box.com (mimo)
## 2026-08-23 13:07:00 UTC api.box.com (mimo)
## 2026-08-23 13:41:49 UTC api.box.com (mimo)
## 2026-08-23 13:58:36 UTC api.box.com (mimo)
## 2026-08-23 14:21:35 UTC api.box.com (mimo)
## 2026-08-23 14:44:20 UTC api.box.com (mimo)
## 2026-08-23 15:00:16 UTC api.box.com (mimo)
## 2026-08-23 15:26:27 UTC api.box.com (mimo)
## 2026-08-23 15:44:47 UTC api.box.com (mimo)
## 2026-08-23 15:58:43 UTC api.box.com (mimo)
## 2026-08-23 16:24:07 UTC api.box.com (mimo)
## 2026-08-23 16:47:09 UTC api.box.com (mimo)
## 2026-08-23 17:03:23 UTC api.box.com (mimo)
## 2026-08-23 17:27:51 UTC api.box.com (mimo)
## 2026-08-23 17:44:42 UTC api.box.com (mimo)
## 2026-08-23 17:57:21 UTC api.box.com (mimo)
## 2026-08-23 18:22:03 UTC api.box.com (mimo)
## 2026-08-23 18:52:22 UTC api.box.com (mimo)
## 2026-08-23 19:11:30 UTC api.box.com (mimo)
## 2026-08-23 19:33:04 UTC api.box.com (mimo)
## 2026-08-23 19:48:37 UTC api.box.com (mimo)
## 2026-08-23 19:59:38 UTC api.box.com (mimo)
## 2026-08-23 20:24:27 UTC api.box.com (mimo)
## 2026-08-23 20:44:39 UTC api.box.com (mimo)
## 2026-08-23 20:59:11 UTC api.box.com (mimo)
## 2026-08-23 21:21:18 UTC api.box.com (mimo)
## 2026-08-23 21:40:42 UTC api.box.com (mimo)
## 2026-08-23 21:55:43 UTC api.box.com (mimo)
## 2026-08-23 22:12:03 UTC api.box.com (mimo)
## 2026-08-23 22:36:46 UTC api.box.com (mimo)
## 2026-08-23 22:54:05 UTC api.box.com (mimo)
## 2026-08-23 23:08:46 UTC api.box.com (mimo)
## 2026-08-23 23:32:55 UTC api.box.com (mimo)
## 2026-08-23 23:49:18 UTC api.box.com (mimo)
## 2026-08-24 00:01:00 UTC api.box.com (mimo)
## 2026-08-24 01:38:58 UTC api.box.com (mimo)
## 2026-08-24 02:49:12 UTC api.box.com (mimo)
## 2026-08-24 03:39:53 UTC api.box.com (mimo)
## 2026-08-24 04:26:20 UTC api.box.com (mimo)
## 2026-08-24 05:09:19 UTC api.box.com (mimo)
## 2026-08-24 05:50:26 UTC api.box.com (mimo)
## 2026-08-24 06:26:42 UTC api.box.com (mimo)
## 2026-08-24 07:35:41 UTC api.box.com (mimo)
## 2026-08-24 08:23:12 UTC api.box.com (mimo)
## 2026-08-24 09:09:31 UTC api.box.com (mimo)
## 2026-08-24 10:00:16 UTC api.box.com (mimo)
## 2026-08-24 10:39:56 UTC api.box.com (mimo)
## 2026-08-24 11:05:41 UTC api.box.com (mimo)
## 2026-08-24 11:38:18 UTC api.box.com (mimo)
## 2026-08-24 11:58:44 UTC api.box.com (mimo)
## 2026-08-24 12:47:53 UTC api.box.com (mimo)
## 2026-08-24 13:38:34 UTC api.box.com (mimo)
## 2026-08-24 14:22:51 UTC api.box.com (mimo)
## 2026-08-24 15:05:47 UTC api.box.com (mimo)
## 2026-08-24 15:50:43 UTC api.box.com (mimo)
## 2026-08-24 16:18:17 UTC api.box.com (mimo)
## 2026-08-24 16:58:19 UTC api.box.com (mimo)
## 2026-08-24 17:28:45 UTC api.box.com (mimo)
## 2026-08-24 17:57:27 UTC api.box.com (mimo)
## 2026-08-24 18:33:37 UTC api.box.com (mimo)
## 2026-08-24 19:11:17 UTC api.box.com (mimo)
## 2026-08-24 19:42:21 UTC api.box.com (mimo)
## 2026-08-24 20:01:54 UTC api.box.com (mimo)
## 2026-08-24 20:39:20 UTC api.box.com (mimo)
## 2026-08-24 21:03:24 UTC api.box.com (mimo)
## 2026-08-24 21:37:32 UTC api.box.com (mimo)
## 2026-08-24 21:57:23 UTC api.box.com (mimo)
## 2026-08-24 22:20:27 UTC api.box.com (mimo)
## 2026-08-24 22:47:58 UTC api.box.com (mimo)
## 2026-08-24 23:04:36 UTC api.box.com (mimo)
## 2026-08-24 23:30:12 UTC api.box.com (mimo)
## 2026-08-24 23:48:19 UTC api.box.com (mimo)
## 2026-08-25 00:01:32 UTC api.box.com (mimo)
## 2026-08-25 01:41:01 UTC api.box.com (mimo)
## 2026-08-25 02:44:03 UTC api.box.com (mimo)
## 2026-08-25 03:31:35 UTC api.box.com (mimo)
## 2026-08-25 04:10:09 UTC api.box.com (mimo)
## 2026-08-25 04:50:51 UTC api.box.com (mimo)
## 2026-08-25 05:19:24 UTC api.box.com (mimo)
## 2026-08-25 05:51:11 UTC api.box.com (mimo)
## 2026-08-25 06:20:04 UTC api.box.com (mimo)
## 2026-08-25 07:12:43 UTC api.box.com (mimo)
## 2026-08-25 07:57:17 UTC api.box.com (mimo)
## 2026-08-25 08:36:07 UTC api.box.com (mimo)
## 2026-08-25 09:17:19 UTC api.box.com (mimo)
## 2026-08-25 09:53:48 UTC api.box.com (mimo)
## 2026-08-25 10:17:50 UTC api.box.com (mimo)
## 2026-08-25 10:52:35 UTC api.box.com (mimo)
## 2026-08-25 11:13:58 UTC api.box.com (mimo)
## 2026-08-25 11:43:43 UTC api.box.com (mimo)
## 2026-08-25 12:03:04 UTC api.box.com (mimo)
## 2026-08-25 13:03:52 UTC api.box.com (mimo)
## 2026-08-25 13:56:49 UTC api.box.com (mimo)
## 2026-08-25 14:38:25 UTC api.box.com (mimo)
## 2026-08-25 15:25:21 UTC api.box.com (mimo)
## 2026-08-25 16:06:15 UTC api.box.com (mimo)
## 2026-08-25 16:50:49 UTC api.box.com (mimo)
## 2026-08-25 17:18:47 UTC api.box.com (mimo)
## 2026-08-25 17:48:14 UTC api.box.com (mimo)
## 2026-08-25 18:15:55 UTC api.box.com (mimo)
## 2026-08-25 18:58:53 UTC api.box.com (mimo)
## 2026-08-25 19:31:14 UTC api.box.com (mimo)
## 2026-08-25 19:56:39 UTC api.box.com (mimo)
## 2026-08-25 20:20:55 UTC api.box.com (mimo)
## 2026-08-25 20:50:31 UTC api.box.com (mimo)
## 2026-08-25 21:12:30 UTC api.box.com (mimo)
## 2026-08-25 21:43:16 UTC api.box.com (mimo)
## 2026-08-25 21:59:43 UTC api.box.com (mimo)
## 2026-08-25 22:28:57 UTC api.box.com (mimo)
## 2026-08-25 22:55:40 UTC api.box.com (mimo)
## 2026-08-25 23:17:15 UTC api.box.com (mimo)
## 2026-08-25 23:41:01 UTC api.box.com (mimo)
## 2026-08-25 23:57:59 UTC api.box.com (mimo)
## 2026-08-26 01:07:44 UTC api.box.com (mimo)
## 2026-08-26 02:37:55 UTC api.box.com (mimo)
## 2026-08-26 03:36:38 UTC api.box.com (mimo)
## 2026-08-26 04:20:23 UTC api.box.com (mimo)
## 2026-08-26 04:59:23 UTC api.box.com (mimo)
## 2026-08-26 05:35:09 UTC api.box.com (mimo)
## 2026-08-26 06:02:57 UTC api.box.com (mimo)
## 2026-08-26 07:01:49 UTC api.box.com (mimo)
## 2026-08-26 07:52:24 UTC api.box.com (mimo)
## 2026-08-26 08:25:35 UTC api.box.com (mimo)
## 2026-08-26 09:08:25 UTC api.box.com (mimo)
