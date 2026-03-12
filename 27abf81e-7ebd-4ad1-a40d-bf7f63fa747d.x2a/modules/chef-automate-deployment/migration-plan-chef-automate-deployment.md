# Migration Plan: Chef Automate Deployment

**TLDR**: This module consists of bash scripts that deploy Chef Automate and Chef Infra Server. The scripts set system parameters, download Chef Automate CLI, deploy the products, and create initial users and organizations. No actual Chef cookbook is present, just deployment scripts.

## Service Type and Instances

**Service Type**: Configuration Management Platform (Chef Automate and Chef Infra Server)

**Configured Instances**:

- **Chef Automate**: 
  - Location/Path: Deployed on the local system
  - Port/Socket: Default ports (443 for web UI)
  - Key Config: Accepts terms and MLSA

- **Chef Infra Server**: 
  - Location/Path: Deployed on the local system
  - Port/Socket: Default ports (443 for web UI)
  - Key Config: Integrated with Chef Automate in the first script, standalone in the second script

## File Structure

```
setup-automate/deploy-automate.sh
setup-automate/deploy-chef-server.sh
```

## Module Explanation

The scripts perform operations in this order:

1. **System Configuration** (`setup-automate/deploy-automate.sh` and `setup-automate/deploy-chef-server.sh`):
   - Sets hostname to the configured value (default: 'automate.chef.lab')
   - Configures kernel parameters:
     - vm.max_map_count=262144
     - vm.dirty_expire_centisecs=20000
   - Resources: hostnamectl, sysctl (2)

2. **Chef Automate Installation** (`setup-automate/deploy-automate.sh` and `setup-automate/deploy-chef-server.sh`):
   - Downloads Chef Automate CLI from packages.chef.io
   - Makes the CLI executable
   - Resources: curl, chmod (2)

3. **Product Deployment** (`setup-automate/deploy-automate.sh`):
   - Deploys Chef Automate and Chef Infra Server using the CLI
   - Accepts terms and MLSA
   - Resources: chef-automate deploy (1)

4. **Product Deployment - Server Only** (`setup-automate/deploy-chef-server.sh`):
   - Deploys only Chef Infra Server using the CLI
   - Accepts terms and MLSA
   - Resources: chef-automate deploy (1)

5. **User Creation** (`setup-automate/deploy-automate.sh` and `setup-automate/deploy-chef-server.sh`):
   - Creates a user in Chef Infra Server with:
     - Username: jtonello (configurable)
     - Full name: John Tonello (configurable)
     - Email: jtonello@chef.lab (configurable)
     - Password: password (configurable)
     - Saves user key to jtonello.pem
   - Resources: chef-server-ctl user-create (1)

6. **Organization Creation** (`setup-automate/deploy-automate.sh` and `setup-automate/deploy-chef-server.sh`):
   - Creates an organization in Chef Infra Server with:
     - Short name: lab (configurable)
     - Full name: Chef Lab (configurable)
     - Associates the previously created user
     - Saves organization validator key to lab-validator.pem
   - Resources: chef-server-ctl org-create (1)

## Dependencies

**External cookbook dependencies**: None
**System package dependencies**: curl, sudo, hostnamectl (systemd)
**Service dependencies**: None explicitly defined

## Checks for the Migration

**Files to verify**:
- /etc/hostname (modified by hostnamectl)
- User key file: jtonello.pem (configurable)
- Organization validator key: lab-validator.pem (configurable)
- Chef Automate configuration files (created by the deployment process)

**Service endpoints to check**:
- Ports listening: 443 (Chef Automate UI and Chef Infra Server UI)
- Network interfaces: All interfaces by default

**Templates rendered**: None (no Chef templates used)

## Pre-flight checks:
```bash
# System configuration
hostname
sysctl vm.max_map_count
sysctl vm.dirty_expire_centisecs

# Chef Automate status
sudo chef-automate status

# Chef Infra Server status
sudo chef-server-ctl status

# User verification
sudo chef-server-ctl user-list | grep jtonello
sudo chef-server-ctl user-show jtonello

# Organization verification
sudo chef-server-ctl org-list | grep lab
sudo chef-server-ctl org-show lab

# Key files
ls -la jtonello.pem
ls -la lab-validator.pem

# Network listening
netstat -tulpn | grep 443
ss -tlnp | grep 443

# Web UI access
curl -k https://localhost/api/v0/health

# Log verification
sudo journalctl -u chef-automate
sudo journalctl -u chef-server

# Disk space
df -h /var/opt/chef-automate
df -h /var/opt/chef-server
```