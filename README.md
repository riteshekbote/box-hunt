# box-hunt

24/7 read-only bug-hunting automation for the **Box** HackerOne program (8 in-scope hosts).

- Per-host analyst loop (2 opencode models per host, 8 hosts) every 5 minutes
- Movement one phase per cycle: RECON -> SURFACE -> HYPOTHESIS -> POC
- Passive verifier robot: runs each cycle's concrete read-only probes, logs actual status codes
- Hard rules: GET/HEAD/OPTIONS only, no writes, no account/upload/OAuth flows, politeness budget,
  403/429 grace-stop, and the SignRequest honeypot and partner staging are hard-forbidden
- Triage job ranks hypotheses by confidence hourly

Hosts in scope: `api.box.com`, `app.box.com`, `account.box.com`, `upload.box.com`,
`dl.boxcloud.com`, `notes.services.box.com`, `m.box.com`, `cloud.app.box.com`

See `scope.txt` for the exact rules the models are bound to.