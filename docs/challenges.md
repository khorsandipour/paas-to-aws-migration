# Challenges and how they were solved

Overall difficulty: **9/10**. This was a live system with paying customers and a history of data loss, on a fixed price, with ten contractual "red flag" hard stops, and two domains on one database.

| Phase | Challenge | Resolution |
|---|---|---|
| Discovery | Production shell access was unavailable at the start | Escalated with the provider; continued the analysis from source and configuration in the meantime |
| Discovery | The cause of the data loss was unclear | Traced the startup path step by step; separated observed facts from uncertainty |
| Build | Generating PDFs from untrusted content | Dedicated OS user, per-user egress firewall, per-job temp dirs, hostile-content tests |
| Build | Silent fallback to development settings | Fail-closed configuration; all fallbacks removed |
| Rehearsal | DB and files had to come from the same point in time | Frozen source with hashes plus a file-reference checker |
| Rehearsal | Retention of rehearsal data inside automated backups | Computed the exact expiry; got an extension approved **before** import |
| Rehearsal | Proving repeatability | Two full rehearsals with identical results |
| Cutover | A dev workspace on the old platform restarted by itself | Stopped it again, added an extra fence with the owner's approval, kept the gate closed until verified |
| Cutover | The window was too short for all checks | Asked for a written extension; no step was skipped |
| Cutover | A live credential value had a format problem | Pre-activation checks caught it; it was corrected before any user was affected |
| Stabilisation | Database memory pressure | Time-boxed exception with monitoring, then the alarm was re-tuned from measured data with no upsizing cost |
| Stabilisation | Turning on customer mail and jobs on real data | Waves with dry-runs, cut-off dates and a separate approval for each wave |
| Support | A third-party provider's security notice | Inventoried the exposed keys and advised rotation using a method that never prints values |

## Lessons

- Flag a schedule risk before the deadline passes. In this project, the extension was accepted in writing with no change to scope or fee.
- Rehearse the whole cutover, not just the import.
- Automate pre-checks. They catch human-entry errors before users see them.
- Short consolidated reports plus priced options lead to faster decisions.
