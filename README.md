# @ternco/scopes-collector

The Postgres collector for [Ternco Scopes](https://scopes.terncosoftware.com).

It runs next to your database, reads Postgres's own statistics views, and sends
them to Scopes. It only reads, so it needs no superuser, no inbound port and no
server setting change. Statement text is redacted before it leaves your network.

## What it collects

- Sessions, waits, blocking and replication lag
- Statement statistics from `pg_stat_statements`
- Database, WAL and checkpoint activity
- Table and index statistics, sizes and vacuum state
- Query plans for your heaviest statements

## Run it

Create a monitoring role:

```sql
CREATE ROLE scopes LOGIN PASSWORD '...';
GRANT pg_monitor TO scopes;
CREATE EXTENSION IF NOT EXISTS pg_stat_statements;
```

Then start the collector with your Scopes key and a connection string:

```bash
npx @ternco/scopes-collector@1.0.0 --key ts_live_... --database-url postgres://scopes:...@db.internal:5432/app
```

or as a container:

```bash
docker run -d --restart=always \
  -e SCOPES_KEY=ts_live_... \
  -e DATABASE_URL=postgres://scopes:...@db.internal:5432/app \
  ghcr.io/terncosoftware/scopes-collector:1.0.0
```

## Options

| Flag | Environment | Default |
|---|---|---|
| `--key` | `SCOPES_KEY` | required |
| `--database-url` | `DATABASE_URL`, `DATABASE_URLS` | required |
| `--ingest-url` | `SCOPES_INGEST_URL` | `https://scopes.terncosoftware.com/api/scopes/ingest` |
| `--name` | `SCOPES_NAME` | `scopes-collector` |
| `--spool-dir` | `SCOPES_SPOOL_DIR` | `/var/lib/scopes/spool` |
| `--discover` | | off |
| `--no-query-text` | | off |
| `--full-on-replicas` | | off |
| `--no-custom-metrics` | | off |
| `--activity-interval` | | 10 seconds |
| `--statements-interval` | | 60 seconds |
| `--catalog-interval` | | 600 seconds |
| `--plans-interval` | | 600 seconds |
| `--metrics-tick` | | 5 seconds |
| `--version` | | prints the version and exits |
| `--help` | | prints the options and exits |

Full documentation: <https://terncosoftware.com/docs/scopes/collector>

## License

[Business Source License 1.1](LICENSE). Copyright 2026 Ternco Software Inc.
