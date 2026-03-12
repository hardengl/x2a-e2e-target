# MIGRATION FROM CHEF/ANSIBLE TO ANSIBLE

## Executive Summary

This repository contains a mix of Chef and Ansible components focused on demonstrating Chef InSpec for compliance automation alongside Ansible. The migration scope is relatively small, primarily involving Chef InSpec tests and Ansible playbooks for configuring web servers with HTTPS. The repository also includes Chef Automate and Chef Infra Server setup scripts. The estimated migration timeline is short (1-2 weeks) due to the limited scope and the fact that most components are already in Ansible format.

## Module Migration Plan

This repository contains Chef InSpec tests and Ansible playbooks that need individual migration planning:

### MODULE INVENTORY

- **website_https**:
    - Description: Ansible playbook for configuring Apache web server with HTTPS using self-signed certificates
    - Path: chef-and-ansible/website_https.yml
    - Technology: Ansible
    - Key Features: Apache configuration, SSL certificate generation, virtual host setup

- **poodle_fix**:
    - Description: Ansible playbook for fixing SSL vulnerabilities in Apache by disabling older protocols
    - Path: chef-and-ansible/poodle_fix.yml
    - Technology: Ansible
    - Key Features: Security hardening, SSL protocol configuration

- **inspec_tests**:
    - Description: Chef InSpec tests for verifying HTTPS configuration and SSH security
    - Path: chef-and-ansible/tests/
    - Technology: Chef InSpec
    - Key Features: HTTPS validation, SSL protocol verification, SSH security compliance checks

- **chef_server_setup**:
    - Description: Bash scripts for deploying Chef Automate and Chef Infra Server
    - Path: setup-automate/
    - Technology: Bash/Chef
    - Key Features: Chef server deployment, user and organization creation

### Infrastructure Files

- `kitchen.yml`: Test Kitchen configuration for running Ansible playbooks and InSpec tests
- `index.html`: Sample HTML file for testing web server deployment
- `README.md`: Documentation files explaining the purpose of the examples

### Target Details

Based on the source repository:

- **Operating System**: Ubuntu 20.04 (explicitly specified in kitchen.yml)
- **Virtual Machine Technology**: Vagrant (specified in kitchen.yml)
- **Cloud Platform**: Not specified, appears to be on-premises or generic cloud VM deployment

## Migration Approach

### Key Dependencies to Address

- **Chef InSpec**: Replace with Ansible-native testing solutions:
  - Option 1: Migrate to Ansible Molecule for testing
  - Option 2: Use ansible-lint for static analysis
  - Option 3: Maintain InSpec as a standalone testing tool that can work with Ansible

- **Test Kitchen**: Replace with:
  - Ansible Molecule for testing infrastructure
  - Or adapt existing kitchen.yml to work with pure Ansible testing

### Security Considerations

- **SSL Configuration**: The migration must maintain the security hardening present in poodle_fix.yml
  - Ensure TLSv1.2 remains enabled and older protocols disabled
  - Maintain proper certificate generation and management

- **SSH Hardening**: The SSH security profile tests must be preserved
  - Migrate the InSpec SSH tests to equivalent Ansible assertions or molecule tests
  - Ensure root login remains disabled as per compliance requirements

- **Self-signed Certificates**: The current implementation uses self-signed certificates
  - Consider enhancing with Let's Encrypt integration for production environments
  - Maintain proper certificate permissions and security

### Technical Challenges

- **InSpec Test Migration**: Converting InSpec tests to equivalent Ansible testing frameworks
  - Challenge: InSpec has specific compliance-focused syntax that may not directly map to Ansible testing tools
  - Mitigation: Consider using ansible-test or maintaining InSpec as a complementary tool

- **Chef Server Setup**: Replacing Chef server deployment scripts with Ansible equivalents
  - Challenge: The Chef server setup scripts are specific to Chef infrastructure
  - Mitigation: This may not need migration if the goal is to move away from Chef infrastructure

### Migration Order

1. **Ansible Playbooks** (website_https.yml, poodle_fix.yml): Low risk as they are already in Ansible format, may only need refinement
2. **Testing Framework**: Migrate InSpec tests to Ansible-compatible testing framework
3. **Chef Server Setup Scripts**: Convert to Ansible roles if Chef infrastructure is still needed

### Assumptions

1. The primary goal is to standardize on Ansible and move away from Chef components
2. The InSpec tests are valuable and need to be preserved in some form
3. The target environment will remain Ubuntu 20.04 or similar Linux distributions
4. Vagrant will continue to be used for development/testing environments
5. The Chef server setup scripts may not need migration if Chef infrastructure is being phased out
6. The security compliance requirements represented in the InSpec tests must be maintained
7. The repository is primarily for demonstration purposes rather than production deployment