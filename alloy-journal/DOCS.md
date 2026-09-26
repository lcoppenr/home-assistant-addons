# Alloy Journal to Loki

Reads the Home Assistant OS systemd journal with Grafana Alloy and pushes it
to Loki's push API (`/loki/api/v1/push`). It talks to Loki directly, not
through syslog, so nothing that forwards syslog onward (a SIEM, for example)
receives these logs.

## Options

| Option | Default | Meaning |
|---|---|---|
| `loki_url` | `http://10.0.1.15:3100/loki/api/v1/push` | Loki push endpoint |
| `host_label` | `homeassistant` | Value of the `host` label on every stream |
| `log_level` | `info` | Alloy's own log level |
| `exclude_syslog_identifiers` | `[]` | Journal identifiers to drop, e.g. a noisy add-on `addon_<hash>_<slug>` (the `app_` prefix is matched too) |

## Labels

| Label | Set on | Value |
|---|---|---|
| `host` | every stream | `host_label` |
| `job` | every stream | `systemd-journal` |
| `container` | Docker containers | `homeassistant` (Core), `hassio_supervisor`, `addon_<hash>_<slug>`, ... |
| `service` | everything that is not a container | systemd unit without `.service`, or the syslog identifier |
| `level` | non-container entries | journal priority (`info`, `warning`, `error`, ...) |
| `stream` | containers | `stdout` or `stderr` |

For containers the journal priority only records the stream (Docker stamps
stdout as info and stderr as err), so it is exposed as `stream` rather than
`level`.

Example queries: `{host="homeassistant", container="homeassistant"}` for HA
Core, `{host="homeassistant", container="hassio_supervisor"}` for the
Supervisor.

## Behaviour

- On first start Alloy reads up to 12 hours of journal history; Loki rejects
  entries older than its `reject_old_samples` window, which is harmless.
- The read position is kept in `/data/alloy`, so restarts resume where they
  stopped.
- Alloy's HTTP server listens on 127.0.0.1 inside the container only.

## Updating Alloy

Change the tag and digest of `grafana/alloy` in the Dockerfile together, bump
`version` in config.yaml, and add a CHANGELOG entry. The Supervisor rebuilds
the add-on on update.

## Credit

Adapted from [castlerogers/ha-addon-alloy](https://github.com/castlerogers/ha-addon-alloy)
(MIT License, Copyright (c) 2026 Castle Rogers Homelab).
