# Changelog

## 1.0.0

- First release. Grafana Alloy v1.19.2 reads the HAOS systemd journal and
  pushes it to Loki's push API. Labels: `host`, `job`, `container` (HA Core,
  Supervisor, add-ons), `service` (host units), `level` (non-container
  severity) and `stream` (container stdout/stderr).
- Adapted from castlerogers/ha-addon-alloy (MIT).
