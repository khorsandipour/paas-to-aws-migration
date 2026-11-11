# Method: four gated milestones

Every milestone ends at a **gate**. Work does not continue until the owner has reviewed the evidence and approved the next step in writing.

## Milestone 1: read-only discovery

Nothing is created, copied or changed.

| Deliverable | Content |
|---|---|
| Root-cause report | Observed facts kept separate from what remains uncertain |
| Infrastructure inventory | Accounts, DB engine/version/collation/timezone/SQL mode, grants, triggers/routines/events, sizes, network, backups. Secret values are never inspected |
| Job inventory | Every scheduled, manual, web and CLI entry point, with its state locations |
| Domain/provider matrix | DNS, TLS, callbacks, provider allowlists |
| Compatibility review | Runtime version, PDF renderer qualification tests |
| DB lifecycle | Engine support and retirement dates, upgrade policy |
| Timeout matrix | Load balancer, proxy, PHP-FPM, client and browser timeouts |
| Ranked findings | P0 (immediate risk), P1 (blocks acceptance), P2 (improvement) |
| Cost breakdown | Measured and proposed costs, with no double counting |

**Gate:** the owner approves a bounded "dark" AWS build, its resources and its cost.

## Milestone 2: repeatable dark build with synthetic data

- Remove every automatic restore/import path from startup and deploy.
- Configuration comes from a parameter store. Missing values fail explicitly.
- Use a separate database principal per purpose: app (DML only), migrate (schema, run only as an approved change) and recovery (never present at runtime).
- Use an isolated PDF renderer: a dedicated OS user, per-user firewall rules, per-job temp directories, a concurrency limit, and tests against hostile HTML/SVG/URLs.
- Define every package and service in the deployment config so it survives instance replacement.
- Alarms are proven with safe test stimuli, not assumed to work.

**Gate:** the owner reviews the evidence and authorises a controlled real-data rehearsal.

## Milestone 3: timed rehearsals

- Freeze one consistent DB + files source and record its hashes.
- Check backup retention **before** import, so rehearsal data does not outlive the agreed deletion date.
- Run the full cutover **twice**: import → files → reconcile (per-table counts and checksums) → preservation matrix → instance replacement → cleanup.
- Keep all external effects (mail, payments, AI, webhooks) disabled or routed to sinks.
- Feed the measured timings into the cutover runbook.

**Gate:** the owner accepts the runbook and the RPO/RTO targets, and approves the cutover window.

## Milestone 4: live cutover and support

1. Take a pre-cutover snapshot and record the evidence baseline.
2. Fence the old platform (production and any dev workers) and record times and screenshots.
3. Confirm no writes and no deliveries since the freeze.
4. Run the final import, file copy and full reconciliation.
5. Load the live configuration (the owner enters the values) and run type checks without printing secrets.
6. Run synthetic smoke and preservation tests, then GO/NO-GO.
7. Switch DNS and run the post-switch checks.
8. Enable mail, then jobs, one wave at a time. Each wave gets a dry-run, the owner's go and its own missed-success alarm.
9. Monitor for 24 hours, then hand over acceptance evidence.
10. Provide 14 days of support: daily read-only checks, a handover pack (deploy, rollback, restore, jobs, incidents, keys, costs), an owner walkthrough and access removal.

## Communication rules

- Report P0 immediately. Everything else goes into one consolidated report.
- Every issue comes with options, the cost of each, and a recommendation.
- Times are given in UTC and in the owner's local time.
- A schedule risk is flagged **before** a deadline passes, never after.
