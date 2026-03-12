# MIGRATION FROM CHEF/ANSIBLE TO ANSIBLE

## Executive Summary

This repository contains a mix of Chef InSpec tests and Ansible playbooks that are used for compliance automation. The primary focus appears to be demonstrating how Chef InSpec can be used alongside Ansible for compliance testing. The migration scope is relatively small, with only a few Ansible playbooks and InSpec tests to migrate. The estimated timeline for migration is 1-2 weeks, with low complexity.

## Module Migration Plan

This repository contains Chef InSpec tests and Ansible playbooks that need individual migration planning:

### MODULE INVENTORY

- **website_https**:
    - Description: Ansible playbook that sets up an Apache web server with HTTPS support using self-signed certificates
    - Path: chef-and-ansible/website_https.yml
    - Technology: Ansible
    - Key Features: Apache2 installation, SSL certificate generation, virtual host configuration

- **poodle_fix**:
    - Description: Ansible playbook that fixes SSL configuration in Apache to mitigate the POODLE vulnerability
    - Path: chef-and-ansible/poodle_fix.yml
    - Technology: Ansible
    - Key Features: Disables SSLv3 and enables only TLSv1.2

- **website_https_verify**:
    - Description: Chef InSpec test that verifies HTTPS functionality and security
    - Path: chef-and-ansible/tests/website_https_verify.rb
    - Technology: Chef InSpec
    - Key Features: Port listening check, HTTPS response validation, SSL protocol verification

- **ssh_profile**:
    - Description: Chef InSpec profile that checks SSH security configuration
    - Path: chef-and-ansible/tests/ssh_profile.rb
    - Technology: Chef InSpec
    - Key Features: SSH root login verification, compliance with security standards

- **chef-server-deployment**:
    - Description: Bash script for deploying Chef Infra Server
    - Path: setup-automate/deploy-chef-server.sh
    - Technology: Bash/Chef
    - Key Features: Chef Server installation, user and organization creation

- **automate-deployment**:
    - Description: Bash script for deploying Chef Automate and Chef Infra Server
    - Path: setup-automate/deploy-automate.sh
    - Technology: Bash/Chef
    - Key Features: Chef Automate installation, Chef Server installation, user and organization creation

### Infrastructure Files

- `kitchen.yml`: Test Kitchen configuration for running Ansible playbooks and InSpec tests
- `README.md`: Documentation for the repository and examples

### Target Details

Based on the source repository analysis:

- **Operating System**: Ubuntu 20.04 (explicitly specified in kitchen.yml)
- **Virtual Machine Technology**: Vagrant (specified in kitchen.yml driver)
- **Cloud Platform**: Not specified, appears to be on-premises or generic cloud VMs

## Migration Approach

### Key Dependencies to Address

- **Chef InSpec**: Replace with Ansible-compatible testing frameworks:
  - Option 1: Use Ansible's built-in `assert` module for basic tests
  - Option 2: Integrate with Molecule for more comprehensive testing
  - Option 3: Use pytest-ansible for Python-based testing

- **Test Kitchen**: Replace with:
  - Molecule for Ansible role testing
  - Ansible-specific CI/CD pipelines

### Security Considerations

- **SSL Configuration**: The poodle_fix.yml playbook addresses SSL security by disabling SSLv3 and enabling only TLSv1.2. This security practice should be maintained in the migrated Ansible playbooks.
  
- **SSH Security**: The ssh_profile.rb InSpec test checks for secure SSH configuration. This should be implemented as an Ansible task or role that configures SSH securely and verifies the configuration.

- **Self-signed Certificates**: The website_https.yml playbook generates self-signed certificates. In production, consider using Let's Encrypt or another trusted certificate authority.

- **Hardcoded Credentials**: The Chef server deployment scripts contain hardcoded credentials. These should be replaced with Ansible Vault or another secure secrets management solution.

### Technical Challenges

- **InSpec to Ansible Testing**: Converting InSpec tests to Ansible-compatible testing frameworks will require understanding the testing logic and implementing equivalent checks in Ansible.
  - Mitigation: Use Ansible's assert module for simple tests and consider Molecule for more complex testing scenarios.

- **Chef Server Deployment**: The Chef server deployment scripts need to be converted to Ansible roles or playbooks.
  - Mitigation: Create an Ansible role that installs and configures Chef Server, or consider if Chef Server is still needed after migration.

### Migration Order

1. **website_https.yml** (already in Ansible format, low risk)
2. **poodle_fix.yml** (already in Ansible format, low risk)
3. **InSpec Tests** (convert to Ansible-compatible testing, moderate complexity)
4. **Chef Server Deployment Scripts** (convert to Ansible roles/playbooks, higher complexity)

### Assumptions

1. The primary goal is to migrate all functionality to pure Ansible without relying on Chef components.
2. The InSpec tests are used for compliance verification and need to be replaced with equivalent Ansible-based testing.
3. The Chef server deployment scripts are used for setting up a Chef infrastructure, which may not be needed after migration to Ansible.
4. The target environment will continue to be Ubuntu 20.04 running on Vagrant VMs.
5. The security requirements (SSL configuration, SSH security) will remain the same.
6. No additional Chef cookbooks or recipes are present beyond what's visible in the repository.
7. The migration will maintain the same level of compliance testing and security verification.