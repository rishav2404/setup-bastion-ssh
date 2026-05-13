# Setup Bastion SSH

Configure SSH access to private servers through a bastion/jump host inside GitHub Actions workflows.

This action creates an SSH configuration with `ProxyJump` support and allows secure access to internal/private infrastructure from GitHub-hosted runners.

---

## Features

- Supports bastion/jump host SSH access
- Supports custom SSH ports
- Works with private/internal servers
- Supports configurable strict host checking
- Simple reusable composite GitHub Action
- Compatible with GitHub-hosted runners

---

## Usage

```yaml
name: Deploy Application

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout you code
        uses: actions/checkout@v6

      - name: Setup SSH
        uses: rishav2404/setup-bastion-ssh@v1
        with:
          ssh-private-key: ${{ secrets.SSH_KEY }}

          bastion-host: ${{ secrets.BASTION_HOST }}
          bastion-user: ${{ secrets.BASTION_USER }}
          bastion-port: "9222"

          server-host: ${{ secrets.SERVER_HOST }}
          server-user: ${{ secrets.SERVER_USER }}

      - name: Perform SSH action
        run: |
          ssh target 'cd /xyz && ./deploy.sh'
```

---

## Inputs

| Name                   | Required | Default | Description                             |
| ---------------------- | -------- | ------- | --------------------------------------- |
| `ssh-private-key`      | Yes      | -       | SSH private key used for authentication |
| `bastion-host`         | Yes      | -       | Bastion/jump host IP or hostname        |
| `bastion-user`         | Yes      | -       | SSH username for bastion host           |
| `bastion-port`         | No       | `22`    | SSH port for bastion host               |
| `server-host`          | Yes      | -       | Target/internal server IP or hostname   |
| `server-user`          | Yes      | -       | SSH username for target server          |
| `server-port`          | No       | `22`    | SSH port for target server              |
| `strict-host-checking` | No       | `no`    | SSH StrictHostKeyChecking option        |

---

## SSH Key Setup

Generate SSH key pair:

```bash
ssh-keygen -t ed25519
```

Add the public key to:

```text
~/.ssh/authorized_keys
```

on:

- bastion host
- target server

Store the private key inside GitHub Secrets:

```text
SSH_KEY
```

---

## Example Infrastructure

```text
GitHub Actions Runner
        |
        v
  Bastion Host
        |
        v
 Private Server
```

---

## Security Notes

By default:

```text
StrictHostKeyChecking=no
```

is used for convenience and compatibility with ephemeral CI runners.

For production environments, consider enabling:

```yaml
strict-host-checking: "yes"
```

and managing `known_hosts` validation separately.

---

## Requirements

- SSH access enabled on bastion and target hosts
- Bastion host reachable from internet
- Target server reachable from bastion
- OpenSSH installed on target systems

---

## License

Apache 2.0
