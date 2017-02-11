---
title: "使用 CLI 部署 Azure Container Service 叢集 | Microsoft Docs"
description: "使用 Azure CLI 2.0 Preview 部署 Azure Container Service"
services: container-service
documentationcenter: 
author: sauryadas
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2016
ms.author: saudas
translationtype: Human Translation
ms.sourcegitcommit: 855f0fe77bd55f6ec0dacad4bc28603ac1c6979c
ms.openlocfilehash: c4a513686433e802f27f78de60e8b7fca21b4634


---
# <a name="using-the-azure-cli-20-preview-to-create-an-azure-container-service-cluster"></a>使用 Azure CLI 2.0 preview 建立 Azure Container Service 叢集

若要建立 Azure Container Service 叢集，您需要︰
* 一個 Azure 帳戶 ([取得免費試用帳戶](https://azure.microsoft.com/pricing/free-trial/))
* 安裝 [Azure CLI v.2.0 (預覽)](https://github.com/Azure/azure-cli#installation)
* 以登入您的 Azure 帳戶 (請參下文)

## <a name="log-in-to-your-account"></a>登入您的帳戶
```azurecli
az login 
```
您必須移至這個[連結](https://login.microsoftonline.com/common/oauth2/deviceauth)，並使用 CLI 中提供的裝置代碼進行驗證。

![type command](media/container-service-create-acs-cluster-cli/login.png)

![瀏覽器](media/container-service-create-acs-cluster-cli/login-browser.png)


## <a name="create-a-resource-group"></a>建立資源群組
```azurecli
az group create -n acsrg1 -l "westus"
```

![Image resource group create](media/container-service-create-acs-cluster-cli/rg-create.png)

## <a name="list-of-available-azure-container-service-cli-commands"></a>可用的 Azure Container Service CLI 命令清單

```azurecli
az acs -h
```

![ACS command usage](media/container-service-create-acs-cluster-cli/acs-command-usage-help.png)

## <a name="create-an-azure-container-service-cluster"></a>建立 Azure Container Service 叢集

CLI 中的 ACS create 用法

```azurecli
az acs create -h
```
容器服務的名稱、上一步所建立的資源群組及唯一的 DNS 名稱都是必要的。 其他輸入會設為預設值 (請參閱下方的螢幕擷取畫面說明)，除非使用個別的參數覆寫。
![Image ACS create help](media/container-service-create-acs-cluster-cli/acs-command-usage-help.png)

*使用預設值的快速 ACS create。如果您沒有 SSH 金鑰，請使用第二個命令。這第二個 create 命令使用 --generate-ssh-keys 參數，將會為您建立金鑰*

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789
```

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789 --generate-ssh-keys
```

*請確定 dns 前置詞 (-d 參數) 是唯一的。如果發生錯誤，請以唯一字串再試一次。*

輸入上述命令後，等候約 10 分鐘就會建立叢集。

![Image ACS create](media/container-service-create-acs-cluster-cli/cluster-create.png)

## <a name="list-acs-clusters"></a>列出 ACS 叢集 

### <a name="under-a-subscription"></a>在訂用帳戶下

```azurecli
az acs list --output table
```

### <a name="in-a-specific-resource-group"></a>在特定的資源群組中

```azurecli
az acs list -g acsrg1 --output table
```

![Image ACS list](media/container-service-create-acs-cluster-cli/acs-list.png)


## <a name="display-details-of-a-container-service-cluster"></a>顯示容器服務叢集的詳細資料

```azurecli
az acs show -g acsrg1 -n acs-cluster --output list
```

![Image ACS list](media/container-service-create-acs-cluster-cli/acs-show.png)


## <a name="scale-the-acs-cluster"></a>調整 ACS 叢集
*允許相應縮小和相應放大。new-agent-count 參數 ACS 叢集的新代理程式數目。*

```azurecli
az acs scale -g acsrg1 -n acs-cluster --new-agent-count 4
```

![Image ACS scale](media/container-service-create-acs-cluster-cli/acs-scale.png)

## <a name="delete-a-container-service-cluster"></a>刪除容器服務叢集
```azurecli
az acs delete -g acsrg1 -n acs-cluster 
```
*請注意，此 delete 命令不會刪除在建立容器服務時所建立的所有資源 (網路和儲存體)。若要刪除所有資源，建議為每個資料群組建立單一 ACS 叢集，然後於不再需要 acs 叢集時刪除資源群組本身，以確保刪除所有相關的資源，也就不會對您收費。*



<!--HONumber=Nov16_HO3-->


