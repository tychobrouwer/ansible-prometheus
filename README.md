Install and configure Prometheus
=========

This role installs and configures Prometheus.

Role Variables
--------------

Requirements
----------------

Example Playbook
----------------

```yaml
- hosts: servers
  vars:
    prometheus_pve_password: "{{ proxmox_ci_password }}"
    prometheus_pve_user: ci@pve

  roles:
    - role: tychobrouwer.prometheus

    - role: tychobrouwer.prometheus
      prometheus_template: templates/prometheus.yml.j2
      prometheus_pve_exporter: true
      prometheus_pve_exporter_port: 9221
      prometheus_pve_verify_ssl: false
      prometheus_arch: amd64
      prometheus_dir: /opt
```

License
-------

BSD

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
