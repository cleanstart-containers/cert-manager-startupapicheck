# CleanStart Cert-Manager Startup API Check Container

**CleanStart Container for cert-manager startupapicheck**

The `startupapicheck` tool is a utility from cert-manager that verifies the availability and functionality of the cert-manager API server. This container image provides a security-hardened, production-ready environment for running cert-manager startup API checks in Kubernetes clusters.

**CleanStart Foundation**: Security-hardened, minimal base OS designed for enterprise containerized environments.

## Overview

The `startupapicheck` tool performs a comprehensive check of the cert-manager installation by:

- Verifying cert-manager CustomResourceDefinitions (CRDs) are installed
- Checking that cert-manager API resources are available and accessible
- Creating a test CertificateRequest to validate the cert-manager API server functionality
- Confirming the cert-manager webhook is properly configured
- Exiting with appropriate status codes based on check results

This container is typically used as a readiness check or validation step to ensure cert-manager is properly installed and operational before deploying applications that depend on it.

## Image Details

- **Base Image**: CleanStart minimal Linux distribution
- **Entrypoint**: `/usr/bin/startupapicheck`
- **User**: `clnstrt` (non-root, UID 1000)
- **Architecture**: amd64
- **OS**: Linux
- **Image Tags**: `cleanstart/cert-manager-startupapicheck:latest-dev`

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

Prerequisites:

1. **cert-manager is installed** in your Kubernetes cluster
2. **Kubernetes cluster** is accessible and configured
3. **kubectl** is installed and configured
4. **RBAC permissions** to access cert-manager CRDs and resources (the deployment includes these)

## Understanding the Check Process

The `startupapicheck check api` command:

1. Connects to the Kubernetes API server
2. Verifies cert-manager CRDs are installed
3. Creates a test CertificateRequest resource
4. Waits for cert-manager to process the request
5. Verifies the API server responds correctly
6. Cleans up the test resource
7. Exits with status code 0 if successful, or non-zero if cert-manager is not ready

## Expected Behavior

- **Success**: The tool prints "The cert-manager API is ready" and exits with code 0
- **Failure**: The tool prints an error message and exits with a non-zero code
- **Pod Status**: When deployed as a Deployment, the pod will show `CrashLoopBackOff` after the check completes (this is expected - the tool exits after completion)

## Use Cases

- **Pre-deployment validation**: Verify cert-manager is ready before deploying applications
- **Health checks**: Use as a readiness probe in CI/CD pipelines
- **Troubleshooting**: Diagnose cert-manager installation issues
- **Integration testing**: Validate cert-manager functionality in test environments

