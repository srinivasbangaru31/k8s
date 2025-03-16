### **What is a Namespace in Kubernetes?**  
A **namespace** in Kubernetes is a **logical isolation mechanism** that allows you to group resources within a cluster. It is useful for **organizing and managing** workloads, especially in **multi-team or multi-environment setups**.

---

### **How is a Namespace Useful?**
Namespaces help in **separating and organizing** different workloads efficiently:

 **Multi-Tenancy** – Different teams can work in **isolated environments** within the same cluster.  
 **Resource Quotas** – Limits can be set per namespace to **control CPU and memory usage**.  
 **Security & Access Control** – RBAC (Role-Based Access Control) can be used to **restrict access** to specific namespaces.  
 **Environment Separation** – Useful for **staging, development, and production** environments.  

---

### **Challenges Solved by Namespaces**
1. **Avoids Resource Conflicts**  
   - Multiple applications can run on the same cluster **without conflicting** over resource names.  
   - Example: Two teams can **use the same deployment name** (`frontend`) in different namespaces (`team-a`, `team-b`).

2. **Better Organization**  
   - Large clusters with **hundreds of services** can be **grouped logically** (e.g., `dev`, `staging`, `prod`).  

3. **Fine-Grained Access Control**  
   - Using **RBAC**, you can give developers **access only to their namespace** instead of the entire cluster.  

4. **Simplifies Resource Management**  
   - Kubernetes allows **setting quotas per namespace** to prevent a single team from consuming all cluster resources.

---

### **Demo Deployment File Using Namespace**  
Below is a **sample YAML file** that creates a **namespace** and deploys an **nginx application** inside it.

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev-namespace  # Custom namespace name

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: dev-namespace  # Assigning the deployment to "my-namespace"
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80

---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
  namespace: dev-namespace  # Assigning the service to "my-namespace"
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```

---

### **Steps to Apply & Verify**
 **Apply the namespace and deployment:**  
   ```sh
   kubectl apply -f namespace-deployment.yaml
   ```

 **Check created namespaces:**  
   ```sh
   kubectl get namespaces
   ```

 **List resources inside the namespace:**  
   ```sh
   kubectl get deployments,pods,services -n my-namespace
   ```

 **Delete the namespace (cleanup):**  
   ```sh
   kubectl delete namespace my-namespace
   ```

---


### **Test the ResourceQuota in a Kubernetes Cluster**

1. **Apply the ResourceQuota YAML**
   ```sh
   kubectl apply -f - <<EOF
   apiVersion: v1
   kind: ResourceQuota
   metadata:
     name: dev-namespace-quota
     namespace: dev-namespace
   spec:
     hard:
       requests.cpu: "2"       
       requests.memory: "4Gi"  
       limits.cpu: "4"         
       limits.memory: "8Gi"    
   EOF
   ```
2. **Verify the ResourceQuota**
   ```sh
   kubectl get resourcequota -n dev-namespace
   ```

   **Expected Output:**
   ```
   NAME                    AGE   REQUESTS.CPU   REQUESTS.MEMORY   LIMITS.CPU   LIMITS.MEMORY
   dev-namespace-quota     10s   2              4Gi              4            8Gi
   ```

3. **Deploy a Test Pod to Check the Quota**
   ```sh
   kubectl run test-pod --image=nginx --requests='cpu=1,memory=2Gi' --limits='cpu=3,memory=6Gi' -n dev-namespace
   ```
   This should **work** since the requests and limits are within the quota.

4. **Deploy a Pod That Exceeds the Limit**
   ```sh
   kubectl run fail-pod --image=nginx --requests='cpu=3,memory=5Gi' --limits='cpu=5,memory=10Gi' -n dev-namespace
   ```
   **This should fail** because:
   - `requests.cpu=3` **exceeds** the allowed `2`.
   - `requests.memory=5Gi` **exceeds** the allowed `4Gi`.
   - `limits.cpu=5` **exceeds** the allowed `4`.
   - `limits.memory=10Gi` **exceeds** the allowed `8Gi`.

---
