---
title: "Azure Kubernetes 叢集的服務主體 | Microsoft Docs"
description: "在採用 Kubernetes 的 Azure Container Service 叢集中建立和管理 Azure Active Directory 服務主體"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/21/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 71c6c5ffacf49b907e3e9f488789f31928b25823
ms.openlocfilehash: e01a9ef7d223e7a5a06475cf419b73959baa803f
ms.lasthandoff: 02/22/2017


---

# <a name="about-the-azure-active-directory-service-principal-for-a-kubernetes-cluster-in-azure-container-service"></a>關於 Azure Container Service 中 Kubernetes 叢集的 Azure Active Directory 服務主體



在 Azure Container Service 中，Kubernetes 需要 [Azure Active Directory 服務主體](../active-directory/active-directory-application-objects.md)做為服務帳戶，才能與 Azure API 進行互動。 需要服務主體，才能以動態方式管理資源，例如使用者定義的路由及第 4 層 Azure Load Balancer。

本文說明為 Kubernetes 叢集指定服務主體的不同選項。 例如，如果您已安裝並設定 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2)，您可以執行 [`az acs create`](https://docs.microsoft.com/en-us/cli/azure/acs#create) 命令，在同一時間建立 Kubernetes 叢集與服務主體。



## <a name="requirements-for-the-service-principal"></a>服務主體的需求

以下是 Azure Container Service 的 Kubernetes 叢集中 Azure Active Directory 服務主體的需求。 

* **範圍**：部署叢集的資源群組

* **角色**：**參與者**

* **用戶端密碼**：必須是密碼。 目前，您無法使用針對憑證驗證設定的服務主體。

> [!NOTE]
> 每個服務主體都會與 Azure Active Directory 應用程式相關聯。 Kubernetes 叢集的服務主體可以與任何有效的 Azure Active Directory 應用程式名稱相關聯。
> 


## <a name="service-principal-options-for-a-kubernetes-cluster"></a>Kubernetes 叢集的服務主體選項

### <a name="option-1-pass-the-service-principal-client-id-and-client-secret"></a>選項 1︰傳遞服務主體的用戶端識別碼和用戶端密碼

當您建立 Kubernetes 叢集時，提供現有服務主體的**用戶端識別碼** (也稱為`appId`，適用於應用程式識別碼) 和**用戶端密碼** (`password`) 做為參數。 如果您使用現有的服務主體，請確定它符合上一節所述的需求。 如果您需要建立服務主體，請參閱在本文後面的[建立服務主體](#create-a-service-principal-in-azure-active-directory)。

使用入口網站、Azure 命令列介面 (CLI) 2.0 或 Azure PowerShell 或其他方法[部署 Kubernetes 叢集](./container-service-deployment.md)時，您可以指定這些參數。

>[!TIP] 
>指定**用戶端識別碼**時，務必使用服務主體的 `appId`，而非 `ObjectId`。
>

下列範例顯示使用 Azure CLI 2.0 傳遞參數的方式 (請參閱[安裝和設定指示](/cli/azure/install-az-cli2))。 此範例使用 [Kubernetes 快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)。

1. 從 GitHub [下載](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.parameters.json)範本參數檔案 `azuredeploy.parameters.json`。

2. 若要指定服務主體，在檔案中輸入 `servicePrincipalClientId` 和 `servicePrincipalClientSecret` 的值。 (您也必須提供自己的 `dnsNamePrefix` 和 `sshRSAPublicKey` 值。 後者是可存取叢集的 SSH 公開金鑰)。儲存檔案。

    ![傳遞服務主體參數](./media/container-service-kubernetes-service-principal/service-principal-params.png)

3. 執行下列命令，並使用 `--parameters` 來設定 azuredeploy.parameters.json 檔案的路徑。 此命令會在美國西部區域中名為 `myResourceGroup` 的資源群組中部署叢集。

    ```azurecli
    az login

    az account set --subscription "mySubscriptionID"

    az group create --name "myResourceGroup" --location "westus" 
    
    az group deployment create -g "myResourceGroup" --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.json" --parameters @azuredeploy.parameters.json
    ```


### <a name="option-2-generate-the-service-principal-when-creating-the-cluster-with-the-azure-cli-20"></a>選項 2︰在使用 Azure CLI 2.0 建立叢集時產生服務主體

如果您已安裝並設定 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2)，您可以執行 [`az acs create`](https://docs.microsoft.com/en-us/cli/azure/acs#create) 命令以[建立叢集](./container-service-create-acs-cluster-cli.md)。

至於其他 Kubernetes 叢集建立選項，您可以在執行 `az acs create` 時指定現有服務主體的參數。 不過，當您省略這些參數時，Azure Container Service 會自動建立服務主體。 這會在部署期間以透明方式發生。 

下列命令會建立 Kubernetes 叢集並產生 SSH 金鑰和服務主體認證︰

```console
az acs create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys --orchestrator-type kubernetes
```

## <a name="create-a-service-principal-in-azure-active-directory"></a>在 Azure Active Directory 中建立服務主體

如果您想要在 Azure Active Directory 中建立服務主體以便用於 Kubernetes 叢集，Azure 會提供數種方法。 

下列範例命令示範如何使用 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) 執行這項作業。 或者，您也可以使用 [Azure PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md)、[傳統入口網站](../azure-resource-manager/resource-group-create-service-principal-portal.md)或其他方法來建立服務主體。

> [!IMPORTANT]
> 請務必檢閱本文中先前的服務主體需求。
>

```azurecli
az login

az account set --subscription "mySubscriptionID"

az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/mySubscriptionID"
```

這會傳回類似以下的輸出 (以下顯示擬定的輸出)：

![建立服務主體](./media/container-service-kubernetes-service-principal/service-principal-creds.png)

醒目提示的是您做為叢集部署之服務主體參數的**用戶端識別碼** (`appId`) 和**用戶端密碼** (`password`)。


開啟新的命令介面並執行下列命令 (在 `appId``password` 和 `tenant` 替代)，以確認您的服務主體：

```azurecli 
az login --service-principal -u yourClientID -p yourClientSecret --tenant yourTenant

az vm list-sizes --location westus
```

## <a name="additional-considerations"></a>其他考量


* 指定服務主體**用戶端識別碼**時，您可以使用 `appId` 的值 (如本文所示) 或對應的服務主體`name` (例如，`https://www.contoso.org/example`)。

* 如果您使用 `az acs create` 命令自動產生服務主體，服務主體認證會寫入用來執行命令之電腦上的 ~/.azure/acsServicePrincipal.json 檔案。

* 在 Kubernetes 叢集中的主要和節點 VM 上，服務主體認證會儲存在 /etc/kubernetes/azure.json 檔案中。

## <a name="next-steps"></a>後續步驟

* [開始使用容器服務叢集中的 Kubernetes](container-service-kubernetes-walkthrough.md)。

