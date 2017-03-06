## <a name="prerequisites"></a>必要條件

如果尚未安裝，請取得 [Azure 訂用帳戶免費試用](https://azure.microsoft.com/pricing/free-trial/)，然後安裝 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2)。

## <a name="create-the-scale-set"></a>建立擴展集

首先，建立要部署擴展集的資源群組：

```azurecli
az group create --location westus --name myResourceGroup
```

現在，使用 `az vmss create` 命令建立您的擴展集。 下列範例會建立名為 `myvmss` 的 Linux 擴展集以及名為 `myrg` 資源群組：

```azurecli
az vmss create --resource-group myResourceGroup --name myVmss \
    --image UbuntuLTS --admin-username azureuser \
    --authentication-type password --admin-password P4$$w0rd
```

下列範例會使用相同組態建立 Windows 擴展集：

```azurecli
az vmss create --resource-group myResourceGroup --name myVmss \
    --image Win2016Datacenter --admin-username azureuser \
    --authentication-type password --admin-password P4$$w0rd
```

如果您想要選擇不同的 OS 映像，您可以使用命令 `az vm image list` 或 `az vm image list --all` 來查看可用的映像。 若要在擴展集中查看適用於 VM 的連接資訊，請使用 `az vmss list_instance_connection_info` 命令：

```azurecli
az vmss list_instance_connection_info --resource-group myResourceGroup --name myVmss
```