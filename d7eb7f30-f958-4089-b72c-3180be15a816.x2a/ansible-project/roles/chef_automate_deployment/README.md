# Chef Automate Deployment Role

This role deploys Chef Automate and/or Chef Infra Server on a target system.

## Requirements

- Ubuntu 18.04 (bionic) or Ubuntu 20.04 (focal)
- Sufficient system resources as per Chef Automate requirements
- Internet connectivity to download Chef Automate CLI

## Role Variables

| Variable | Default | Description |
|----------|---------|-------------|
| chef_automate_deploy_automate | true | Whether to deploy Chef Automate |
| chef_automate_deploy_chef_server | true | Whether to deploy Chef Infra Server |
| chef_automate_cli_path | "/tmp" | Path where Chef Automate CLI will be extracted |
| chef_automate_hostname | "automate.chef.lab" | System hostname for Chef Automate/Server |
| chef_automate_username | "jtonello" | Admin username |
| chef_automate_longusername | "John Tonello" | Admin full name |
| chef_automate_useremail | "jtonello@chef.lab" | Admin email |
| chef_automate_userpassword | "password" | Admin password |
| chef_automate_orgname | "lab" | Organization short name |
| chef_automate_longorgname | "Chef Lab" | Organization full name |

## Dependencies

- ansible.posix collection for system configuration
- community.general collection for additional utilities

## Example Playbook

```yaml
- hosts: chef_servers
  roles:
    - role: chef_automate_deployment
      vars:
        chef_automate_hostname: "chef.example.com"
        chef_automate_username: "admin"
        chef_automate_userpassword: "secure_password"
        chef_automate_orgname: "example"
        chef_automate_longorgname: "Example Organization"
```

## License

Apache-2.0

## Author Information

Ansible Migration Team