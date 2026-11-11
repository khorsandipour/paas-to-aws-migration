# Scheduler / job inventory (template)

Rules:
- Take exclusive ownership **before** any business effect, and guard every entry point (timer, manual, web, CLI).
- A takeover requires the old process group to be terminated or fenced. A lease expiry or a silent heartbeat is not proof.
- A heartbeat is not success. Alarm on the age of the last *business* success.
- An unknown email or API outcome is **held** for reconciliation. It is never retried blindly.
- Record UTC plus the named business timezone, and test DST transitions.

| Field | Job A | Job B |
|---|---|---|
| Launcher / script | | |
| Family (internal / staff mail / customer mail / external API) | | |
| Schedule + timezone | | |
| Actor / permissions | | |
| Persistent state location | | |
| Lock / ownership / termination method | | |
| Heartbeat + last business success | | |
| External effects | | |
| Existing idempotency | | |
| Claim / result uncertainty | | |
| Overlap / retry limits | | |
| Alarm threshold | | |
| Rehearsal sink | | |
| Enablement wave + approval | | |

## Failure cases to prove
Concurrent starts · DB lock loss · killed or overlapping instance · crash before/after send · deploy during a job · duplicate payment webhook · old and new platforms both available · missing or wrong file mount · DST transition.
