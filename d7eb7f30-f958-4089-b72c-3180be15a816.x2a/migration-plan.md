# MIGRATION FROM CHEF TO ANSIBLE

## Executive Summary

This repository contains a combination of Chef InSpec tests and Ansible playbooks that are used for compliance automation. The primary focus appears to be demonstrating how Chef InSpec can be used alongside Ansible for continuous compliance. The migration scope is relatively small, focusing on converting Chef InSpec tests to Ansible-native solutions while preserving the existing Ansible playbooks.

The migration complexity is **LOW** as the repository primarily contains:
1. Ansible playbooks that are already in the target format
2. Chef InSpec tests that need to be converted to Ansible-native solutions
3. Chef server deployment scripts that need to be converted to Ansible roles

Estimated timeline: 1-2 weeks for a complete migration, including testing and documentation.

## Module Migration Plan

This repository contains Chef InSpec tests and Ansible playbooks that need individual migration planning:

### MODULE INVENTORY

- **website_https**:
    - Description: Ansible playbook that sets up an Apache web server with HTTPS support using self-signed certificates
    - Path: chef-and-ansible/website_https.yml
    - Technology: Ansible
    - Key Features: Apache configuration, SSL certificate generation, virtual host setup

- **poodle_fix**:
    - Description: Ansible playbook that fixes SSL vulnerabilities in Apache by disabling SSLv3 and enabling only TLSv1.2
    - Path: chef-and-ansible/poodle_fix.yml
    - Technology: Ansible
    - Key Features: Security hardening for Apache SSL configuration

- **ssh_profile**:
    - Description: Chef InSpec profile that checks SSH configuration for security compliance
    - Path: chef-and-ansible/tests/ssh_profile.rb
    - Technology: Chef InSpec
    - Key Features: Verification of SSH root login settings, compliance with security standards

- **website_https_verify**:
    - Description: Chef InSpec tests to verify HTTPS website functionality and security
    - Path: chef-and-ansible/tests/website_https_verify.rb
    - Technology: Chef InSpec
    - Key Features: Port listening checks, HTTPS response validation, SSL protocol security verification

- **chef-automate-deployment**:
    - Description: Bash scripts for deploying Chef Automate and Chef Infra Server
    - Path: setup-automate/deploy-automate.sh, setup-automate/deploy-chef-server.sh
    - Technology: Bash scripts for Chef deployment
    - Key Features: Chef Automate installation, Chef Server configuration, user and organization setup

### Infrastructure Files

- `kitchen.yml`: Test Kitchen configuration for testing Ansible playbooks with InSpec verification
- `index.html`: Sample HTML file used for website testing

### Target Details

Based on the source repository analysis:

- **Operating System**: Ubuntu 20.04 (explicitly specified in kitchen.yml)
- **Virtual Machine Technology**: Vagrant (specified in kitchen.yml driver)
- **Cloud Platform**: Not specified, appears to be platform-agnostic with potential for both on-premises and cloud deployment

## Migration Approach

### Key Dependencies to Address

- **Chef InSpec**: Replace with Ansible-native solutions:
  - For SSH compliance checks: Use Ansible's `ansible-lint` or migrate to Ansible Molecule with testinfra
  - For website HTTPS verification: Use Ansible's `uri` module in verification playbooks or migrate to Ansible Molecule with testinfra

- **Test Kitchen**: Replace with Ansible Molecule for testing infrastructure

- **Chef Automate/Server**: Replace deployment scripts with Ansible roles for configuration management systems

### Security Considerations

- **SSL Configuration**: The migration must preserve the security hardening in the poodle_fix.yml playbook
  - Maintain TLSv1.2 requirement and disable insecure protocols
  - Ensure proper certificate handling

- **SSH Hardening**: Preserve the SSH security checks from the InSpec profile
  - Convert to Ansible security role or include in verification playbooks

- **Secrets Management**: 
  - The current setup uses hardcoded passwords in deployment scripts
  - Migrate to Ansible Vault for secure credential storage

### Technical Challenges

- **InSpec to Ansible Testing**: Converting InSpec tests to equivalent Ansible verification methods
  - Solution: Use Ansible's assert module or migrate to Molecule with testinfra for testing

- **Compliance Reporting**: InSpec provides compliance reporting that needs an equivalent in Ansible
  - Solution: Consider integrating with Ansible Tower/AWX for compliance reporting or use community modules

### Migration Order

1. **Ansible Playbooks** (website_https.yml, poodle_fix.yml): Low risk as they're already in Ansible format
   - Review and optimize existing playbooks
   - Convert any embedded variables to use Ansible Vault for secrets

2. **InSpec Tests** (ssh_profile.rb, website_https_verify.rb): Medium complexity
   - Convert to Ansible verification playbooks or Molecule tests
   - Ensure equivalent coverage of security checks

3. **Chef Deployment Scripts** (deploy-automate.sh, deploy-chef-server.sh): Higher complexity
   - Create Ansible roles for deploying alternative configuration management systems
   - Implement secure credential handling

### Assumptions

1. The primary purpose of this repository is to demonstrate Chef InSpec with Ansible rather than being a production deployment
2. The existing Ansible playbooks are functioning correctly and don't require significant changes
3. There are no additional Chef cookbooks or resources not visible in the repository structure
4. The target environment will continue to be Ubuntu 20.04 or compatible systems
5. The migration will replace Chef InSpec with Ansible-native testing solutions
6. The Chef Automate and Chef Server deployment will be replaced with equivalent configuration management systems or removed if not needed