# Chef Automate Deployment Role

This Ansible role deploys Chef Automate and Chef Infra Server on a target system.

## Requirements

- Ubuntu 18.04 (Bionic) or Ubuntu 20.04 (Focal)
- Sufficient system resources (minimum 4GB RAM, 2 CPUs, 40GB disk space)
- Internet connectivity to download Chef Automate CLI

## Role Variables

| Variable | Default | Description |
|----------|---------|-------------|
| chef_automate_deployment_type | 'full' | Deployment type: 'full' for Chef Automate + Chef Infra Server, 'server_only' for Chef Infra Server only |
| chef_automate_hostname | 'automate.chef.lab' | Hostname to set on the target system |
| chef_automate_username | 'jtonello' | Username for the initial Chef user |
| chef_automate_longusername | 'John Tonello' | Full name for the initial Chef user |
| chef_automate_useremail | 'jtonello@chef.lab' | Email for the initial Chef user |
| chef_automate_userpassword | 'password' | Password for the initial Chef user (should be overridden) |
| chef_automate_orgname | 'lab' | Short name for the Chef organization |
| chef_automate_longorgname | 'Chef Lab' | Full name for the Chef organization |
| chef_automate_userfilename | "{{ chef_automate_username }}.pem" | Filename for the user's PEM file |
| chef_automate_orgfilename | "{{ chef_automate_orgname }}-validator.pem" | Filename for the organization validator PEM file |

## Dependencies

- ansible.posix collection (for sysctl module)
- community.general collection (for various utilities)

## Example Playbook

```yaml
- hosts: chef_servers
  become: true
  vars:
    chef_automate_hostname: 'chef.example.com'
    chef_automate_username: 'admin'
    chef_automate_longusername: 'Admin User'
    chef_automate_useremail: 'admin@example.com'
    chef_automate_userpassword: 'secure_password'
    chef_automate_orgname: 'example'
    chef_automate_longorgname: 'Example Organization'
  roles:
    - role: chef_automate_deployment
```

## License

Apache-2.0

## Author Information

Chef Migration Team