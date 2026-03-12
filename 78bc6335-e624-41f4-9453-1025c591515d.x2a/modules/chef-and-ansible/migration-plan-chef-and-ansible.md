# Migration Plan: chef-and-ansible

**TLDR**: This repository doesn't contain a Chef cookbook to migrate. It's actually a demonstration of using Chef InSpec for compliance testing alongside existing Ansible playbooks. The repository contains Ansible playbooks (website_https.yml, poodle_fix.yml) and InSpec test files (website_https_verify.rb, ssh_profile.rb).

## Service Type and Instances

**Service Type**: Web Server (Apache)

**Configured Instances**:
- **Apache HTTPS Server**: 
  - Location/Path: /var/www/helloworld
  - Port/Socket: 443 (HTTPS)
  - Key Config: SSL enabled with self-signed certificate, TLSv1.2 protocol

## File Structure

```
chef-and-ansible/website_https.yml
chef-and-ansible/poodle_fix.yml
chef-and-ansible/tests/website_https_verify.rb
chef-and-ansible/tests/ssh_profile.rb
chef-and-ansible/kitchen.yml
chef-and-ansible/README.md
chef-and-ansible/index.html
```

## Module Explanation

This repository demonstrates using Chef InSpec for compliance testing alongside Ansible, rather than containing a Chef cookbook to migrate. The components work together as follows:

1. **website_https.yml** (`chef-and-ansible/website_https.yml`):
   - Installs Apache 2.4.41 web server package
   - Installs supporting packages: curl, openssl, python3-openssl
   - Creates SSL certificate directory at /etc/apache2/certs
   - Generates self-signed SSL certificate using OpenSSL
   - Configures Apache virtual host for HTTPS on port 443
   - Deploys a simple "Hello World" website to /var/www/helloworld
   - Enables SSL module in Apache
   - Resources: apt (3), file (2), openssl_privatekey (1), openssl_csr (1), openssl_certificate (1), copy (2), command (3), service (2)

2. **poodle_fix.yml** (`chef-and-ansible/poodle_fix.yml`):
   - Addresses the POODLE vulnerability by updating SSL configuration
   - Modifies /etc/apache2/mods-available/ssl.conf to disable SSLv3 and only enable TLSv1.2
   - Restarts Apache and SSH services after changes
   - Resources: replace (1), service (2)

3. **InSpec Tests**:
   - **website_https_verify.rb** (`chef-and-ansible/tests/website_https_verify.rb`):
     - Verifies port 443 is listening
     - Checks HTTPS response returns status 200 and contains "Hello, world!"
     - Confirms SSLv3 is disabled (POODLE mitigation)
     - Confirms TLSv1.2 is enabled
   
   - **ssh_profile.rb** (`chef-and-ansible/tests/ssh_profile.rb`):
     - Implements a security control to verify SSH root login is disabled
     - Checks /etc/ssh/sshd_config for PermitRootLogin setting
     - Includes compliance metadata (STIG IDs, CCI references)

4. **Test Kitchen Configuration** (`chef-and-ansible/kitchen.yml`):
   - Configures Vagrant as the driver
   - Uses Ansible as the provisioner to run website_https.yml
   - Uses InSpec as the verifier to run website_https_verify.rb tests
   - Targets Ubuntu 20.04 platform

## Dependencies

**External cookbook dependencies**: None (this is not a Chef cookbook)
**System package dependencies**: apache2, curl, openssl, python3-openssl
**Service dependencies**: apache2, sshd

## Checks for the Migration

**Files to verify**:
- /etc/apache2/sites-available/helloworld.conf
- /etc/apache2/sites-enabled/helloworld.conf
- /var/www/helloworld/index.html
- /etc/apache2/certs/apache.key
- /etc/apache2/certs/apache.crt
- /etc/apache2/certs/apache.csr
- /etc/apache2/mods-available/ssl.conf

**Service endpoints to check**:
- Ports listening: 443 (HTTPS)
- Network interfaces: All interfaces (*)

**Templates rendered**:
- Apache virtual host configuration (inline template in website_https.yml)
- HTML content (inline template in website_https.yml)

## Pre-flight checks:
```bash
# Service status
systemctl status apache2
systemctl status sshd

# Apache configuration validation
apache2ctl configtest
apache2ctl -M | grep ssl  # Should show ssl_module

# SSL certificate verification
openssl x509 -in /etc/apache2/certs/apache.crt -text -noout
openssl verify -CAfile /etc/apache2/certs/apache.crt /etc/apache2/certs/apache.crt

# SSL protocol verification
openssl s_client -connect localhost:443 -ssl3  # Should fail
openssl s_client -connect localhost:443 -tls1_2  # Should succeed

# Website accessibility
curl -k https://localhost/  # Should return 200 and contain "Hello, world!"

# Configuration checks
grep "SSLProtocol" /etc/apache2/mods-available/ssl.conf  # Should show "SSLProtocol -all +TLSv1.2"
grep "PermitRootLogin" /etc/ssh/sshd_config  # Should not be "yes"

# Virtual host configuration
cat /etc/apache2/sites-available/helloworld.conf
ls -la /etc/apache2/sites-enabled/  # Should show helloworld.conf symlink

# Network listening
netstat -tulpn | grep :443
ss -tlnp | grep :443
lsof -i :443

# Logs
tail -f /var/log/apache2/error.log
tail -f /var/log/apache2/access.log
```