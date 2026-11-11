# Target architecture

| Layer | Choice | Notes |
|---|---|---|
| Compute | Elastic Beanstalk, 1 × t3.small, Amazon Linux 2023, PHP 8.4, nginx | Larger size only if measured load requires it |
| Database | RDS MariaDB, private subnets | TLS with a verified CA/hostname, encryption at rest, least-privilege grants, engine support dates checked |
| Files | EFS, encrypted, TLS mount | Mount identity verified; no ephemeral fallback; backups kept out of web paths |
| Sessions | MariaDB session handler | Exactly one documented sign-out at cutover |
| Config / secrets | SSM Parameter Store | Never bundled with releases; missing value = explicit failure |
| Deployments | Versioned bundles with a pinned config hash; GitHub Actions OIDC designed | No long-lived AWS keys; approval before every release |
| PDF | Chromium under a dedicated user, network-isolated | Max 2 concurrent renders, resource limits, issued PDFs immutable |
| Jobs | systemd timers + a shared runner wrapper | Exclusive ownership, signed evidence per run, missed-success alarms |
| Monitoring | CloudWatch alarms → SNS (email) + chat webhook | Health, 5xx, DB memory, EFS, backups, per-job success age, held outcomes |
| Backups | RDS automated backups (≥ 7 days) + pre-change snapshots + versioned S3 file archives | Recovery is a separate, approved operation |
| Domains | Host-aware nginx for two domains | HTTPS redirect, canonical host, unexpected hosts rejected |

## Data-safety layers

```mermaid
flowchart TB
  D[Deploy / restart / replacement] -->|code only| APP[Application]
  APP -->|DML only| DB[(Database)]
  MIG[Migrate principal] -.->|approved schema change<br/>after a checkpoint| DB
  REC[Recovery role] -.->|separate approved operation<br/>never at runtime| DB
  APP -->|missing config / wrong DB / wrong mount| STOP[Readiness blocked + alarm]
```

## Cutover fencing

```mermaid
sequenceDiagram
  participant Old as Old platform
  participant Ops as Engineer
  participant Owner
  participant New as AWS
  Ops->>Owner: Pre-checks PASS, request GO for freeze
  Owner->>Old: Pause production
  Ops->>Old: Stop dev workers, verify 0 processes
  Ops->>New: Final import, files, reconcile
  Ops->>Owner: Results + GO/NO-GO
  Owner->>Ops: GO
  Ops->>New: DNS switch, post checks
  Ops->>Owner: Mail and job waves, each approved
```
