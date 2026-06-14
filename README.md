# 🔐 Trivy Security Scanning & Vulnerability Detection

[![Security](https://img.shields.io/badge/Focus-Container%20%26%20Infrastructure%20Security-red?style=flat-square)](https://github.com/sairam-bathini/trivy-security-scanning)
[![Tool](https://img.shields.io/badge/Tool-Trivy-darkgreen?style=flat-square)](https://aquasecurity.github.io/trivy/)
[![Docker](https://img.shields.io/badge/Docker-Image%20Scanning-blue?style=flat-square)](https://www.docker.com/)
[![IaC](https://img.shields.io/badge/IaC-Config%20Scanning-purple?style=flat-square)](https://www.terraform.io/)

## 📋 Problem Statement

Container images and Infrastructure-as-Code (IaC) files often contain unpatched dependencies, misconfigurations, and security vulnerabilities that expose organizations to attacks. Traditional manual security reviews are inefficient. This project demonstrates how to automate vulnerability detection using Trivy, enabling organizations to shift security left in their DevOps pipelines.

## 🎯 What It Does

Trivy is a comprehensive vulnerability scanner that detects:

### Container Image Scanning
- **OS Package Vulnerabilities** - CVEs in Alpine, Debian, Ubuntu, etc.
- **Application Dependencies** - NPM, Python, Java, Ruby, Go packages
- **Secrets Detection** - Hardcoded credentials, API keys, tokens
- **Misconfigurations** - Docker security issues, secrets in ENV

### Infrastructure-as-Code Scanning
- **Terraform Misconfigurations** - AWS, Azure, GCP security issues
- **Kubernetes Manifests** - Pod security, RBAC violations
- **CloudFormation Templates** - IAM policy issues
- **Docker Compose Files** - Service security configuration

### Key Features:
✅ **Fast & Accurate** - Scans 500 layers in seconds  
✅ **Low False Positives** - Industry-leading accuracy  
✅ **Multiple Output Formats** - JSON, SARIF, SBOM, CycloneDX  
✅ **CI/CD Integration** - GitHub Actions, GitLab CI, Jenkins, etc.  
✅ **Risk-based Filtering** - Suppress low-risk findings  
✅ **Compliance Reports** - FIPS, PCI-DSS, HIPAA validation  

## 🛠️ Tech Stack

| Component | Purpose | Version |
|-----------|---------|---------|
| **Trivy** | Vulnerability scanner | 0.42+ |
| **Docker** | Container runtime | 20.10+ |
| **Aqua Security** | CVE database | Latest |
| **Terraform** | IaC examples | 1.0+ |
| **Kubernetes** | K8s manifests | 1.24+ |

## 🚀 How to Run

### Option 1: Install Trivy Natively

```bash
# macOS (Homebrew)
brew install aquasecurity/trivy/trivy

# Ubuntu/Debian
sudo apt-get install trivy

# Windows (Scoop)
scoop install trivy

# Verify installation
trivy version
```

### Option 2: Use Docker Image

```bash
# Pull latest Trivy image
docker pull aquasec/trivy:latest

# Run scan (Docker)
docker run --rm \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v $PWD:/root \
  aquasec/trivy:latest image node:14-alpine
```

### Option 3: GitHub Actions

```yaml
name: Trivy Security Scan
on: [push, pull_request]

jobs:
  trivy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Run Trivy vulnerability scanner
        uses: aquasecurity/trivy-action@master
        with:
          scan-type: 'fs'
          scan-ref: '.'
          format: 'sarif'
          output: 'trivy-results.sarif'
      
      - name: Upload Trivy results to GitHub Security
        uses: github/codeql-action/upload-sarif@v2
        with:
          sarif_file: 'trivy-results.sarif'
```

## 📊 Scanning Examples

### 1. Scan Docker Image

```bash
# Scan public image (hello-world - should be clean)
trivy image hello-world

# Scan vulnerable image
trivy image node:14-alpine

# Scan with detailed output
trivy image -v node:14-alpine

# Scan and generate JSON report
trivy image -f json -o report.json node:14-alpine

# Scan with severity filter
trivy image --severity HIGH,CRITICAL node:14-alpine

# Scan local image
docker build -t my-app:latest .
trivy image my-app:latest
```

### 2. Scan Terraform/IaC Files

```bash
# Scan all configs in directory
trivy fs --security-checks vuln,config ./terraform

# Scan specific Kubernetes manifest
trivy fs --security-checks config deployment.yaml

# Scan with custom policy
trivy fs --config trivy.yaml ./infrastructure

# Generate compliance report
trivy fs --compliance pci-dss ./aws-config
```

### 3. Generate SBOM (Software Bill of Materials)

```bash
# Generate CycloneDX SBOM
trivy image --format cyclonedx -o sbom.json node:14

# Generate SPDX SBOM
trivy image --format spdx-json -o sbom.spdx.json node:14
```

### 4. Suppress Known Vulnerabilities

Create `.trivyignore` file:
```yaml
# CVE-2021-12345  # Brief description
# Expiration: 2026-12-31  # Optional expiration

# Or by specific package
AVD-AQ-1234 node_modules/package-name
```

## 📈 Sample Scan Results

### Example 1: Node Alpine (3 Vulnerabilities Found)

```
node:14-alpine
==============

OS Packages (Alpine Linux v3.12)
================================
HIGH: CVE-2021-33193 libcurl 
 - Package: curl [7.74.0-r0]
 - Severity: HIGH
 - CVSS Score: 7.5
 - Description: curl and libcurl up to version 7.73.0 are vulnerable to an...
 - Fixed in: 7.74.0-r1

MEDIUM: CVE-2021-22911 openssl
 - Package: openssl [1.1.1g-r13]
 - Severity: MEDIUM
 - CVSS Score: 5.3
 - Fixed in: 1.1.1h-r0

Application Dependencies (npm)
==============================
HIGH: 2 vulnerabilities in node_modules
 - Severity: HIGH (1), MEDIUM (1)
 - Most recent: lodash@4.17.19
```

### Example 2: Terraform Misconfigurations (5 Issues Found)

```
Terraform (aws_s3_bucket)
==========================
HIGH: S3 bucket does not have logging enabled
 - File: main.tf:12-18
 - Severity: HIGH
 - Description: S3 buckets should have logging enabled...
 - Remediation: Add logging_configuration block

MEDIUM: S3 bucket versioning is disabled
 - File: main.tf:12-18
 - Severity: MEDIUM
 - Description: Versioning should be enabled for data protection
 - Remediation: Set versioning { enabled = true }

MEDIUM: S3 bucket is publicly accessible
 - File: main.tf:20-25
 - Severity: HIGH
 - Description: S3 bucket should not be publicly accessible
 - Remediation: Block public access
```

## 🔐 Security Impact

### Risk Matrix

| Scan Type | Vulnerabilities Detected | CVSS Range | Business Impact |
|-----------|------------------------|------------|-----------------|
| **OS Packages** | 2-15 per image | 5.0-9.8 | RCE, data breach |
| **Dependencies** | 3-20 per app | 4.0-9.9 | Supply chain attack |
| **Secrets** | 0-5 per scan | 10.0 | Full infrastructure compromise |
| **IaC Misconfig** | 5-30 per template | 4.0-9.0 | Unauthorized access, data loss |
| **RBAC Issues** | 2-8 per cluster | 6.0-9.0 | Privilege escalation |

### Vulnerability Categories by Severity

```
CRITICAL 🔴  (CVSS 9.0-10.0)
├─ Remote Code Execution (RCE)
├─ Arbitrary File Write
└─ Complete System Compromise

HIGH 🟠      (CVSS 7.0-8.9)
├─ Privilege Escalation
├─ Authentication Bypass
└─ Sensitive Data Exposure

MEDIUM 🟡    (CVSS 4.0-6.9)
├─ Information Disclosure
├─ Denial of Service
└─ Weak Encryption

LOW 🟢       (CVSS 0.1-3.9)
├─ Code Quality Issues
└─ Minor Configuration Issues
```

## 🏆 Best Practices

### 1. Image Building & Scanning

```dockerfile
# Dockerfile
FROM alpine:3.16 as scanner
COPY . /app
RUN trivy fs --exit-code 1 --severity HIGH /app

FROM node:18-alpine
WORKDIR /app
COPY --from=scanner /app .
RUN npm install --production
CMD ["node", "index.js"]
```

### 2. CI/CD Integration

```yaml
# GitHub Actions
- name: Scan with Trivy
  run: |
    trivy image \
      --exit-code 1 \
      --severity HIGH,CRITICAL \
      my-registry/my-app:${{ github.sha }}
```

### 3. Registry Scanning

```bash
# Scan all images in registry
trivy image registry.example.com/my-org/*
```

### 4. Compliance & Reporting

```bash
# PCI-DSS Compliance Check
trivy fs --compliance pci-dss ./infrastructure > pci-report.json

# Generate SBOM for procurement
trivy image --format cyclonedx myapp:latest > sbom.json
```

## 📚 Project Structure

```
trivy-security-scanning/
├── configs/
│   ├── terraform/           # Terraform examples with issues
│   ├── kubernetes/          # K8s manifests to scan
│   └── docker-compose.yml   # Docker Compose examples
├── scripts/
│   ├── scan-image.sh        # Container image scanning
│   ├── scan-iac.sh          # Infrastructure scanning
│   └── generate-sbom.sh     # SBOM generation
├── .trivyignore             # Vulnerability suppressions
├── trivy.yaml               # Trivy configuration
└── README.md
```

## 🔧 Configuration

### trivy.yaml

```yaml
# Security checks to perform
security-checks:
  - vuln        # Vulnerability scanning
  - config      # Misconfiguration detection
  - secret      # Secret detection

# Severity levels
severity:
  - HIGH
  - CRITICAL

# Output format
format: sarif

# Cache settings
cache-dir: /tmp/trivy-cache

# Custom policies
skip-files:
  - "*.test.js"
  
skip-dirs:
  - node_modules
  - vendor
```

## 🤝 Contributing

Contributions welcome:
- Add more scanning examples
- Improve remediation guides
- Add policy templates
- Enhance documentation

## 📞 Support

- **Trivy Docs:** [aquasecurity.github.io/trivy](https://aquasecurity.github.io/trivy/)
- **Issues:** [GitHub Issues](https://github.com/sairam-bathini/trivy-security-scanning/issues)
- **CVE Database:** [NVD](https://nvd.nist.gov/)

## 📄 License

MIT License - Educational and Production Use

---

**For Recruiters:** This project demonstrates:
- ✅ Container security expertise
- ✅ Infrastructure-as-Code security knowledge
- ✅ DevSecOps pipeline implementation
- ✅ Vulnerability management at scale
- ✅ Compliance and audit readiness

**Last Updated:** 2026-06-14 | **Maintained by:** [@sairam-bathini](https://github.com/sairam-bathini)
