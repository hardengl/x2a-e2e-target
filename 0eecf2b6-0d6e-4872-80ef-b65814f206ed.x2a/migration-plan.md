# MIGRATION FROM CHEF INSPEC AND ANSIBLE TO ANSIBLE

## Executive Summary

This repository contains a combination of Chef InSpec tests and Ansible playbooks that are used together for compliance automation. The migration scope is relatively small, focusing on converting Chef InSpec tests to Ansible-native solutions while preserving the existing Ansible playbooks. The repository also contains Chef Automate and Chef Infra Server deployment scripts that need to be converted to Ansible playbooks.

Estimated timeline: 1-2 weeks for a single developer, considering the limited scope and complexity.

## Module Migration Plan

This repository contains Chef InSpec tests and Ansible playbooks that need individual migration planning:

### MODULE INVENTORY

- **website_https**:
    - Description: Ansible playbook that sets up an Apache web server with HTTPS support using self-signed certificates
    - Path: chef-and-ansible/website_https.yml
    - Technology: Ansible
    - Key Features: Apache2 installation, SSL certificate generation, virtual host configuration

- **poodle_fix**:
    - Description: Ansible playbook that fixes SSL configuration in Apache to mitigate POODLE vulnerability
    - Path: chef-and-ansible/poodle_fix.yml
    - Technology: Ansible
    - Key Features: Disables SSLv3 and enables only TLSv1.2

- **website_https_verify**:
    - Description: Chef InSpec test that verifies HTTPS functionality and security
    - Path: chef-and-ansible/tests/website_https_verify.rb
    - Technology: Chef InSpec
    - Key Features: Port listening check, HTTPS response validation, SSL protocol verification

- **ssh_profile**:
    - Description: Chef InSpec control that ensures SSH root login is disabled for security compliance
    - Path: chef-and-ansible/tests/ssh_profile.rb
    - Technology: Chef InSpec
    - Key Features: SSH configuration validation, STIG compliance check

- **chef-automate-deployment**:
    - Description: Bash script to deploy Chef Automate and Chef Infra Server
    - Path: setup-automate/deploy-automate.sh
    - Technology: Bash script
    - Key Features: Chef Automate installation, Chef Infra Server installation, user and organization creation

- **chef-server-deployment**:
    - Description: Bash script to deploy Chef Infra Server without Automate
    - Path: setup-automate/deploy-chef-server.sh
    - Technology: Bash script
    - Key Features: Chef Infra Server installation, user and organization creation

### Infrastructure Files

- `kitchen.yml`: Test Kitchen configuration for running Ansible playbooks and InSpec tests
- `index.html`: Sample HTML file for the web server

### Target Details

Based on the source repository analysis:

- **Operating System**: Ubuntu 20.04 (explicitly specified in kitchen.yml)
- **Virtual Machine Technology**: Vagrant (specified in kitchen.yml driver)
- **Cloud Platform**: Not specified, but deployment scripts suggest on-premises or generic cloud VM usage

## Migration Approach

### Key Dependencies to Address

- **Chef InSpec**: Replace with Ansible-native solutions:
  - For website_https_verify.rb: Use Ansible's uri module and assert module to verify HTTPS functionality
  - For ssh_profile.rb: Use Ansible's assert module with lineinfile or template module to verify SSH configuration

- **Test Kitchen**: Replace with Ansible Molecule for testing Ansible roles and playbooks

### Security Considerations

- **SSL/TLS Configuration**: The poodle_fix.yml playbook specifically addresses SSL security. Ensure this security fix is preserved in the migration.
- **SSH Security**: The ssh_profile.rb InSpec test verifies SSH security configurations. Ensure these checks are maintained in the Ansible migration.
- **Self-signed Certificates**: The website_https.yml playbook generates self-signed certificates. Consider implementing proper certificate management in production.
- **Hardcoded Credentials**: The Chef deployment scripts contain hardcoded credentials. These should be replaced with Ansible Vault or another secure secret management solution.

### Technical Challenges

- **InSpec to Ansible Testing**: Converting InSpec tests to Ansible assertions will require careful mapping of test functionality.
  - Mitigation: Use Ansible's assert module combined with command/shell modules to replicate InSpec's testing capabilities.

- **Chef Automate Deployment**: Converting the Chef Automate deployment scripts to Ansible will require understanding of Chef Automate's installation process.
  - Mitigation: Break down the deployment scripts into discrete tasks and create equivalent Ansible tasks.

### Migration Order

1. Convert InSpec tests to Ansible assertions (low risk, preserves compliance checks)
2. Create Ansible role for Chef Automate deployment (moderate complexity)
3. Create Ansible role for Chef Infra Server deployment (moderate complexity)
4. Update documentation and testing framework (low complexity)

### Assumptions

1. The existing Ansible playbooks (website_https.yml and poodle_fix.yml) will be preserved as-is, with only the testing framework changing.
2. The Chef InSpec tests are used primarily for compliance validation and can be replaced with equivalent Ansible assertions.
3. The deployment scripts for Chef Automate and Chef Infra Server are intended to be run on a fresh Ubuntu 20.04 system.
4. The hardcoded credentials in the deployment scripts are for demonstration purposes only and would be replaced in a production environment.
5. The repository is primarily used for demonstration and educational purposes, as indicated by the README.md files.