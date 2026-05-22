# Legacy Decommission - Synapse Labels

Date: 2026-05-22

## Scope

The Skirmshop labels Synapse runtime was migrated from Docker on `sauvage` to k8s/GitOps in `k8s-shopify-label-pocharlies`.

## Backup

Before stopping Docker legacy containers, the legacy Postgres container was exported with `pg_dumpall`.

Backup path on `sauvage`:

```text
/home/ubuntu/backups/k8s-legacy-decom/20260522-synapse-labels/skirmshop-labels-postgres-pgdumpall-20260522T093934.sql.gz
```

## Docker Containers Stopped

The following legacy Docker containers were stopped, but not removed:

- `skirmshop-labels-shopify-app`
- `skirmshop-labels-cex-adapter`
- `skirmshop-labels-saga-launcher`
- `skirmshop-labels-routing-service`
- `skirmshop-labels-label-storage`
- `skirmshop-labels-ups-adapter`
- `skirmshop-labels-synapse-operator`
- `skirmshop-labels-synapse-api`
- `skirmshop-labels-event-gateway`
- `skirmshop-labels-synapse-correlator`
- `skirmshop-labels-postgres`
- `skirmshop-labels-nats`
- `skirmshop-labels-redis`
- `monitoring-postgres-exporter-labels`

## Validation

- ArgoCD `shopify-label`: `Synced` and `Healthy`
- k8s labels services: all expected pods `1/1 Running`
- Synapse workflows registered: `generate-shipment-label`, `handle-tracking-event`, `poll-tracking`, `process-return`
- Synapse workflow instances after cutover: `0`
- NATS `SHIPPING_EVENTS` consumers: `num_pending=0`, `num_ack_pending=0`
- Public health endpoints:
  - `https://skirmshop.e-dani.com/labels/health` -> `200`
  - `https://skirmshop.e-dani.com/labels-ups/health` -> `200`

## Notes

The Docker containers remain present for rollback. Volumes and images were not deleted.
