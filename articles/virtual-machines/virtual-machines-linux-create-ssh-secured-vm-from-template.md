<properties
	pageTitle="使用 Azure 範本建立 Linux VM | Microsoft Azure"
	description="使用 Azure Resource Manager 範本在 Azure 上建立 Linux VM。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="vlivech"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="08/17/2016"
	ms.author="v-livech"/>

# 使用 Azure 範本建立 Linux VM

本文示範如何使用 Azure 範本，在 Azure 上快速部署 Linux 虛擬機器。本文需要 Azure 帳戶 ([取得免費試用](https://azure.microsoft.com/pricing/free-trial/))，以及已登入 (`azure login`) 且處於 Resource Manager 模式 (`azure config mode arm`) 的 [Azure CLI](../xplat-cli-install.md)。您也可以使用 [Azure 入口網站](virtual-machines-linux-quick-create-portal.md)和 [Azure CLI](virtual-machines-linux-quick-create-cli.md) 快速部署 Linux VM。

## 快速命令摘要

```bash
azure group create \
-n quicksecuretemplate \
-l eastus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## 詳細的逐步解說

範本可讓您在 Azure 上使用要在啟動期間自訂的設定 (如使用者名稱和主機名稱等設定) 建立 VM。在本文中，我們推出的 Azure 範本會利用 Ubuntu VM 以及連接埠 22 對 SSH 開放的網路安全性群組 (NSG)。

Azure Resource Manager 範本是 JSON 檔案，可用於進行簡單的一次性工作 (如本文中的啟動 Ubuntu VM)。Azure 範本也可用來建構整個環境的複雜 Azure 組態，例如測試、開發或生產部署堆疊。

## 建立 Linux VM

下列程式碼範例示範如何呼叫 `azure group create`，以使用[此 Azure Resource Manager 範本](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json)同時建立資源群組和部署受 SSH 保護的 Linux VM。請記住，在您的範例中您必須使用環境中唯一的名稱。此範例則以 `quicksecuretemplate` 做為資源群組名稱、以 `securelinux` 做為 VM 名稱，並以 `quicksecurelinux` 做為子網域名稱。

```bash
azure group create \
-n quicksecuretemplate \
-l eastus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

輸出

```bash
info:    Executing command group create
+ Getting resource group quicksecuretemplate
+ Creating resource group quicksecuretemplate
info:    Created resource group quicksecuretemplate
info:    Supply values for the following parameters
sshKeyData: ssh-rsa AAAAB3Nza<..ssh public key text..>VQgwjNjQ== vlivech@azure
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<..subid text..>/resourceGroups/quicksecuretemplate
data:    Name:                quicksecuretemplate
data:    Location:            eastus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

該範例使用 `--template-uri` 參數部署 VM。您也可以使用 `--template-file` 參數並以範本檔案路徑做為引數，在本機下載或建立範本並傳遞範本。Azure CLI 會提示您輸入範本所需的參數。

## 後續步驟

請搜尋[範本庫](https://azure.microsoft.com/documentation/templates/)以探索接下來要部署哪些應用程式架構。

<!---HONumber=AcomDC_0824_2016-->