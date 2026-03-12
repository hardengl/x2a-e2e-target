# MIGRATION FROM CHEF/INSPEC TO ANSIBLE

## Executive Summary

This repository contains a combination of Chef InSpec tests and Ansible playbooks that are used together to demonstrate compliance automation. The migration scope is relatively small, focusing primarily on converting InSpec tests to Ansible-compatible testing frameworks while maintaining the existing Ansible playbooks. The repository also contains Chef Automate and Chef Infra Server setup scripts that need to be converted to Ansible playbooks.

Estimated timeline: 1-2 weeks for a complete migration, with minimal complexity due to the limited scope of Chef components.

## Module Migration Plan

This repository contains Chef InSpec tests and Ansible playbooks that need individual migration planning:

### MODULE INVENTORY

- **chef-and-ansible**:
    - Description: Integration of Chef InSpec with Ansible for compliance testing of web servers
    - Path: chef-and-ansible/
    - Technology: Chef InSpec + Ansible
    - Key Features: HTTPS configuration testing, SSL/TLS security validation, web server compliance

- **setup-automate**:
    - Description: Deployment scripts for Chef Automate and Chef Infra Server
    - Path: setup-automate/
    - Technology: Bash scripts for Chef deployment
    - Key Features: Chef Automate installation, Chef Infra Server configuration, user and organization setup

### Infrastructure Files

- `chef-and-ansible/kitchen.yml`: Test Kitchen configuration for Ansible and InSpec integration. Migration considerations include replacing with Ansible Molecule for testing.
- `chef-and-ansible/website_https.yml`: Ansible playbook for configuring Apache with HTTPS. Can be retained as-is in the Ansible migration.
- `chef-and-ansible/poodle_fix.yml`: Ansible playbook for fixing SSL POODLE vulnerability. Can be retained as-is in the Ansible migration.
- `chef-and-ansible/tests/website_https_verify.rb`: InSpec test for verifying HTTPS configuration. Needs migration to Ansible-compatible testing framework.
- `chef-and-ansible/tests/ssh_profile.rb`: InSpec control for SSH security compliance. Needs migration to Ansible-compatible testing framework.
- `setup-automate/deploy-automate.sh`: Bash script for deploying Chef Automate. Needs conversion to Ansible playbook.
- `setup-automate/deploy-chef-server.sh`: Bash script for deploying Chef Infra Server. Needs conversion to Ansible playbook.

### Target Details

Based on the source configuration files:

- **Operating System**: Ubuntu 20.04 (specified in kitchen.yml)
- **Virtual Machine Technology**: Vagrant (specified in kitchen.yml)
- **Cloud Platform**: Not specified, appears to be designed for on-premises or generic cloud VMs

## Migration Approach

### Key Dependencies to Address

- **Test Kitchen (latest)**: Replace with Ansible Molecule for integration testing
- **Chef InSpec (latest)**: Replace with one of the following:
  - Ansible's built-in assert module for basic testing
  - ansible-lint for static analysis
  - Molecule with Testinfra for more comprehensive testing
  - Maintain InSpec as a separate tool if deep compliance testing is required

### Security Considerations

- **SSL/TLS Configuration**: The migration must maintain the security hardening for TLS 1.2 enforcement and disabling of SSL3
- **SSH Security Controls**: The SSH root login restrictions must be preserved in the migrated solution
- **Self-signed Certificates**: The certificate generation process should be maintained with equivalent Ansible modules

### Technical Challenges

- **Compliance Testing Framework**: InSpec provides specialized compliance testing capabilities that may not have direct equivalents in Ansible. Consider using a combination of Ansible assert, Testinfra, and possibly maintaining InSpec as a separate tool.
- **Test Kitchen Integration**: The current setup uses Test Kitchen to orchestrate Ansible and InSpec. This workflow needs to be replaced with Ansible Molecule or another Ansible-native testing approach.

### Migration Order

1. **Ansible Playbooks** (Low risk): Retain existing `website_https.yml` and `poodle_fix.yml` as they are already in Ansible format
2. **Testing Framework** (Moderate complexity): Migrate InSpec tests to Ansible-compatible testing framework
3. **Chef Deployment Scripts** (Moderate complexity): Convert the Chef Automate and Chef Infra Server deployment scripts to Ansible playbooks

### Assumptions

1. The primary purpose of this repository is to demonstrate how Chef InSpec can be used alongside Ansible for compliance testing, rather than being a production infrastructure codebase.
2. The Chef components are limited to InSpec tests and deployment scripts, with no Chef cookbooks or recipes to migrate.
3. The existing Ansible playbooks (`website_https.yml` and `poodle_fix.yml`) can be retained as-is in the migration.
4. The target environment is Ubuntu 20.04 running on Vagrant VMs, but the solution should be adaptable to other environments.
5. The security requirements, particularly around SSL/TLS and SSH configurations, must be maintained in the migrated solution.
6. The repository is primarily used for demonstration and educational purposes, as indicated by the README.md references to white papers and blog content.