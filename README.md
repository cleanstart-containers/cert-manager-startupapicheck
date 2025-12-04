# CleanStart Cert-Manager Startup API Check Container

The `startupapicheck` tool is a utility from cert-manager that verifies the availability and functionality of the cert-manager API server. This container image provides a security-hardened, production-ready environment for running cert-manager startup API checks in Kubernetes clusters. The CleanStart Cert-Manager-Startupapicheck image provides a production-ready, security-hardened container optimized for enterprise environments. Built on a minimal base OS with comprehensive security hardening, this image delivers reliable application execution with advanced security features.

**📌 CleanStart Foundation:** Security-hardened, minimal base OS designed for enterprise containerized environments.

**Image Path:** `cleanstart/cert-manager-startupapicheck`

**Registry:** CleanStart Registry

---

## Overview

The `startupapicheck` tool performs a comprehensive check of the cert-manager installation by:

- Verifying cert-manager CustomResourceDefinitions (CRDs) are installed
- Checking that cert-manager API resources are available and accessible
- Creating a test CertificateRequest to validate the cert-manager API server functionality
- Confirming the cert-manager webhook is properly configured
- Exiting with appropriate status codes based on check results

This container is typically used as a readiness check or validation step to ensure cert-manager is properly installed and operational before deploying applications that depend on it. This Cert-Manager-Startupapicheck container is part of the CleanStart application suite, featuring enterprise-grade security hardening, automated vulnerability management, and compliance with industry standards.

---

## About CleanStart

CleanStart is a comprehensive container registry providing security-hardened, enterprise-ready container images. Our images are designed with security-first principles, featuring minimal attack surfaces, regular security updates, and compliance with industry standards.

### About CleanStart Images

CleanStart images are built on secure, minimal base operating systems and optimized for production environments. Each image undergoes rigorous security testing, vulnerability scanning, and compliance validation to ensure enterprise-grade security and reliability.

---

## Image Details

- **Base Image**: CleanStart minimal Linux distribution
- **Entrypoint**: `/usr/bin/startupapicheck`
- **User**: `clnstrt` (non-root, UID 1000)
- **Architecture**: amd64
- **OS**: Linux
- **Image Tags**: `cleanstart/cert-manager-startupapicheck:latest-dev`

---

## Key Features

- **Security-First Design**: Built with minimal attack surfaces and security hardening
- **Enterprise Compliance**: Meets industry standards including FIPS, STIG, and CIS benchmarks
- **Regular Updates**: Automated security patches and vulnerability management
- **Multi-Architecture Support**: Available for AMD64 and ARM64 architectures
- **Production Ready**: Optimized for enterprise deployment and scaling
- **Comprehensive Documentation**: Detailed guides and best practices for each image
- Automated cert-manager API validation
- Non-root execution for enhanced security
- Minimal container footprint
- Kubernetes-native integration

---

## Prerequisites

Before using this container, ensure you have:

1. **cert-manager is installed** in your Kubernetes cluster
2. **Kubernetes cluster** is accessible and configured
3. **kubectl** is installed and configured
4. **RBAC permissions** to access cert-manager CRDs and resources (the deployment includes these)

---

## Quick Start

### Pull Latest Image

Download the container image from the registry:
```bash
docker pull cleanstart/cert-manager-startupapicheck:latest
docker pull cleanstart/cert-manager-startupapicheck:latest-dev
```

### Basic Run

Run the container with basic configuration:
```bash
docker run --rm cleanstart/cert-manager-startupapicheck:latest-dev
```

**Note:** Running the container directly requires access to a Kubernetes cluster. The tool needs to connect to the Kubernetes API to perform checks.

### Inspect Image

View image details:
```bash
docker inspect cleanstart/cert-manager-startupapicheck:latest-dev
```

### Production Deployment
```bash
docker run -d --name cert-manager-startupapicheck-prod \
  --read-only \
  --security-opt=no-new-privileges \
  --user 1000:1000 \
  cleanstart/cert-manager-startupapicheck:latest
```

---

## Understanding the Check Process

The `startupapicheck check api` command:

1. Connects to the Kubernetes API server
2. Verifies cert-manager CRDs are installed
3. Creates a test CertificateRequest resource
4. Waits for cert-manager to process the request
5. Verifies the API server responds correctly
6. Cleans up the test resource
7. Exits with status code 0 if successful, or non-zero if cert-manager is not ready

---

## Expected Behavior

- **Success**: The tool prints "The cert-manager API is ready" and exits with code 0
- **Failure**: The tool prints an error message and exits with a non-zero code
- **Pod Status**: When deployed as a Deployment, the pod will show `CrashLoopBackOff` after the check completes (this is expected - the tool exits after completion)

---

## Use Cases

Typical scenarios where this container excels:

- **Pre-deployment validation**: Verify cert-manager is ready before deploying applications
- **Health checks**: Use as a readiness probe in CI/CD pipelines
- **Troubleshooting**: Diagnose cert-manager installation issues
- **Integration testing**: Validate cert-manager functionality in test environments
- Automated cert-manager readiness validation
- CI/CD pipeline integration
- Post-installation verification

---

## Architecture Support

CleanStart images support multiple architectures to ensure compatibility across different deployment environments:

- **AMD64**: Intel and AMD x86-64 processors
- **ARM64**: ARM-based processors including Apple Silicon and ARM servers

### Architecture-based Pull Commands
```bash
docker pull --platform linux/amd64 cleanstart/cert-manager-startupapicheck:latest
docker pull --platform linux/arm64 cleanstart/cert-manager-startupapicheck:latest
```

---

## Resources

- **Official Documentation:** https://cert-manager.io/docs/
- **Cert-Manager GitHub Repository:** https://github.com/cert-manager/cert-manager
- **Provenance / SBOM / Signature:** https://images.cleanstart.com/images/cert-manager-startupapicheck
- **Docker Hub:** https://hub.docker.com/r/cleanstart/cert-manager-startupapicheck
- **CleanStart All Images:** https://images.cleanstart.com
- **CleanStart Community Images:** https://hub.docker.com/u/cleanstart

---

## Vulnerability Disclaimer

CleanStart offers Docker images that include third-party open-source libraries and packages maintained by independent contributors. While CleanStart maintains these images and applies industry-standard security practices, it cannot guarantee the security or integrity of upstream components beyond its control.

Users acknowledge and agree that open-source software may contain undiscovered vulnerabilities or introduce new risks through updates. CleanStart shall not be liable for security issues originating from third-party libraries, including but not limited to zero-day exploits, supply chain attacks, or contributor-introduced risks.

**Security remains a shared responsibility:** CleanStart provides updated images and guidance where possible, while users are responsible for evaluating deployments and implementing appropriate controls.
