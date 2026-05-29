# Kubernetes Metrics Server Customized

Before applying the manifests, make the following changes according to your environment.

## 1. Update Metrics Server Image (Optional)

This repository uses:

```yaml
image: registry.k8s.io/metrics-server/metrics-server:v0.7.2
```

If a newer Metrics Server version is available, simply update the image field in `deployment.yaml`.

Example:

```yaml
image: registry.k8s.io/metrics-server/metrics-server:v0.8.0
```

## 2. Update Host Aliases

Edit the `hostAliases` section in `deployment.yaml` and replace the IP addresses and hostnames with your Kubernetes node details.

## 3. Open Port 4443

This setup uses port **4443** for Metrics Server communication.

Make sure port **4443** is available and not being used by another application.

## 4. Change Port (Optional)

If you want to use a different port, update the port in both files.

### deployment.yaml

```yaml
--secure-port=4443
containerPort: 4443
```

### service.yaml

```yaml
port: 443
targetPort: 4443
```

Replace `4443` with your preferred port in both places.

---

## Deploy

```bash
kubectl apply -k .
```

---

## Verify Installation

### Check Pods

```bash
kubectl get pods -n kube-system
```

### Check APIService

```bash
kubectl get apiservice | grep metrics
```

### Check Node Metrics

```bash
kubectl top nodes
```

### Check Pod Metrics

```bash
kubectl top pods -A
```

---

## Troubleshooting Notes

If Metrics Server is not working, check the following:

### Pod Status

```bash
kubectl get pods -n kube-system
kubectl describe pod <pod-name> -n kube-system
kubectl logs <pod-name> -n kube-system
```

### APIService Status

```bash
kubectl describe apiservice v1beta1.metrics.k8s.io
```

### Service Endpoints

```bash
kubectl get svc metrics-server -n kube-system
kubectl get endpoints metrics-server -n kube-system
```

### Common Issues Fixed During Setup

* CrashLoopBackOff due to incorrect port configuration.
* Liveness and Readiness probe failures.
* Metrics API not available.
* APIService showing `Available=False`.
* Service selector mismatch.
* Service `targetPort` mismatch.
* Kubelet TLS certificate validation issues fixed using:

```yaml
--kubelet-insecure-tls
```

If `kubectl top nodes` and `kubectl top pods -A` return metrics successfully, the setup is working correctly.

