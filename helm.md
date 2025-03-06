## **What is Helm and Charts in EKS?**  
**Helm** is a package manager for **Kubernetes**, similar to how **apt** (Ubuntu) or **yum** (Amazon Linux) works for Linux. It simplifies the deployment and management of applications in Kubernetes by using **Helm Charts**—pre-packaged Kubernetes resources with configurable templates.

### **Key Components of Helm:**
1. **Helm CLI** → Command-line tool to install and manage Helm charts.
2. **Helm Chart** → A collection of YAML files that define Kubernetes resources (Deployment, Service, ConfigMaps, etc.).
3. **Helm Repository** → A storage location for charts (e.g., ArtifactHub, Bitnami Helm Repo).
4. **Values File (`values.yaml`)** → Configurable parameters for the Helm chart.

---

## **Why is Helm Important?**
In an **enterprise EKS (Elastic Kubernetes Service) environment**, managing multiple microservices manually using raw YAML files becomes complex. Helm provides:
✅ **Simplification** → Manage multiple Kubernetes objects as a single package.  
✅ **Reusability** → Use the same chart across multiple environments with different configurations.  
✅ **Versioning & Rollbacks** → Easily roll back to a previous version if a deployment fails.  
✅ **Parameterization** → Customize deployments with values.yaml instead of modifying YAML files manually.  
✅ **Dependency Management** → Helps manage dependent services (e.g., deploying a database alongside an app).  

---

## **Common Use Cases of Helm in EKS**
1. **Deploying Enterprise Applications** → Use Helm to deploy and manage microservices-based applications.
2. **Installing Monitoring Tools** → Prometheus, Grafana, Loki, and Fluentd are installed via Helm in EKS.
3. **Managing Database Deployments** → Deploy MySQL, PostgreSQL, or MongoDB using Helm charts.
4. **CI/CD Integration** → Automate Helm deployments in **Jenkins, GitHub Actions, or ArgoCD**.
5. **Managing Configurations** → Helm **values.yaml** helps manage different configurations for dev, staging, and prod environments.

---

## **Example 1: Install Nginx Using Helm on EKS**
### **Step 1: Install Helm (if not installed)**
```sh
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```

### **Step 2: Add the Bitnami Helm Repository**
```sh
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
```

### **Step 3: Install Nginx**
```sh
helm install my-nginx bitnami/nginx
```
**What happens?**  
This installs an **Nginx Deployment and Service** on EKS using Bitnami’s Helm chart.

### **Step 4: Verify the Deployment**
```sh
kubectl get pods
kubectl get svc
```

### **Step 5: Uninstall Nginx**
```sh
helm uninstall my-nginx
```

---

## **Example 2: Deploy a Custom Application Using Helm**
### **Step 1: Create a Helm Chart**
```sh
helm create my-app
cd my-app
```
It creates a directory structure like:
```
my-app/
├── charts/
├── templates/
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── ingress.yaml
│   ├── _helpers.tpl
│   └── NOTES.txt
├── values.yaml
├── Chart.yaml
└── README.md
```

### **Step 2: Modify `values.yaml`**
```yaml
replicaCount: 2
image:
  repository: <aws-account-id>.dkr.ecr.<region>.amazonaws.com/my-app
  tag: latest
service:
  type: LoadBalancer
  port: 80
```

### **Step 3: Deploy the Chart to EKS**
```sh
helm install my-app ./my-app
```

### **Step 4: Verify the Deployment**
```sh
kubectl get pods
kubectl get svc
```

### **Step 5: Upgrade the Application**
Modify `values.yaml` (change `replicaCount: 3`), then run:
```sh
helm upgrade my-app ./my-app
```

### **Step 6: Rollback to Previous Version**
```sh
helm rollback my-app 1
```

---

## **Example 3: Installing Prometheus & Grafana via Helm in EKS**
```sh
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install prometheus prometheus-community/kube-prometheus-stack
```
This installs **Prometheus, Grafana, and AlertManager** in EKS.

To access Grafana:
```sh
kubectl port-forward svc/prometheus-grafana 3000:80
```
Now, open `http://localhost:3000` in your browser.

---

## **Conclusion**
- **Helm** simplifies application deployment and management in EKS.  
- **Helm Charts** package Kubernetes resources and allow customization via `values.yaml`.  
- **Use Cases**: CI/CD automation, monitoring tools, database deployments, etc.  
- **Examples**: Installed **Nginx**, created a **custom Helm chart**, and deployed **Prometheus & Grafana**.

