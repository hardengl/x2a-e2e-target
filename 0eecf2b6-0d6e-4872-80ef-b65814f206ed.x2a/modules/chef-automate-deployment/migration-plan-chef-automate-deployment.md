# Migration Plan: Chef Automate Deployment

**TLDR**: This module consists of Bash scripts that deploy Chef Automate and Chef Infra Server. The scripts set system parameters, download the Chef Automate CLI, deploy the products, and create initial users and organizations. No actual Chef cookbook is present - these are deployment scripts for Chef's own infrastructure components.

## Service Type and Instances

**Service Type**: Application Server (Chef Automate and Chef Infra Server)

**Configured Instances**:

- **Chef Automate**: 
  - Location/Path: Installed on the local system
  - Port/Socket: Default ports (443 for web UI)
  - Key Config: Accepts terms and MLSA

- **Chef Infra Server**:
  - Location/Path: Installed on the local system
  - Port/Socket: Default ports (443 for web UI)
  - Key Config: Integrated with Chef Automate in the main script

## File Structure

```
setup-automate/deploy-automate.sh
setup-automate/deploy-chef-server.sh
```

## Module Explanation

The scripts perform operations in this order:

1. **System Configuration** (`setup-automate/deploy-automate.sh` and `setup-automate/deploy-chef-server.sh`):
   - Sets the system hostname to the configured value (default: 'automate.chef.lab')
   - Configures kernel parameters:
     - vm.max_map_count=262144
     - vm.dirty_expire_centisecs=20000
   - Resources: hostnamectl command, sysctl command (2)

2. **Chef Automate Installation** (`setup-automate/deploy-automate.sh` and `setup-automate/deploy-chef-server.sh`):
   - Downloads Chef Automate CLI from packages.chef.io
   - Makes the CLI executable
   - Resources: curl command, chmod command (2)

3. **Product Deployment** (`setup-automate/deploy-automate.sh`):
   - Deploys Chef Automate and Chef Infra Server using the CLI
   - Accepts terms and MLSA
   - Resources: chef-automate deploy command (1)

4. **Product Deployment (Server Only)** (`setup-automate/deploy-chef-server.sh`):
   - Deploys only Chef Infra Server using the CLI
   - Accepts terms and MLSA
   - Resources: chef-automate deploy command (1)

5. **User and Organization Setup** (`setup-automate/deploy-automate.sh` and `setup-automate/deploy-chef-server.sh`):
   - Creates a user with the configured username, name, email, and password
   - Creates an organization with the configured name
   - Associates the user with the organization
   - Generates and saves user and organization PEM files
   - Resources: chef-server-ctl user-create command, chef-server-ctl org-create command (2)

## Dependencies

**External cookbook dependencies**: None
**System package dependencies**: curl, sudo, hostnamectl (systemd)
**Service dependencies**: None explicitly defined

## Checks for the Migration

**Files to verify**:
- /etc/chef-server (Chef Server configuration directory)
- /etc/chef (Chef client configuration directory)
- User PEM file (e.g., jtonello.pem)
- Organization validator PEM file (e.g., lab-validator.pem)

**Service endpoints to check**:
- Ports listening: 443 (HTTPS for Chef Automate and Chef Infra Server web UIs)
- Network interfaces: All interfaces (0.0.0.0)

**Templates rendered**: None (scripts don't use templates)

## Pre-flight checks:
```bash
# System configuration
hostname
sysctl vm.max_map_count
sysctl vm.dirty_expire_centisecs

# Chef Automate status
sudo chef-automate status

# Chef Server status
sudo chef-server-ctl status

# User verification
sudo chef-server-ctl user-list | grep jtonello
ls -la jtonello.pem
sudo chef-server-ctl user-show jtonello

# Organization verification
sudo chef-server-ctl org-list | grep lab
ls -la lab-validator.pem
sudo chef-server-ctl org-show lab

# Service status
systemctl status chef-automate
systemctl status chef-server

# Network listening
netstat -tulpn | grep 443
ss -tlnp | grep 443
curl -k https://localhost

# Web UI access
curl -k https://automate.chef.lab/api/_status
curl -k https://automate.chef.lab/organizations/lab

# Log verification
sudo journalctl -u chef-automate -n 50
sudo journalctl -u chef-server -n 50
sudo tail -f /var/log/chef-server/nginx/access.log
sudo tail -f /var/log/chef-server/nginx/error.log

# API verification (using the created user's PEM)
knife user list -s https://automate.chef.lab/organizations/lab -u jtonello -k jtonello.pem
knife ssl check -s https://automate.chef.lab/organizations/lab -u jtonello -k jtonello.pem
```