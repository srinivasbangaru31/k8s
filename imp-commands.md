###K8s Basic and Important commands


| **Category** | **Command** | **Description** |
|-------------|------------|----------------|
| **Cluster Information** | `kubectl cluster-info` | Displays cluster information. |
| | `kubectl version` | Shows Kubernetes client and server versions. |
| **Working with Nodes** | `kubectl get nodes` | Lists all nodes in the cluster. |
| | `kubectl describe node <node-name>` | Displays detailed information about a specific node. |
| **Working with Pods** | `kubectl get pods` | Lists all pods in the default namespace. |
| | `kubectl get pods --all-namespaces` | Lists all pods across all namespaces. |
| | `kubectl describe pod <pod-name>` | Displays detailed information about a pod. |
| | `kubectl logs <pod-name>` | Fetches logs from a pod. |
| | `kubectl logs -f <pod-name>` | Streams logs from a pod. |
| | `kubectl exec -it <pod-name> -- <command>` | Executes a command inside a running pod. |
| | `kubectl exec -it <pod-name> -- /bin/sh` | Opens a shell session inside a pod. |
| | `kubectl delete pod <pod-name>` | Deletes a pod. |
| **Working with Deployments** | `kubectl get deployments` | Lists all deployments. |
| | `kubectl apply -f <deployment-file.yaml>` | Creates or updates a deployment from a YAML file. |
| | `kubectl scale deployment <deployment-name> --replicas=<number>` | Scales a deployment. |
| | `kubectl delete deployment <deployment-name>` | Deletes a deployment. |
| **Working with Services** | `kubectl get services` | Lists all services. |
| | `kubectl apply -f <service-file.yaml>` | Creates a service from a YAML file. |
| | `kubectl delete service <service-name>` | Deletes a service. |
| **Working with Namespaces** | `kubectl get namespaces` | Lists all namespaces. |
| | `kubectl create namespace <namespace-name>` | Creates a new namespace. |
| | `kubectl config set-context --current --namespace=<namespace-name>` | Switches to a specific namespace. |
| | `kubectl get pods -n <namespace-name>` | Lists pods in a specific namespace. |
| **Working with ConfigMaps & Secrets** | `kubectl get configmaps` | Lists all ConfigMaps. |
| | `kubectl get secrets` | Lists all Secrets. |
| | `kubectl create configmap <configmap-name> --from-file=<path>` | Creates a ConfigMap from a file. |
| | `kubectl create secret generic <secret-name> --from-file=<path>` | Creates a Secret from a file. |
| **Debugging & Troubleshooting** | `kubectl describe <resource-type> <resource-name>` | Describes a Kubernetes resource. |
| | `kubectl get events` | Displays cluster events. |
| | `kubectl port-forward <pod-name> <local-port>:<pod-port>` | Forwards a local port to a pod. |
| **Managing Resources** | `kubectl apply -f <file.yaml>` | Applies a YAML configuration. |
| | `kubectl delete -f <file.yaml>` | Deletes resources defined in a YAML file. |
| | `kubectl edit <resource-type> <resource-name>` | Edits a resource directly. |
| **Miscellaneous** | `kubectl get all` | Lists all resources in the cluster. |
| | `kubectl top nodes` | Displays resource usage of nodes. |
| | `kubectl top pods` | Displays resource usage of pods. |
| | `kubectl drain <node-name> --ignore-daemonsets --delete-emptydir-data` | Drains a node for maintenance. |
| | `kubectl uncordon <node-name>` | Marks a node as schedulable again. |
| **Context & Configuration** | `kubectl config current-context` | Shows the current context. |
| | `kubectl config get-contexts` | Lists all available contexts. |
| | `kubectl config use-context <context-name>` | Switches to a different context. |
| | `kubectl config view` | Displays Kubernetes configuration. |
| **Cleanup** | `kubectl delete pods --all -n <namespace-name>` | Deletes all pods in a namespace. |
| | `kubectl delete all --all -n <namespace-name>` | Deletes all resources in a namespace. |

