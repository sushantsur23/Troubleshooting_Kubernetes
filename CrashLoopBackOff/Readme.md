## CrashLoopBackOff – Kubernetes Debugging Notes

`CrashLoopBackOff` occurs when a Pod’s container **keeps crashing and restarting repeatedly**.  
Kubernetes retries the restart with **increasing delays** (BackOff delay) instead of failing immediately.

### What It Indicates
- The Pod starts successfully
- The container crashes
- Kubernetes restarts it
- This cycle continues in a loop

---

### Common Causes
- ❌ Application or command errors (wrong entrypoint, missing files)
- ❌ Misconfigured **liveness probe** causing forced restarts
- ❌ Insufficient CPU or memory (OOM / resource limits)
- ❌ Runtime exceptions inside the container

---

### How to Debug
```bash
kubectl get pods
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl get events
