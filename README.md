## ImagePullBackOff – Kubernetes Debugging Notes

`ImagePullBackOff` occurs when Kubernetes fails to pull a container image onto a node.

### Common Causes
- ❌ Invalid image name or tag
- 🔒 Private image without proper authentication
- 🌐 Network, DNS, or registry connectivity issues
- 🚫 Permission denied from the container registry

### Private Image Handling
- For **Docker Hub private images**, configure `imagePullSecrets` with Docker credentials.
- For **ECR / ACR**, create a secret containing AWS/Azure credentials and reference it in the Pod or ServiceAccount.

### How to Identify the Issue
```bash
kubectl get pods
kubectl describe pod <pod-name>
kubectl get events

