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
```


## CrashLoopBackOff

`CrashLoopBackOff` occurs when a Pod’s container **repeatedly crashes and restarts**.  
Kubernetes retries the restart with **increasing delays** instead of failing immediately.

**Common causes:** application errors, misconfigured liveness probes, or insufficient CPU/memory.

Use `kubectl describe pod`, `kubectl logs`, and `kubectl get events` to identify the root cause.




## 📦 StatefulSets & Persistent Volumes

StatefulSets require **Persistent Volumes** to start successfully.  
If a Pod remains in **Pending**, it usually means the **PersistentVolumeClaim is unbound**.

⚠️ This commonly happens when:
- StorageClasses differ across platforms
- AWS-specific storage is used elsewhere
- CSI drivers are missing for external storage

🔑 Fixing storage configuration allows StatefulSet Pods to be scheduled and run correctly.


