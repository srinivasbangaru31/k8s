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

### **Step by Step guide to install Httpd and delivering""

---

## **1. Install Helm (if not already installed)**
If Helm is not installed, install it using:  
```sh
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```
Verify installation:  
```sh
helm version
```

---

## **2. Add the `hackcoderr-httpd` Helm Repository**
Since you're using the `hackcoderr-httpd/httpd` chart, first add the repository:  
```sh
helm repo add hackcoderr-httpd https://hackcoderr.github.io/helm-charts/
helm repo update
```

---

## **3. Install the HTTPD Helm Chart**
Run the following command to deploy Apache (`httpd`):  
```sh
helm install my-httpd hackcoderr-httpd/httpd --version 0.1.0
```
This will deploy **httpd** in your Kubernetes cluster.

---

## **4. Verify the Deployment**
Check if the pods and services are running:  
```sh
kubectl get all
```
Expected output:
```
NAME                                 READY   STATUS    RESTARTS   AGE
pod/my-httpd-xxxxxx                  1/1     Running   0          1m

NAME                     TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
service/my-httpd         ClusterIP      10.100.162.249   <none>        80/TCP         1m
```

---

## **5. Access the HTTPD Pod**
Find your pod name:
```sh
kubectl get pods
```
Example output:
```
NAME                          READY   STATUS    RESTARTS   AGE
my-httpd-7d8f76fb69-9vgmm     1/1     Running   0          5m
```
Connect to the pod:
```sh
kubectl exec -it my-httpd-7d8f76fb69-9vgmm -- /bin/sh
```

---

## **6. Modify the Default Web Page**
Since `vi`, `vim`, or `nano` are not available, use `echo` to modify the file:
```sh
echo "Hello from Avinash Reddy, Your HTTPD is running!" > /var/www/html/index.html
```
Verify the change:
```sh
cat /var/www/html/index.html
```

---

## **7. Expose HTTPD to the Internet (Change Service to LoadBalancer)**
By default, the service is of type **ClusterIP**. Change it to **LoadBalancer** to make it accessible publicly.  

Edit the service:
```sh
kubectl edit svc my-httpd
```
Find the `spec.type` field and change it to:
```yaml
spec:
  type: LoadBalancer
```
Save and exit.

OR delete and create a new service:
```sh
kubectl delete svc my-httpd
kubectl expose deployment my-httpd --type=LoadBalancer --name=my-httpd --port=80 --target-port=80
```

---

## **8. Get the Public IP and Access HTTPD**
Check if an **EXTERNAL-IP** is assigned:
```sh
kubectl get svc my-httpd
```
Example output:
```
NAME       TYPE           CLUSTER-IP       EXTERNAL-IP        PORT(S)        AGE
my-httpd   LoadBalancer   10.100.162.249   3.109.225.100      80/TCP         5m
```
Now, access HTTPD in a browser:
```
http://<EXTERNAL-IP>
```
It should display:
```
Hello from Avinash Reddy, Your HTTPD is running!
```

---

### **9. Clean Up (Optional)**
To delete the deployment:
```sh
helm uninstall my-httpd
```


---


| **Command** | **Syntax** | **Example Command** | **Description** |
|------------|-----------|----------------------|----------------|
| **Search for a Helm chart** | `helm search repo <chart-name>` | `helm search repo bitnami/nginx` | Searches for a chart in configured Helm repositories. |
| **Search in Helm Hub** | `helm search hub <keyword>` | `helm search hub nginx` | Searches for a chart in Helm Hub (Artifact Hub). |
| **Install a Helm chart** | `helm install <release-name> <chart-name>` | `helm install my-nginx bitnami/nginx` | Installs a Helm chart in the Kubernetes cluster. |
| **Install with custom values** | `helm install <release-name> <chart-name> --set <key>=<value>` | `helm install my-nginx bitnami/nginx --set service.type=LoadBalancer` | Installs a Helm chart with overridden values. |
| **List installed Helm releases** | `helm list` | `helm list` | Displays all installed Helm releases. |
| **Upgrade a Helm release** | `helm upgrade <release-name> <chart-name>` | `helm upgrade my-nginx bitnami/nginx` | Upgrades an existing Helm release to a newer version. |
| **Uninstall a Helm release** | `helm uninstall <release-name>` | `helm uninstall my-nginx` | Deletes a Helm release from the cluster. |
| **Pull a Helm chart (without installing)** | `helm pull <chart-name>` | `helm pull bitnami/nginx` | Downloads a Helm chart archive (`.tgz` file). |
| **Extract (untar) a Helm chart** | `helm pull <chart-name> --untar` | `helm pull bitnami/nginx --untar` | Downloads and extracts a Helm chart locally. |
| **Check installed repositories** | `helm repo list` | `helm repo list` | Lists all configured Helm repositories. |
| **Add a Helm repository** | `helm repo add <repo-name> <repo-url>` | `helm repo add bitnami https://charts.bitnami.com/bitnami` | Adds a new Helm repository for searching and installing charts. |
| **Update Helm repositories** | `helm repo update` | `helm repo update` | Refreshes the Helm repository list to fetch the latest charts. |
| **Get details of a Helm release** | `helm status <release-name>` | `helm status my-nginx` | Shows the status and health of a Helm release. |
| **Get values of an installed release** | `helm get values <release-name>` | `helm get values my-nginx` | Displays the values used for an installed Helm release. |
| **Preview a Helm installation (Dry Run)** | `helm install <release-name> <chart-name> --dry-run --debug` | `helm install test-nginx bitnami/nginx --dry-run --debug` | Simulates a Helm install to preview the outcome without applying changes. |
| **Render Helm templates without installing** | `helm template <release-name> <chart-name> --debug` | `helm template my-nginx bitnami/nginx --debug` | Displays the Kubernetes YAML manifest generated by Helm. |
| **Package a Helm chart** | `helm package <chart-directory>` | `helm package my-custom-chart/` | Packages a Helm chart into a `.tgz` archive for distribution. |
| **View history of a Helm release** | `helm history <release-name>` | `helm history my-nginx` | Shows the revision history of a Helm release. |
| **Rollback to a previous release** | `helm rollback <release-name> <revision>` | `helm rollback my-nginx 2` | Rolls back a Helm release to a previous version. |
| **Delete Helm release history** | `helm delete <release-name> --purge` | `helm delete my-nginx --purge` | Deletes a Helm release along with its history. |

---

## **Conclusion**
- **Helm** simplifies application deployment and management in EKS.  
- **Helm Charts** package Kubernetes resources and allow customization via `values.yaml`.  
- **Use Cases**: CI/CD automation, monitoring tools, database deployments, etc.  
- **Examples**: Installed **Nginx**, created a **custom Helm chart**, and deployed **Prometheus & Grafana**.

