## <a name="create-a-resource-group"></a>建立資源群組

 [Azure 資源群組](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)是一個邏輯容器，可在其中管理 Web 應用程式和資料庫等資源。 您可部署、更新及刪除資源群組中的資源。

使用 [az group create](/cli/azure/group#create) 命令來建立資源群組。

```azurecli
az group create --name myResourceGroup --location westeurope
```

若要查看可用的位置，請使用 `az appservice list-locations` 命令。 您通常會在附近的區域中建立資源。

## <a name="create-an-azure-app-service-plan"></a>建立 Azure App Service 方案

使用 [az appservice plan create](/cli/azure/appservice/plan#create) 命令來建立「免費」[App Service 方案](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。

[!INCLUDE [app-service-plan](app-service-plan.md)]

下列命令會使用**免費**定價層，建立名為 `quickStartPlan` 的 App Service 方案。

```azurecli
az appservice plan create --name quickStartPlan --resource-group myResourceGroup --sku FREE
```

建立 App Service 方案後，Azure CLI 會顯示類似下列範例的資訊：

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "quickStartPlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "quickStartPlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
```

## <a name="create-a-web-app"></a>建立 Web 應用程式