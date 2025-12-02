# Cert-Manager Startup API Check - Kubernetes Deployment

This directory contains Kubernetes manifests to deploy and test the `cleanstart/cert-manager-startupapicheck:latest-dev` image.

## Overview

The `startupapicheck` tool is a utility from cert-manager that verifies the availability and functionality of the cert-manager API server. This deployment allows you to test the CleanStart cert-manager-startupapicheck container image in a Kubernetes environment.

## What This Deployment Includes

- **Namespace**: `cert-manager-startupapicheck` - Isolated environment for testing
- **ServiceAccount**: `startupapicheck-sa` - Identity for the pod
- **ClusterRole & ClusterRoleBinding**: Permissions to check cert-manager CRDs and resources
- **Deployment**: Runs the startupapicheck container to test functionality

**Note on Pod Status:** The `startupapicheck` tool completes its check and exits. Since this is a Deployment (not a Job), Kubernetes will restart the pod when it exits, causing the pod to show `CrashLoopBackOff` status. This is **expected behavior** - the check is succeeding (you'll see "The cert-manager API is ready" in the logs), but the container exits after completion. For production use, consider using a Job instead of a Deployment for one-time checks.

## Prerequisites

- Kubernetes cluster (v1.19+)
- `kubectl` configured to access your cluster
- **cert-manager installed** in your cluster (required for the tool to function)
- RBAC permissions to create namespaces, deployments, service accounts, cluster roles, and cluster role bindings

## Deployment Steps

### Step 1: Verify cert-manager is Installed

Before deploying, ensure cert-manager is installed in your cluster:

```bash
# Check if cert-manager namespace exists
kubectl get namespace cert-manager

# Check if cert-manager pods are running
kubectl get pods -n cert-manager

# Verify cert-manager CRDs are installed
kubectl get crd | grep cert-manager.io
```

If cert-manager is not installed, install it first:

```bash
# Install cert-manager (example for v1.13.0)
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.13.0/cert-manager.yaml

# Wait for cert-manager to be ready
kubectl wait --for=condition=ready pod -l app.kubernetes.io/instance=cert-manager -n cert-manager --timeout=300s
```

### Step 2: Deploy the Startup API Check

Apply the deployment manifest:

```bash
kubectl apply -f deployment.yaml
```

### Step 3: Verify Deployment

Check that the deployment was created successfully:

```bash
# Check namespace
kubectl get namespace cert-manager-startupapicheck

# Check deployment status
kubectl get deployment -n cert-manager-startupapicheck -w

# Check pod status
kubectl get pods -n cert-manager-startupapicheck -w
```

### Step 4: Check Pod Logs

View the logs to see the startupapicheck tool output:

```bash
# Get pod name
POD_NAME=$(kubectl get pods -n cert-manager-startupapicheck -l app=cert-manager-startupapicheck -o jsonpath='{.items[0].metadata.name}')

# View logs
kubectl logs -n cert-manager-startupapicheck $POD_NAME

# Follow logs in real-time
kubectl logs -n cert-manager-startupapicheck $POD_NAME -f
```

### Step 5: Verify Functionality

The startupapicheck tool runs with the `check api` command and should:
- Successfully connect to the Kubernetes API
- Check for cert-manager CustomResourceDefinitions (CRDs)
- Verify cert-manager API resources are available
- Exit with status code 0 if cert-manager is properly installed

**Success Indicator:** When the check succeeds, you should see the message:
```
The cert-manager API is ready
```

Check the pod exit status:

```bash
# Check pod status
kubectl get pods -n cert-manager-startupapicheck -l app=cert-manager-startupapicheck

# Describe pod for detailed status
kubectl describe pod -n cert-manager-startupapicheck -l app=cert-manager-startupapicheck
```

**Note:** The `startupapicheck` tool runs the `check api` command and exits after completion (with exit code 0 on success). Since this is a Deployment, Kubernetes will automatically restart the pod when it exits, which causes the pod to show `CrashLoopBackOff` status even though the check is succeeding. This is **expected behavior** - the tool completes successfully, prints "The cert-manager API is ready", and then exits.

**Important:** 
- The pod showing `CrashLoopBackOff` is normal - it means the check completed and the container exited
- Check the logs to verify success: `kubectl logs -n cert-manager-startupapicheck -l app=cert-manager-startupapicheck`
- If you see "The cert-manager API is ready" in the logs, the check is working correctly
- For a one-time check that doesn't restart, consider using a Job instead of a Deployment

## Testing Scenarios

### Test 1: Verify Image Functionality

```bash
# Deploy and check logs
kubectl apply -f deployment.yaml
kubectl logs -n cert-manager-startupapicheck -l app=cert-manager-startupapicheck --tail=50
```

Expected output should show:
```
The cert-manager API is ready
```

This indicates that the startupapicheck tool successfully verified cert-manager API availability.

### Test 2: Check Pod Restart Behavior

```bash
# Delete the pod to test restart
kubectl delete pod -n cert-manager-startupapicheck -l app=cert-manager-startupapicheck

# Watch new pod creation
kubectl get pods -n cert-manager-startupapicheck -w
```

### Test 3: Verify Security Context

```bash
# Check that pod is running as non-root user
kubectl exec -n cert-manager-startupapicheck -l app=cert-manager-startupapicheck -- id
```

Expected output should show user ID 1000 (non-root).

### Test 4: Resource Limits Verification

```bash
# Check resource usage
kubectl top pod -n cert-manager-startupapicheck -l app=cert-manager-startupapicheck
```

## Cleanup

To remove all resources created by this deployment:

```bash
kubectl delete -f deployment.yaml
```

Or delete the namespace (this will remove all resources):

```bash
kubectl delete namespace cert-manager-startupapicheck
```
