# MIGRATION FROM CHEF INSPEC AND ANSIBLE TO ANSIBLE

## Executive Summary

This repository contains a combination of Ansible playbooks and Chef InSpec tests that demonstrate how to use Chef InSpec for compliance testing alongside Ansible for configuration management. The migration scope is relatively small, focusing on converting the InSpec tests to Ansible-native testing solutions while preserving the existing Ansible playbooks. The repository also contains Chef Automate and Chef Server deployment scripts that need to be converted to Ansible playbooks.

Estimated timeline: 1-2 weeks for a single developer, considering the limited scope and complexity.

## Module Migration Plan

This repository contains Chef InSpec tests and Ansible playbooks that need individual migration planning:

### MODULE INVENTORY

- **website_https**:
    - Description: Ansible playbook that configures an Apache web server with HTTPS support using self-signed certificates
    - Path: chef-and-ansible/website_https.yml
    - Technology: Ansible
    - Key Features: Apache configuration, SSL certificate generation, virtual host setup

- **poodle_fix**:
    - Description: Ansible playbook that addresses the POODLE vulnerability by disabling SSLv3 and enabling only TLSv1.2 in Apache
    - Path: chef-and-ansible/poodle_fix.yml
    - Technology: Ansible
    - Key Features: Security hardening, SSL/TLS configuration

- **website_https_verify**:
    - Description: Chef InSpec test that verifies the HTTPS website configuration
    - Path: chef-and-ansible/tests/website_https_verify.rb
    - Technology: Chef InSpec
    - Key Features: Port listening check, HTTPS response validation, SSL/TLS protocol verification

- **ssh_profile**:
    - Description: Chef InSpec test that verifies SSH security configuration (root login disabled)
    - Path: chef-and-ansible/tests/ssh_profile.rb
    - Technology: Chef InSpec
    - Key Features: SSH configuration validation, security compliance check

- **chef-automate-deployment**:
    - Description: Bash script for deploying Chef Automate and Chef Infra Server
    - Path: setup-automate/deploy-automate.sh
    - Technology: Bash/Chef
    - Key Features: Chef Automate installation, Chef Server configuration, user and organization setup

- **chef-server-deployment**:
    - Description: Bash script for deploying Chef Infra Server without Automate
    - Path: setup-automate/deploy-chef-server.sh
    - Technology: Bash/Chef
    - Key Features: Chef Server installation, user and organization setup

### Infrastructure Files

- `kitchen.yml`: Test Kitchen configuration for running Ansible playbooks and InSpec tests. Will need to be replaced with Ansible-native testing framework configuration.
- `index.html`: Sample HTML content for the web server. Can be preserved as-is or incorporated into Ansible templates.

### Target Details

Based on the source repository analysis:

- **Operating System**: Ubuntu 20.04 (specified in kitchen.yml)
- **Virtual Machine Technology**: Vagrant (specified in kitchen.yml)
- **Cloud Platform**: Not specified, appears to be platform-agnostic with potential for on-premises or cloud deployment

## Migration Approach

### Key Dependencies to Address

- **Chef InSpec**: Replace with Ansible-native testing solutions:
  - Option 1: Use Ansible's `assert` module for basic validation
  - Option 2: Implement Molecule for more comprehensive testing
  - Option 3: Use pytest-ansible for Python-based testing

- **Test Kitchen**: Replace with Molecule for Ansible role testing

### Security Considerations

- **SSL/TLS Configuration**: The migration must preserve the security hardening in the POODLE fix playbook
- **SSH Hardening**: The SSH security checks must be implemented in Ansible
- **Self-signed Certificates**: The certificate generation process should be preserved in Ansible
- **Credentials in Scripts**: The Chef deployment scripts contain hardcoded credentials that should be moved to Ansible Vault or another secure storage solution

### Technical Challenges

- **InSpec to Ansible Testing**: Converting InSpec's declarative testing syntax to Ansible's procedural approach may require additional logic
- **Chef Server Deployment**: Creating an equivalent Ansible playbook for Chef Server deployment will require understanding of Chef Server architecture and configuration

### Migration Order

1. **Ansible Playbooks** (website_https.yml, poodle_fix.yml): Low risk as they are already in Ansible format, only need review and potential optimization
2. **InSpec Tests** (website_https_verify.rb, ssh_profile.rb): Medium complexity, need conversion to Ansible-native testing
3. **Chef Deployment Scripts** (deploy-automate.sh, deploy-chef-server.sh): High complexity, need complete rewrite as Ansible playbooks

### Assumptions

1. The existing Ansible playbooks are functioning correctly and don't require significant modifications
2. The target environment will continue to be Ubuntu 20.04 or compatible
3. Vagrant will continue to be used for development/testing environments
4. The Chef Automate and Chef Server deployment scripts are intended to be converted to Ansible rather than preserved as-is
5. No additional Chef cookbooks or resources are required beyond what's visible in the repository
6. The InSpec tests are comprehensive and cover all necessary compliance checks
7. No external dependencies or integrations exist beyond what's documented in the repository

## Detailed Migration Steps

### 1. Ansible Playbooks

The existing Ansible playbooks (website_https.yml and poodle_fix.yml) can be preserved with minimal changes:

- Review for best practices and optimization opportunities
- Consider converting to Ansible roles for better organization
- Update any deprecated syntax or modules

### 2. InSpec Tests to Ansible Tests

For each InSpec test:

1. **website_https_verify.rb**:
   - Create equivalent tests using Ansible's `uri` module to check HTTP status and content
   - Use Ansible's `wait_for` module to check port listening status
   - Implement custom module or script for SSL/TLS protocol verification

2. **ssh_profile.rb**:
   - Create equivalent test using Ansible's `lineinfile` or `command` module to check SSH configuration
   - Implement compliance metadata (tags, descriptions) using Ansible variables or comments

### 3. Chef Deployment Scripts to Ansible Playbooks

Create new Ansible playbooks to replace the Chef deployment scripts:

1. **chef-automate-ansible.yml**:
   - Implement hostname configuration
   - Configure system parameters (vm.max_map_count, vm.dirty_expire_centisecs)
   - Download and install Chef Automate and Chef Infra Server
   - Create users and organizations
   - Store credentials securely using Ansible Vault

2. **chef-server-ansible.yml**:
   - Similar to above but only for Chef Infra Server

### 4. Testing Framework

Replace Test Kitchen with Molecule:

1. Create Molecule configuration for testing Ansible roles
2. Implement scenario-based testing
3. Configure Vagrant driver for local testing

## Conclusion

This migration involves preserving existing Ansible playbooks while converting Chef InSpec tests to Ansible-native testing solutions and rewriting Chef deployment scripts as Ansible playbooks. The scope is relatively small, and the complexity is moderate, making this a straightforward migration project.