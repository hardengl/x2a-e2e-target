# Migration Plan: chef-automate-deployment

**TLDR**: This module deploys Chef Automate and/or Chef Infra Server using bash scripts. It configures system settings, downloads the Chef Automate CLI, deploys the specified products, and creates initial user and organization.

## Service Type and Instances

**Service Type**: Application Server (Chef Automate and Chef Infra Server)

**Configured Instances**:
- **Chef Automate**: Management platform for Chef infrastructure
  - Location/Path: Default installation path
  - Port/Socket: 443 (HTTPS)
  - Key Config: Hostname, user credentials, organization details

- **Chef Infra Server**: Chef server component
  - Location/Path: Default installation path
  - Port/Socket: 443 (HTTPS)
  - Key Config: Hostname, user credentials, organization details

## File Structure

```
setup-automate/deploy-automate.sh
setup-automate/deploy-chef-server.sh
```

## Module Explanation

The cookbook performs operations in this order:

1. **deploy-automate.sh** (`setup-automate/deploy-automate.sh`):
   - Sets system hostname to the configured value (e.g., 'automate.chef.lab')
   - Configures kernel parameters:
     - vm.max_map_count=262144
     - vm.dirty_expire_centisecs=20000
   - Downloads Chef Automate CLI tool
   - Deploys Chef Automate AND Chef Infra Server with `--product automate --product infra-server`
   - Creates initial admin user with specified credentials
   - Creates initial organization and associates the admin user
   - Resources: hostnamectl, sysctl (2), curl, chef-automate deploy, chef-server-ctl (2)

2. **deploy-chef-server.sh** (`setup-automate/deploy-chef-server.sh`):
   - Sets system hostname to the configured value (e.g., 'automate.chef.lab')
   - Configures kernel parameters:
     - vm.max_map_count=262144
     - vm.dirty_expire_centisecs=20000
   - Downloads Chef Automate CLI tool
   - Deploys ONLY Chef Infra Server with `--product infra-server`
   - Creates initial admin user with specified credentials
   - Creates initial organization and associates the admin user
   - Resources: hostnamectl, sysctl (2), curl, chef-automate deploy, chef-server-ctl (2)

Both scripts use the same set of variables:
- hostname: The FQDN for the Chef server (default: 'automate.chef.lab')
- username: Admin username (default: 'jtonello')
- longusername: Full name for admin user (default: 'John Tonello')
- useremail: Email for admin user (default: 'jtonello@chef.lab')
- userpassword: Password for admin user (default: 'password')
- orgname: Short organization name (default: 'lab')
- longorgname: Full organization name (default: 'Chef Lab')

The scripts also define two dynamic variables:
- userfilename: Generated from username (e.g., 'jtonello.pem')
- orgfilename: Generated from orgname (e.g., 'lab-validator.pem')

## Dependencies

**External cookbook dependencies**: None (standalone bash scripts)
**System package dependencies**: curl, gunzip, sudo
**Service dependencies**: None explicitly defined

## Checks for the Migration

**Files to verify**:
- /etc/hostname (should contain the configured hostname)
- /proc/sys/vm/max_map_count (should be 262144)
- /proc/sys/vm/dirty_expire_centisecs (should be 20000)
- chef-automate executable in the directory where the script was run
- User PEM file (e.g., jtonello.pem) in the directory where the script was run
- Organization validator PEM file (e.g., lab-validator.pem) in the directory where the script was run

**Service endpoints to check**:
- Ports listening: 443 (HTTPS)
- Network interfaces: All interfaces (0.0.0.0) or specific IP if configured

**Templates rendered**: No templates are used in these scripts

## Pre-flight checks:
```bash
# System hostname check
hostname
hostname -f  # Should match the configured hostname

# Kernel parameter checks
sysctl vm.max_map_count  # Should be 262144
sysctl vm.dirty_expire_centisecs  # Should be 20000

# Chef Automate CLI availability
ls -la chef-automate
./chef-automate version

# Chef Automate status (if deployed)
sudo ./chef-automate status

# Chef Infra Server status
sudo chef-server-ctl status

# User and organization verification
ls -la ${username}.pem  # Should exist
ls -la ${orgname}-validator.pem  # Should exist

# Service status
systemctl status chef-automate
systemctl status chef-server

# Network listening
ss -tulpn | grep :443
curl -k https://localhost/api/_status  # Chef Automate API status check

# User authentication test
knife user list -s https://localhost/organizations/${orgname} -u ${username} -k ${username}.pem

# Organization verification
knife org list -s https://localhost -u ${username} -k ${username}.pem

# Web UI accessibility
curl -k -I https://localhost/  # Should return HTTP 200 OK

# Log checks
sudo journalctl -u chef-automate -n 50
sudo journalctl -u chef-server -n 50
```