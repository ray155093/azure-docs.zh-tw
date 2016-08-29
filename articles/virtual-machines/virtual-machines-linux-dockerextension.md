<properties
   pageTitle="了解 Azure 上的 Docker VM 擴充功能 | Microsoft Azure"
   description="了解如何使用 Docker VM 擴充功能在 Azure 中快速而安全地部署 Docker 環境"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="07/20/2016"
   ms.author="iainfou"/>

# 使用 Docker VM 擴充功能來部署您的環境

Docker 是常用的容器管理和映像處理平台，它能讓您在 Linux 上 (和 Windows 上) 快速地操作容器。透過 Azure，您可以根據需求利用幾個不同的方式彈性地部署 Docker︰

- 若要快速建立應用程式原型，您可以[使用 Docker Machine Azure 驅動程式](./virtual-machines-linux-docker-machine.md)在 Azure 內部署 Docker 主機。
- 適用於 Azure 虛擬機器的 Docker VM 擴充功能可用來進行以範本為基礎的部署。這種做法可以與 Azure Resource Manager 範本部署整合，而且包含所有相關的優點，例如角色型存取、診斷及部署後設定。
- Docker VM 擴充功能也支援 Docker Compose。Docker Compose 使用宣告式 YAML 檔案來取得任何環境的開發人員模型化應用程式並產生一致的部署。
- 對於使用 Swarm 所提供之其他排程和管理工具的實際執行準備就緒、可調整部署，您也可以[在 Azure 容器服務上部署完整的 Docker Swarm 叢集](../container-service/container-service-deployment.md)。

本文將焦點放在使用 Resource Manager 範本，在您定義的自訂、實際執行準備就緒環境中，部署「Docker VM 擴充功能」。

## 適用於範本部署的 Azure Docker VM 擴充功能

Azure Docker VM 擴充功能會在您的 Linux 虛擬機器中安裝並設定 Docker 精靈、Docker 用戶端和 Docker Compose。此擴充功能也可用來透過 Docker Compose 定義及部署容器應用程式。您可以額外控制是要使用 Docker Machine 還是自行建立 Docker 主機，使其適合更健全的開發人員環境或生產環境。

在使用 Azure Resource Manager 的情況下，您可以建立及部署定義整個環境結構的範本。範本可讓您定義 Docker 主機、儲存體、「角色型存取控制」(RBAC)、診斷等。您可以[閱讀更多有關 Resource Manage ](../resource-group-overview.md) 和範本的資訊，以充分了解其中的一些優點。透過使用 Resource Manager 範本，您未來也將能夠視需要重現部署。

## 使用 Docker VM 擴充功能部署範本︰

讓我們使用現有的快速啟動範本來示範如何部署已安裝「Docker VM 擴充功能」的 Ubuntu VM。您可以在這裡檢視範本︰[使用 Docker 簡易部署 Ubuntu VM](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)。您還需要處於 Resource Manager 模式的[最新 Azure CLI](../xplat-cli-install.md) (`azure config mode arm`)。

使用 Azure CLI 部署範本，並指定新資源群組 (即範例中的 `myDockerResourceGroup`) 的名稱和範本 URI：

```
azure group create --name myDockerResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

回應為儲存體帳戶命名、DNS 名稱、使用者名稱等的提示，然後稍待幾分鐘的時間完成部署。您應該會看到如下所示的輸出：

```
info:    Executing command group create
+ Getting resource group myDockerResourceGroup
+ Updating resource group myDockerResourceGroup
info:    Updated resource group myDockerResourceGroup
info:    Supply values for the following parameters
newStorageAccountName: mydockerstorage
adminUsername: ops
adminPassword: P@ssword!
dnsNameForPublicIP: mydockergroup
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/guid/resourceGroups/myDockerResourceGroup
data:    Name:                myDockerResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK

```

## 部署您的第一個 nginx 容器
部署完成之後，使用在部署期間提供的 DNS 名稱透過 SSH 連接新 Docker 主機。讓我們嘗試執行 nginx 容器︰

```
sudo docker run -d -p 80:80 nginx
```

您應該會看到如下所示的輸出：

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
a48df1751a97: Pull complete
8ddc2d7beb91: Pull complete
Digest: sha256:2ca2638e55319b7bc0c7d028209ea69b1368e95b01383e66dfe7e4f43780926d
Status: Downloaded newer image for nginx:latest
b6ed109fb743a762ff21a4606dd38d3e5d35aff43fa7f12e8d4ed1d920b0cd74
```

使用 `sudo docker ps` 來檢查在您主機上執行的容器：

```
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

若要查看容器實際運作的情況，請開啟網頁瀏覽器，然後輸入您在部署期間指定的 DNS 名稱︰

![執行 ngnix 容器](./media/virtual-machines-linux-dockerextension/nginxrunning.png)

您可能會想要使用 Docker Compose 來設定 Docker 精靈 TCP 連接埠、安全性或部署容器。如需詳細資訊，請參閱[適用於 Docker 的 Azure 虛擬機器擴充功能 GitHub 專案](https://github.com/Azure/azure-docker-extension/)。

## Docker VM 擴充功能 JSON 範本參考

此範例使用快速啟動範本。若要使用您自己的 Resource Manager 範本來部署 Azure Docker VM 擴充功能，請新增下列內容︰

```
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'), '/DockerExtension'))]",
  "apiVersion": "2015-05-01-preview",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "DockerExtension",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

如需更詳細的 Resource Manager 範本使用逐步解說，請參閱 [Azure Resource Manager 概觀](../resource-group-overview.md)

## 後續步驟

閱讀不同部署選項的詳細步驟：

1. [使用 Docker 電腦搭配 Azure 驅動程式](./virtual-machines-linux-docker-machine.md)
2. [透過 Azure 命令列介面 (Azure CL) 使用 Docker VM 延伸模組](./virtual-machines-linux-classic-cli-use-docker.md)
3. [在 Azure 虛擬機器上開始使用 Docker 和 Compose 定義並執行多容器應用程式](virtual-machines-linux-docker-compose-quickstart.md)。
3. [部署 Azure 容器服務叢集](../container-service/container-service-deployment.md)

<!---HONumber=AcomDC_0817_2016-->