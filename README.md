# pulsar_relay (Ansible role)

Deploys [pulsar-relay](https://github.com/mvdbeek/pulsar-relay) as a systemd
service, backed by **Valkey** (Redis-compatible) so credentials and in-flight
messages survive a restart.

The relay is the HTTP broker between Galaxy and a remote Pulsar running in
relay mode: both sides reach it by long-polling, so the compute site needs no
inbound ports and no AMQP broker.

## What it does

- Installs Valkey (snap) when the Valkey backend is selected
- Installs Python 3 + venv and pulsar-relay into a virtualenv
- Renders and enables the `pulsar-relay` systemd unit

## Role variables

See `defaults/main.yml`. Override at least the secrets:

| Variable | Default | Notes |
|----------|---------|-------|
| `pulsar_relay_port` | `9000` | Listen port |
| `pulsar_relay_user` | `ubuntu` | Service user |
| `pulsar_relay_venv` | `/opt/pulsar-relay/venv` | Virtualenv path |
| `pulsar_relay_jwt_secret` | `change_me_in_production` | **override** |
| `pulsar_relay_admin_username` | `admin` | |
| `pulsar_relay_admin_password` | `change_me_in_production` | **override** |
| `pulsar_relay_storage_backend` | `valkey` | `valkey` or `memory` |
| `pulsar_relay_valkey_host` | `localhost` | |
| `pulsar_relay_valkey_port` | `6379` | |

> Supply real secrets via the vault / extra-vars — never commit them.

## Example

```yaml
- hosts: relay
  become: true
  roles:
    - role: pulsar_relay
      vars:
        pulsar_relay_jwt_secret: "{{ vault_pulsar_relay_jwt_secret }}"
        pulsar_relay_admin_password: "{{ vault_pulsar_relay_admin_password }}"
```

## Status

Experimental — pin a pulsar-relay version known to work with your environment.
