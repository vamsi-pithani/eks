# Kubernetes Pods — Notes

## 📌 What is a Pod?
- **Smallest deployable unit** in Kubernetes.
- Represents **one or more containers** that share:
  - A **network namespace** (same IP and port space).
  - **Volumes** (shared storage).
- Typically runs **one container**, but can run multiple tightly coupled containers (sidecar pattern).

---

## 📌 Pod Structure
- **Pod Specification**:
  - Containers (images, commands, environment variables, ports).
  - Volumes.
  - Network configuration.
  - Resource requests and limits (CPU, memory).
- **Pod Manifest**:
  - YAML or JSON:
    - `apiVersion: v1`
    - `kind: Pod`
    - `metadata`, `spec`

---

## 📌 Pod Lifecycle
1. **Pending**: Pod accepted by Kubernetes but not yet scheduled on a node.
2. **Scheduled**: Pod assigned to a node.
3. **Running**: Pod is running on the node.
4. **Succeeded**: Pod completed successfully (for pods with restartPolicy: Never).
5. **Failed**: Pod terminated with failure (non-zero exit code).
6. **Unknown**: Node unreachable or state cannot be determined.

---

## 📌 Pod Restart Policies
- **Always** (default): Restart container on exit.
- **OnFailure**: Restart container only if it fails (non-zero exit code).
- **Never**: Never restart the container.

---

## 📌 Pod Networking
- Each Pod gets:
  - A **unique IP address** via the CNI plugin.
  - Containers in a Pod share the **same network namespace**:
    - Localhost communication between containers.
    - Same IP and port space.
- Pod communication:
  - Pod-to-Pod: direct Pod IP or via Services.
  - Pod-to-Service: DNS or ClusterIP.

---

## 📌 Pod Storage
- **Volumes**: Shared storage between containers in a Pod.
  - `emptyDir`: Temporary, deleted with Pod.
  - `hostPath`: Node’s filesystem.
  - `persistentVolumeClaim` (PVC): Connects to Persistent Volumes.

---

## 📌 Multi-Container Pods
- Use cases:
  - Sidecar containers (logging, monitoring, proxy).
  - Ambassador containers (API adapters).
- Containers share:
  - Network namespace.
  - Volumes.

---

## 📌 Pod Health Checks
- **Liveness probe**: Is the app alive? (Restart if failing.)
- **Readiness probe**: Is the app ready to serve traffic?
- **Startup probe**: For slow-starting apps.

---

## 📌 Pod Labels & Selectors
- **Labels**: Key-value pairs to identify Pods (e.g. `app: frontend`).
- **Selectors**: Used by Services, ReplicaSets, Deployments to select Pods.

---

## 📌 Pod Annotations
- Non-identifying metadata (e.g. config, debug info).

---

## 📌 Pod Security Context
- Controls permissions inside the Pod:
  - `runAsUser`
  - `privileged`
  - `fsGroup`

---

## 📌 Common Pod Patterns
✅ **Single-container Pod**: Typical use case.  
✅ **Sidecar container**: Log shipper, monitoring agent.  
✅ **Ambassador container**: Proxy or API adapter.  
✅ **Adapter container**: Data transformation.

---

## 📌 Example Pod YAML

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
  - name: myapp-container
    image: nginx:latest
    ports:
    - containerPort: 80
    livenessProbe:
      httpGet:
        path: /
        port: 80
      initialDelaySeconds: 5
      periodSeconds: 10
