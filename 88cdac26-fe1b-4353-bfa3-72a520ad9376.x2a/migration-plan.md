# MIGRATION FROM CHEF/ANSIBLE TO ANSIBLE

## Executive Summary

This repository contains a mix of Chef and Ansible components that demonstrate how Chef InSpec can be used alongside Ansible for compliance automation. The migration scope is relatively small, focusing on:

1. Chef InSpec tests that need to be migrated to Ansible-compatible testing frameworks
2. Chef Automate and Chef Server deployment scripts that need to be converted to Ansible playbooks
3. Existing Ansible playbooks that need to be reviewed and potentially refactored

The migration complexity is low to moderate, with an estimated timeline of 1-2 weeks for a single developer to complete the migration, including testing and documentation.

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
    - Description: Chef InSpec test that verifies SSH security configuration
    - Path: chef-and-ansible/tests/ssh_profile.rb
    - Technology: Chef InSpec
    - Key Features: SSH root login check, compliance with security standards (SRG-OS-000112)

- **chef-automate-deployment**:
    - Description: Bash script for deploying Chef Automate and Chef Infra Server
    - Path: setup-automate/deploy-automate.sh
    - Technology: Bash/Chef
    - Key Features: Chef Automate installation, user and organization creation

- **chef-server-deployment**:
    - Description: Bash script for deploying Chef Infra Server without Automate
    - Path: setup-automate/deploy-chef-server.sh
    - Technology: Bash/Chef
    - Key Features: Chef Server installation, user and organization creation

### Infrastructure Files

- `kitchen.yml`: Test Kitchen configuration for running Ansible playbooks and InSpec tests
- `index.html`: Simple HTML file used as a test page for the web server

### Target Details

Based on the source repository analysis:

- **Operating System**: Ubuntu 20.04 (explicitly specified in kitchen.yml)
- **Virtual Machine Technology**: Vagrant (specified in kitchen.yml driver)
- **Cloud Platform**: Not specified, but deployment scripts are designed to work on both on-premises and cloud VMs

## Migration Approach

### Key Dependencies to Address

- **Chef InSpec**: Replace with Ansible-native testing solutions:
  - Option 1: Use Ansible's built-in `assert` module for basic testing
  - Option 2: Integrate with Molecule for more comprehensive testing
  - Option 3: Use the ansible-lint tool for static analysis
  - Option 4: Consider maintaining InSpec as a separate testing tool that can work with Ansible

- **Test Kitchen**: Replace with:
  - Option 1: Molecule for Ansible role testing
  - Option 2: A combination of Vagrant and custom test scripts

- **Chef Automate/Server**: Replace deployment scripts with:
  - Ansible roles for configuration management
  - Consider using AWX/Ansible Tower as a replacement for Chef Automate's functionality

### Security Considerations

- **SSL Configuration**: The poodle_fix.yml playbook addresses SSL security. Ensure this security hardening is maintained in the migrated solution.
- **SSH Security**: The ssh_profile.rb InSpec test verifies SSH security. Ensure these checks are maintained in the Ansible-based testing.
- **Self-signed Certificates**: The website_https.yml playbook generates self-signed certificates. Consider using Ansible's crypto modules or integrating with Let's Encrypt for production environments.
- **Hardcoded Credentials**: The Chef deployment scripts contain hardcoded credentials. Use Ansible Vault or another secrets management solution in the migrated playbooks.

### Technical Challenges

- **InSpec to Ansible Testing**: Converting InSpec tests to Ansible-compatible testing frameworks may require different approaches for different test types:
  - For simple state verification, Ansible's `assert` module can be used
  - For more complex compliance testing, consider maintaining InSpec or exploring alternatives like Ansible's `stat` module combined with custom assertions

- **Chef Server Functionality**: If Chef Server is being used for node management and policy application, equivalent functionality needs to be implemented in Ansible:
  - Consider AWX/Ansible Tower for web UI and API
  - Use Ansible inventory for node management
  - Use Ansible roles and collections for policy management

### Migration Order

1. **Existing Ansible Playbooks** (website_https.yml, poodle_fix.yml): Review and refactor these first as they are already in Ansible format and will be easiest to migrate.
2. **InSpec Tests** (website_https_verify.rb, ssh_profile.rb): Convert these to Ansible-compatible testing frameworks.
3. **Chef Deployment Scripts** (deploy-automate.sh, deploy-chef-server.sh): Convert these to Ansible playbooks last, as they may require more significant architectural decisions.

### Assumptions

1. The repository is primarily used for demonstration purposes rather than production deployment, as indicated by the README.md mentioning it's a companion to a white paper.
2. The Chef InSpec tests are used for compliance verification of infrastructure provisioned by Ansible, not Chef-managed infrastructure.
3. There are no additional Chef cookbooks or recipes beyond what's visible in the repository structure.
4. The migration goal is to standardize on Ansible as the sole configuration management tool, potentially replacing Chef Automate/Server with AWX/Ansible Tower.
5. The current setup uses Test Kitchen with Vagrant for local testing, which will need to be replaced with an Ansible-compatible testing framework.
6. The hardcoded credentials in the deployment scripts are for demonstration purposes only and would be replaced with proper secrets management in the migrated solution.