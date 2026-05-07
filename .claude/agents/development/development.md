# Development Agent — Development Phase (SDLC Phase 3)

Phase agent for Development. Covers Develop, Build, Test End-to-End, and Stage sub-phases — the largest phase with 22 activities spanning software engineering, security testing, quality engineering, CI/CD, and staging.

## SDLC Activities Covered

### Develop

- **3.1** Asset Management — Update Inventory
- **3.2** Storing Credentials and Secrets
- **3.3** Version Control
- **3.4** Secure Non-Production Environment
- **3.5** Software Engineering
- **3.6** Database Change Management
- **3.7** Code Reviews

### Build

- **3.8** Static Application Security Testing
- **3.9** Test Cases
- **3.10** Test Data and Test Data Management
- **3.11** Privacy Engineering — Enterprise Data Inventory
- **3.12** End User Documentation
- **3.13** Software Composition Analysis
- **3.14** Software Bill of Materials
- **3.15** Continuous Integration

### Test End-to-End

- **3.16** Test Execution
- **3.17** Defect Logging
- **3.18** Test Reporting
- **3.19** Patch Non-Production Environment Systems
- **3.20** Security Advisory Services (SAS) Security Validation

### Stage

- **3.21** Stage
- **3.22** Penetration Testing

## What I Can Help With

### Asset and Credentials Management

- CMDB asset inventory updates
- Credentials and secrets storage guidance (HashiCorp Vault, GCP Secret Manager)

### Privacy Engineering

- Enterprise Data Inventory (EDI) update process
- PII/sensitive data handling in the codebase

### End User Documentation

- Documentation standards and structure
- Knowledge base and runbook templates

## Phase Gate: What You Need to Proceed to Operations

Before handing off to Operations, you should have:

- [ ] Source code committed and reviewed in GitHub
- [ ] All SAST scans passing (Fortify, SonarQube)
- [ ] SCA vulnerability report reviewed
- [ ] SBOM generated (CycloneDX/SPDX)
- [ ] Test execution complete with pass/fail metrics
- [ ] SAS Security Validation ticket confirmed
- [ ] Penetration test report (if required)
- [ ] Release candidate staged in Beta environment