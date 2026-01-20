# Ansible Role: Attack Range Splunk SOAR

This role installs and configures Splunk SOAR (formerly Phantom) for use with [Attack Range](https://github.com/splunk/attack_range). The role handles the complete installation process including system preparation, SOAR installation, and initial password configuration.

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

```
ar_soar_password: Pl3ase-k1Ll-me:p
ar_soar_setup_user: "{{ ansible_user | default('ec2-user') }}"
ar_soar_phantom_user: phantom
```

- `ar_soar_password`: Password for the Splunk SOAR admin user (default: `Pl3ase-k1Ll-me:p`)
- `ar_soar_setup_user`: Username used for initial setup and file extraction. Automatically uses `ansible_user` if available, otherwise defaults to `ec2-user`
- `ar_soar_phantom_user`: Username under which Splunk SOAR runs (default: `phantom`)

## Dependencies

None.

## Requirements

- The Splunk SOAR application archive must be available at `../../apps/{{ phantom_server.phantom_app }}` relative to the playbook location
- The `phantom_server.phantom_app` variable must be defined (typically in your playbook or inventory)
- Target system must be running a RHEL/CentOS/Amazon Linux distribution (uses `yum` package manager)

## Example Playbook

```yaml
- hosts: soar_servers
  vars:
    phantom_server:
      phantom_app: "splunk-soar-4.10.0.tar.gz"
    ar_soar_password: "MySecurePassword123!"
    ar_soar_setup_user: ubuntu
    ar_soar_phantom_user: soar
  roles:
    - P4T12ICK.ar_soar
```

## What This Role Does

This role performs the following tasks:

1. **Updates all packages**: Updates all system packages using `yum` to ensure the latest versions are installed
2. **Copies Splunk SOAR archive**: Extracts the Splunk SOAR application archive to the setup user's home directory
3. **Creates SOAR directory**: Creates `/opt/soar` directory for SOAR installation
4. **Prepares system**: Runs `soar-prepare-system` to prepare the system for SOAR installation
5. **Copies SOAR files**: Copies the SOAR files to the phantom user's home directory
6. **Sets ownership**: Changes ownership of SOAR files to the phantom user
7. **Installs SOAR**: Runs the SOAR installation script as the phantom user
8. **Configures admin password**: Changes the default admin password using the SOAR REST API

## Customization

### Using Different Usernames

The role supports different usernames for different environments:

```yaml
# For Ubuntu/Debian systems
ar_soar_setup_user: ubuntu

# For RHEL/CentOS systems
ar_soar_setup_user: centos

# For custom SOAR user
ar_soar_phantom_user: mysoaruser
```

The `ar_soar_setup_user` variable automatically detects the Ansible connection user (`ansible_user`) if available, making it work seamlessly across different environments without manual configuration.

### Custom Password

Set a custom password for the SOAR admin user:

```yaml
ar_soar_password: "YourSecurePassword123!"
```

## Post-Installation

After installation, Splunk SOAR will be available at:
- **Web Interface**: `https://<server-ip>:8443`
- **Default Admin User**: `soar_local_admin`
- **Password**: Value from `ar_soar_password` variable

## License

Apache License 2.0

## Author Information

This role was created by [P4T12ICK](https://github.com/P4T12ICK)
