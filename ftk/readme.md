# FTK Agent Installation Playbook

This repository hosts an Ansible role that provisions the FTK agent on Red Hat Enterprise Linux 7, 8, and 9. The role handles storage preparation, package/key distribution, configuration deployment, and service management in a repeatable manner tailored for enterprise estates.

## Prerequisites

- Ansible 2.14+ executed from a control node with SSH reachability to the managed hosts.
- Managed hosts running RHEL 7, 8, or 9 with `lvm2` and `systemd`.
- Access to the FTK RPM artifacts and PGP public keys. Place them under the `ansible/roles/ftk/files/` tree following the structure described below.

## Repository Layout

```
ansible/
└── roles/
    └── ftk/
        ├── defaults/
        │   └── main.yml
        ├── handlers/
        │   └── main.yml
        ├── tasks/
        │   └── main.yml
        ├── templates/
        │   └── agent.config.j2
        └── files/
            ├── ftk.crt
            └── rhel{7,8,9}/
                ├── AD_Linux_RPM_Public.pgp
                └── ftk-agent_linux.rpm
```

## Role Highlights

- Validates the platform is a supported RHEL major release.
- Ensures at least 1&nbsp;GB free space in `appvg` (falling back to `rootvg`) and creates `/opt/ftk`.
- Confirms `/var/log` has sufficient free space prior to installation.
- Imports the appropriate GPG key and stages the FTK RPM per RHEL version.
- Deploys certificate and configuration, installs the agent, corrects service permissions.
- Stops/disables `enlinuxpo` (if present) and starts `agentcored`.

## Usage

1. Update inventory with target hosts and ensure the role files (PGP keys, RPMs, certificate, configuration template variables) are populated.
2. Create a playbook such as:

```yaml
- name: Deploy FTK agent
  hosts: ftk_targets
  gather_facts: true
  roles:
    - role: ftk
      vars:
        ftk_agent_configuration: |
          # Customize FTK agent configuration values here
          server_url = https://ftk.example.com
          node_name = {{ inventory_hostname }}
```

3. Execute the playbook:

```bash
ansible-playbook -i inventory.ini deploy-ftk.yml
```

## Customization

- Modify `ansible/roles/ftk/defaults/main.yml` to adjust VG names, filesystem parameters, or additional services to disable.
- Override `ftk_agent_configuration` with specific agent settings.
- Extend tasks or handlers as required by your operational standards.

## Contributing

1. Fork the repository and create a feature branch.
2. Implement and test changes (Molecule is recommended for role testing).
3. Submit a pull request describing your updates.

For more details about the repository and future updates, visit the GitHub project page: [Plateform-Linux](https://github.com/mukesh3710/Plateform-Linux)^[https://github.com/mukesh3710/Plateform-Linux.git].

