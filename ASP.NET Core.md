# ASP.NET Core在Azure Kubernetes Service中的部署和管理

https://www.cnblogs.com/binking338/p/aspnetcore_on_k8s.html



# Azure CLI 登陆
Azure CLI
```
az login
```


# 容器注册表 Azure Container Register (ACR)
## 使用 ACR 管理 Docker 镜像。
### 创建 ACR
Azure CLI / Azure Portal
```
az acr create --resource-group Microshaoft-Misc-001-rg --name microshaoftacr001 --sku Basic
```

### 登录 ACR
```
az acr login --name microshaoftacr001
```

# 服务主体 service principle
### 创建服务主体
```
az ad sp create-for-rbac --skip-assignment
```
记下返回信息 appId 和 password，返回格式如下
```
{
  "appId": "e2e720a2-e063-40a8-9d82-9f8863509b9f",
  "displayName": "azure-cli-2022-07-05-08-20-49",
  "password": "IEr8Q~g8F7lw~u.k6dv0eHMEyVklUMAw2jxUxbZ_",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```
### 给服务主体配置 ACR 的pull权限
查询 ACR 的 arcId
```
az acr show --resource-group Microshaoft-Misc-001-rg --name microshaoftacr001 --query "id" --output tsv
```
记下返回信息 AcrId，返回格式如下
```
/subscriptions/06111d9b-367e-4369-87e5-11c1852ae6eb/resourceGroups/Microshaoft-Misc-001-rg/providers/Microsoft.ContainerRegistry/registries/microshaoftacr001
```

给服务主体分配 AcrPull 角色
```
# az role assignment create --assignee <appId> --scope <acrId> --role acrpull
az role assignment create --assignee e2e720a2-e063-40a8-9d82-9f8863509b9f --scope /subscriptions/06111d9b-367e-4369-87e5-11c1852ae6eb/resourceGroups/Microshaoft-Misc-001-rg/providers/Microsoft.ContainerRegistry/registries/microshaoftacr001 --role acrpull
```
记下返回信息,返回格式如下
```
This command or command group has been migrated to Microsoft Graph API. Please carefully review all breaking changes introduced during this migration: https://docs.microsoft.com/cli/azure/microsoft-graph-migration
{
  "canDelegate": null,
  "condition": null,
  "conditionVersion": null,
  "description": null,
  "id": "/subscriptions/06111d9b-367e-4369-87e5-11c1852ae6eb/resourceGroups/Microshaoft-Misc-001-rg/providers/Microsoft.ContainerRegistry/registries/microshaoftacr001/providers/Microsoft.Authorization/roleAssignments/7e885577-53f8-4241-a80c-611c4d278ee6",
  "name": "7e885577-53f8-4241-a80c-611c4d278ee6",
  "principalId": "cf001b58-09e9-475a-a1bc-d7f8429074d4",
  "principalType": "ServicePrincipal",
  "resourceGroup": "Microshaoft-Misc-001-rg",
  "roleDefinitionId": "/subscriptions/06111d9b-367e-4369-87e5-11c1852ae6eb/providers/Microsoft.Authorization/roleDefinitions/7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "scope": "/subscriptions/06111d9b-367e-4369-87e5-11c1852ae6eb/resourceGroups/Microshaoft-Misc-001-rg/providers/Microsoft.ContainerRegistry/registries/microshaoftacr001",
  "type": "Microsoft.Authorization/roleAssignments"
}
```
# K8s服务集群 Azure Kubernetes Service（AKS）
## 创建AKS集群
```
# az aks create \
#     --resource-group Microshaoft-Misc-001-rg \
#     --name microshaoft-aks-001 \
#      --node-count 1 \
#     --enable-addons monitoring \
#     --service-principal <appId> \
#     --client-secret <password> \
#     --generate-ssh-keys
    

az aks create --resource-group Microshaoft-Misc-001-rg --name microshaoft-aks-001 --node-count 1 --enable-addons monitoring --service-principal e2e720a2-e063-40a8-9d82-9f8863509b9f --client-secret 72f988bf-86f1-41af-91ab-2d7cd011db47 --generate-ssh-keys
```