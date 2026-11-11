# Findings register (template)

| Level | Meaning |
|---|---|
| **P0** | Immediate data-preservation or security risk. Report at once, before continuing |
| **P1** | Blocks migration acceptance. Must be resolved before its gate |
| **P2** | Improvement. Must never be relabelled P2 to get past a gate |

## Finding P?-NN · <short title>

| Field | Detail |
|---|---|
| Source | Which deliverable or check found it |
| Evidence | File/line, command output (secrets masked), screenshot hash |
| Observed vs uncertain | What is proven / what is still unknown |
| Impact | What breaks, for whom, how badly |
| Plan reference | Section of the controlling plan |
| Owner | Developer / client / provider |
| Required action | Exact change |
| Approval needed | Yes / No (who) |
| Fix order | Before which gate |
| Dependencies | Other findings |
| Retest criteria | How we prove it is fixed |
| Status | OPEN / FIXED / CLOSED (date + evidence) |
