# Horizontal Pod Autoscaling (HPA) Testing:

## Prerequisites
Before testing HPA, ensure you have the following:
- A running Kubernetes cluster (EKS in this case)
- `kubectl` installed and configured
- `eksctl` installed if using Amazon EKS
- `metrics-server` deployed for resource monitoring

## Step 1: Verify Cluster and Environment
```sh
kubectl get all
eksctl get cluster
```
Ensure your cluster is up and running.

## Step 2: Deploy the PHP-Apache Application
Create a file `php-apache.yaml` with the following content:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: php-apache
spec:
  selector:
    matchLabels:
      run: php-apache
  template:
    metadata:
      labels:
        run: php-apache
    spec:
      containers:
      - name: php-apache
        image: registry.k8s.io/hpa-example
        ports:
        - containerPort: 80
        resources:
          limits:
            cpu: 500m
          requests:
            cpu: 200m
---
apiVersion: v1
kind: Service
metadata:
  name: php-apache
  labels:
    run: php-apache
spec:
  ports:
  - port: 80
  selector:
    run: php-apache
```

Apply the deployment:
```sh
kubectl apply -f php-apache.yaml
```

Verify deployment and service:
```sh
kubectl describe svc php-apache
kubectl describe deployment php-apache
```

## Step 3: Enable Horizontal Pod Autoscaler (HPA)
Create HPA with CPU threshold of 50% and scaling between 1 and 10 pods:
```sh
kubectl autoscale deployment php-apache --cpu-percent=50 --min=1 --max=10
```

Check HPA status:
```sh
kubectl get hpa
```

## Step 4: Generate Load
Run a load generator to simulate traffic. This command continuously sends HTTP requests to php-apache, simulating high traffic load. The HPA monitors the CPU usage and, if it exceeds the threshold (50% in this case), Kubernetes scales up the number of pods automatically.

```sh
kubectl run -i --tty load-generator --rm --image=busybox:1.28 --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://php-apache; done"
```

Monitor HPA scaling:
```sh
kubectl get hpa php-apache --watch
kubectl get deployment php-apache
kubectl get pods
```

**In the terminal where you created the Pod that runs a busybox image, terminate the load generation by typing <Ctrl> + C.**


Deploy Metrics Server (If Not Already Installed)
Check if the metrics server is running:
```sh
kubectl get deployment metrics-server -n kube-system
```

If not present, install it:
```sh
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

Verify HPA and resource usage:
```sh
kubectl get hpa
kubectl top pods
```
