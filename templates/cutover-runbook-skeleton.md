# Timed cutover runbook (skeleton)

Window: `<start>–<end> UTC` (owner local time: `<…>`). Fallback window: `<…>`.
Each step lists its **expected output** and its **stop condition**. A failed stop condition means NO-GO: hold, report, and do not improvise.

| Step | Action | Owner | Expected | Stop if | Time |
|---|---|---|---|---|---|
| W | Pre-window checks: environment health, managed actions, secret rotation schedule, alarms, RPO baseline | Engineer | All PASS | Any FAIL | |
| W | Pre-change snapshot + file archive with SHA-256 | Engineer | Available | Not available | |
| C1 | Freeze: pause old production, stop old dev workers/schedulers | Owner + Engineer | 0 processes; times + screenshots | Anything still running | |
| C2 | Prove no old activity: payments, mail log, monitors | Owner | No activity after the freeze | Unexplained activity | |
| C3 | Final source export (consistent DB + files) | Engineer | Hashes recorded | Hash mismatch | |
| C4 | Transfer over an encrypted channel | Engineer | Hash verified | Mismatch | |
| C5 | Import (approved migrate principal) | Engineer | rc 0, 0 error lines | Any error | |
| C6 | Files copy | Engineer | Count + hash match | Mismatch | |
| C7 | Reconcile: per-table counts + checksums + invariants + file references | Engineer | 100 % match | Any diff | |
| C8 | Live configuration entered by the owner; type/length checks only | Owner + Engineer | All present | Missing / wrong type | |
| C9 | Synthetic smoke + preservation matrix | Engineer | All PASS | Any FAIL | |
| C10 | GO / NO-GO review | Owner | Written GO | No GO | |
| C11 | DNS switch + post-switch checks | Engineer | All names → new target; checks PASS | FAIL → rollback plan | |
| C12 | Enable effects one at a time: mail, then job waves | Owner approves each | Signed run evidence, 0 held | Held/unknown outcomes | |
| M | 24 h monitoring | Engineer | 0 5xx, alarms healthy | NO-GO condition | |

Rollback: DNS back + old platform resumes **only** if no new writes have reached AWS; otherwise hold writes and choose an approved forward fix.
