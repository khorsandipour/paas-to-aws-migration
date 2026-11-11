# Preservation test matrix (template)

Run after **every** release, instance replacement and schema-compatible rollback. A live homepage is not acceptance.

| # | Test | How | Pass criteria | Result |
|---|---|---|---|---|
| 1 | New registration survives | Create a test account before the deploy, check it after | Account present | |
| 2 | Password change persists | Change the password before the deploy | New password accepted, **old one rejected** | |
| 3 | Sessions / OAuth state / import previews | Start each before replacement | Still valid, or the one sign-out was documented | |
| 4 | Business records | Per-table counts + checksums + business invariants | All match the baseline | |
| 5 | Files and permissions | Hash list + permission check; cross-tenant access attempt | Match; cross-tenant access denied | |
| 6 | Issued documents | SHA-256 against the canonical list | Byte-identical, never regenerated | |
| 7 | All domains / locales / assets / verification links | Scripted URL check | All expected status codes | |
| 8 | Wrong or missing store | Simulate a missing mount / wrong DB in a test environment | Readiness blocked, alarm sent, **no fallback** | |
| 9 | Recovery | Independent DB + files restore in isolation | Within the agreed RPO/RTO | |
| 10 | Rollback | Compatible rollback with newer records present | Data retained; unknown outcomes reconciled | |

Record: date (UTC), release label, operator, evidence file + SHA-256.
