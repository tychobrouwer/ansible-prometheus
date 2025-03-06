# Prometheus Ansible Role

This Ansible role installs and configures [Prometheus](https://prometheus.io/), a powerful open-source monitoring and alerting toolkit, on Debian-based systems. It automates the process of downloading, installing, and setting up Prometheus, along with optional configurations such as exporters (e.g., Proxmox VE exporter) and custom templates.

---

## Role Overview

The role performs the following tasks:
- Installs Prometheus on a Debian-based system.
- Configures Prometheus with a user-defined or default configuration file.
- Optionally sets up the Proxmox VE exporter for monitoring Proxmox environments.
- Ensures Prometheus runs as a systemd service for reliability and easy management.

This role is designed to be flexible, allowing users to customize installation paths, architecture, and exporter settings through variables.

---

## Requirements

- **Operating System**: Debian-based system (e.g., Debian 11/12, Ubuntu 20.04/22.04).
- **Ansible**: Version 2.9 or higher.
- **Privileges**: Root or sudo access on the target host(s).
- **Dependencies**: 
  - `wget` or `curl` (for downloading Prometheus binaries).
  - `tar` (for extracting the Prometheus archive).
  - A working systemd installation (default on most modern Debian systems).

No additional Python modules or external roles are required beyond a standard Ansible setup.

---

## Role Variables

The role uses several variables to customize the installation and configuration of Prometheus. Below are the available variables with their defaults (if applicable):

| Variable                          | Description                                                                 | Default Value         |
|-----------------------------------|-----------------------------------------------------------------------------|-----------------------|
| `prometheus_version`             | The version of Prometheus to install.                                       | `2.50.0`             |
| `prometheus_arch`                | Target architecture for Prometheus (e.g., `amd64`, `arm64`).                | `amd64`              |
| `prometheus_dir`                 | Directory where Prometheus will be installed.                               | `/opt`               |
| `prometheus_template`            | Path to a custom Jinja2 template for the Prometheus configuration file.     | `templates/prometheus.yml.j2` |
| `prometheus_pve_exporter`        | Boolean to enable/disable the Proxmox VE exporter.                          | `false`              |
| `prometheus_pve_exporter_port`   | Port for the Proxmox VE exporter to listen on.                              | `9221`               |
| `prometheus_pve_exporter_address`| Bind address for the Proxmox VE exporter.                                   | `127.0.0.1`          |
| `prometheus_pve_verify_ssl`      | Whether to verify SSL when connecting to Proxmox VE.                        | `false`              |
| `prometheus_pve_user`            | Proxmox VE username for authentication (required if exporter is enabled).   | None                 |
| `prometheus_pve_password`        | Proxmox VE password for authentication (required if exporter is enabled).   | None                 |

### Notes:
- Sensitive credentials like `prometheus_pve_user` and `prometheus_pve_password` should be stored in an encrypted format (e.g., using Ansible Vault).
- If `prometheus_pve_exporter` is set to `true`, both `prometheus_pve_user` and `prometheus_pve_password` must be defined.

---

## Example Playbook

Here’s an example Ansible playbook demonstrating how to use this role:

```yaml
---
- hosts: servers
  vars:
    # Proxmox VE exporter credentials
    prometheus_pve_user: "ci@pve"
    prometheus_pve_password: "{{ proxmox_ci_password }}"  # Use a vaulted variable for security
    
    # Optional customizations
    prometheus_version: "2.50.0"
    prometheus_arch: "amd64"
    prometheus_dir: "/opt"
    prometheus_template: "templates/prometheus.yml.j2"
    
    # Proxmox VE exporter settings
    prometheus_pve_exporter: true
    prometheus_pve_exporter_port: 9221
    prometheus_pve_exporter_address: "127.0.0.1"
    prometheus_pve_verify_ssl: false

  roles:
    - role: tychobrouwer.prometheus
```

---

## Installation Steps (Performed by the Role)

1. **Download Prometheus**: Fetches the specified version of Prometheus for the target architecture from the official release page.
2. **Extract and Install**: Extracts the archive to `prometheus_dir` (e.g., `/opt/prometheus`).
3. **Configure Prometheus**: Applies the provided `prometheus_template` to generate `prometheus.yml`.
4. **Set Up Systemd Service**: Creates and enables a systemd service to manage Prometheus.
5. **Optional Exporter**: If `prometheus_pve_exporter` is enabled, installs and configures the Proxmox VE exporter.
6. **Start Service**: Ensures Prometheus and any exporters are running.

---

## Usage Notes

- **Custom Configuration**: To use a custom `prometheus.yml`, create a Jinja2 template (e.g., `templates/prometheus.yml.j2`) in your playbook directory and reference it with `prometheus_template`.
- **Security**: Avoid hardcoding sensitive data (e.g., passwords) in the playbook. Use Ansible Vault or a secrets management tool.
- **Verification**: After running the playbook, verify Prometheus is running by accessing `http://<server-ip>:9090` in a browser.

---

## Author Information

- **Author**: Tycho Brouwer
- **Repository**: [github.com/tychobrouwer/ansible-role-prometheus](https://github.com/tychobrouwer/ansible-role-prometheus).

