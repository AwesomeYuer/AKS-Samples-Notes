# AKS-Samples-Notes

教程：部署 Azure Kubernetes 服务 (AKS) 群集
https://docs.microsoft.com/zh-cn/azure/aks/tutorial-kubernetes-deploy-cluster?tabs=azure-cli

快速入门：使用 Azure 门户部署 Azure Kubernetes 服务 (AKS) 群集
https://docs.microsoft.com/zh-cn/azure/aks/learn/quick-kubernetes-deploy-portal?tabs=azure-cli

Azure CLI

To perform these operations in a <font color="red">local shell </font>installation:

Verify Azure CLI or Azure PowerShell is installed.
Connect to Azure via the az login or Connect-AzAccount command.

```
az login
```

Configure kubectl to connect to your Kubernetes cluster using the az aks get-credentials command. The following command downloads credentials and configures the Kubernetes CLI to use them.

```
az aks get-credentials --resource-group Microshaoft-Misc-001-rg --name microshaoft-AKSCluster
```

```
kubectl get nodes
```





In the<font color="red"> Cloud Shell</font>, use an editor to create a file named azure-vote.yaml, such as:
```
# use
code azure-vote.yaml
# or
nano azure-vote.yaml
# or
vi azure-vote.yaml.
```

Using the Azure Cloud Shell editor
https://docs.microsoft.com/en-us/azure/cloud-shell/using-cloud-shell-editor

```
code .
```
CTRL+Q 退出编辑器


Copy in the following YAML definition:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
        env:
        - name: ALLOW_EMPTY_PASSWORD
          value: "yes"
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```


Deploy the application using the kubectl apply command and specify the name of your YAML manifest:

```
kubectl apply -f azure-vote.yaml
```

Test the application
When the application runs, a Kubernetes service exposes the application front end to the internet. This process can take a few minutes to complete.

To monitor progress, use the kubectl get service command with the --watch argument.

```
kubectl get service azure-vote-front --watch
```