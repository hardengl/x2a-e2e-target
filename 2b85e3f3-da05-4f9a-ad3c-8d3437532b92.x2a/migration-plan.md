# MIGRATION FROM CHEF/ANSIBLE TO ANSIBLE

## Executive Summary

This repository contains a mix of Chef and Ansible components focused on demonstrating Chef InSpec for compliance automation alongside Ansible. The migration scope is relatively small, primarily involving Chef InSpec tests and Ansible playbooks for configuring web servers with HTTPS. The repository also includes Chef Automate and Chef Infra Server setup scripts. The estimated migration timeline is short (1-2 weeks) due to the limited scope and the fact that most components are already in Ansible format.

## Module Migration Plan

This repository contains Chef InSpec tests and Ansible playbooks that need individual migration planning:

### MODULE INVENTORY

- **website-https**:
    - Description: Ansible playbook for configuring Apache web server with HTTPS using self-signed certificates
    - Path: chef-and-ansible/website_https.yml
    - Technology: Ansible
    - Key Features: Apache configuration, SSL certificate generation, virtual host setup

- **poodle-fix**:
    - Description: Ansible playbook for remediating SSL POODLE vulnerability in Apache
    - Path: chef-and-ansible/poodle_fix.yml
    - Technology: Ansible
    - Key Features: Disables vulnerable SSL protocols, enables TLSv1.2

- **inspec-website-tests**:
    - Description: Chef InSpec tests for verifying HTTPS website functionality and security
    - Path: chef-and-ansible/tests/website_https_verify.rb
    - Technology: Chef InSpec
    - Key Features: Port listening checks, HTTPS response validation, SSL protocol verification

- **inspec-ssh-profile**:
    - Description: Chef InSpec profile for SSH security compliance checking
    - Path: chef-and-ansible/tests/ssh_profile.rb
    - Technology: Chef InSpec
    - Key Features: SSH root login verification, compliance with security standards (SRG-OS-000112)

- **chef-automate-setup**:
    - Description: Bash script for deploying Chef Automate and Chef Infra Server
    - Path: setup-automate/deploy-automate.sh
    - Technology: Bash/Chef
    - Key Features: Chef Automate installation, user and organization creation

- **chef-server-setup**:
    - Description: Bash script for deploying Chef Infra Server without Automate
    - Path: setup-automate/deploy-chef-server.sh
    - Technology: Bash/Chef
    - Key Features: Chef Infra Server installation, user and organization creation

### Infrastructure Files

- `kitchen.yml`: Test Kitchen configuration for running Ansible playbooks and InSpec tests
- `index.html`: Sample HTML file for web server testing

### Target Details

Based on the source repository analysis:

- **Operating System**: Ubuntu 20.04 (specified in kitchen.yml)
- **Virtual Machine Technology**: Vagrant (specified in kitchen.yml)
- **Cloud Platform**: Not specified, appears to be on-premises or generic cloud VM setup

## Migration Approach

### Key Dependencies to Address

- **Chef InSpec**: Replace with Ansible-native testing solutions:
  - Option 1: Migrate to Ansible Molecule for testing
  - Option 2: Use ansible-lint for static analysis
  - Option 3: Keep InSpec tests but integrate with Ansible using ansible_inspec_callback

- **Test Kitchen**: Replace with Ansible-native testing frameworks:
  - Option 1: Migrate to Ansible Molecule for environment provisioning and testing
  - Option 2: Use simple Vagrant or Docker-based testing with direct Ansible commands

- **Chef Automate/Infra Server**: Replace with Ansible automation platform:
  - Option 1: Migrate to AWX/Ansible Tower for web UI and job scheduling
  - Option 2: Use Ansible Semaphore for lightweight project management
  - Option 3: Use GitLab CI/CD or Jenkins for Ansible automation

### Security Considerations

- **SSL Configuration**: The current playbooks configure Apache with SSL. Ensure the Ansible roles maintain or improve the security posture:
  - Maintain TLSv1.2 requirement
  - Consider adding TLSv1.3 support
  - Ensure strong cipher suites

- **SSH Security**: The InSpec profile checks SSH root login settings. Ensure Ansible:
  - Applies the same SSH hardening
  - Documents compliance requirements
  - Provides equivalent validation

- **Self-signed Certificates**: The current implementation uses self-signed certificates. Consider:
  - Maintaining this approach for development/testing
  - Adding support for proper CA-signed certificates in production
  - Implementing certificate rotation

### Technical Challenges

- **InSpec to Ansible Testing**: Converting InSpec tests to Ansible-native testing will require:
  - Mapping InSpec resources to Ansible modules
  - Recreating test logic in Ansible syntax
  - Ensuring equivalent coverage and reporting

- **Chef Server Replacement**: Replacing Chef Server functionality with Ansible alternatives:
  - User management and authentication
  - Role-based access control
  - Inventory management
  - Secret management

### Migration Order

1. **Ansible Playbooks** (website_https.yml, poodle_fix.yml): Low risk as they're already in Ansible format; simply refactor to follow best practices
2. **InSpec Tests**: Moderate complexity; convert to Ansible testing framework
3. **Chef Server/Automate Setup**: Higher complexity; replace with Ansible automation platform

### Assumptions

1. The primary purpose of this repository is demonstrating Chef InSpec with Ansible rather than production deployment
2. The target environment is Ubuntu 20.04 running on Vagrant VMs
3. Self-signed certificates are acceptable for the web server configuration
4. No external dependencies or data sources are required beyond what's in the repository
5. No complex state management or database configurations are needed
6. The SSH profile is intended for compliance checking only, not remediation
7. The Chef Automate/Server setup scripts are for demonstration purposes and not part of a larger Chef ecosystem
8. No custom Chef resources or complex Chef-specific logic needs migration