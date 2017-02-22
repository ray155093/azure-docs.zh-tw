---
title: "在 Azure 中部署 Docker 容器叢集 | Microsoft Docs"
description: "使用 Azure 入口網站或Azure Resource Manager 範本來部署 Azure Container Service 叢集。"
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, Containers, Micro-services, Mesos, Azure, dcos, swarm, kubernetes, azure container service, acs
ms.assetid: 696a736f-9299-4613-88c6-7177089cfc23
ms.service: container-service
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: 01fe5302e1c596017755c4669103bac910e3452c
ms.openlocfilehash: 470bf39bf0e61325f36a2f45316f57545c69e3de


---
# <a name="deploy-an-azure-container-service-cluster"></a>部署 Azure Container Service 叢集



Azure Container Service 支援快速部署常用的開放原始碼容器叢集和協調流程解決方案。 本文件會逐步引導您使用 Azure 入口網站或Azure Resource Manager 快速入門範本來部署 Azure Container Service 叢集。 

> [!NOTE]
> Azure Container Service 中的 Kubernetes 支援目前為預覽狀態。

您也可以使用 [Azure CLI 2.0 (Preview)](container-service-create-acs-cluster-cli.md) 或 Azure Container Service API 來部署 Azure Container Service 叢集。



## <a name="prerequisites"></a>必要條件

* **Azure 訂用帳戶**：如果您沒有帳戶，請註冊[免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935)。

* **SSH 公開金鑰**︰透過入口網站或其中一個 Azure 快速入門範本部署時，您必須提供公開金鑰，以便對 Azure Container Service 虛擬機器進行驗證。 若要建立安全殼層 (SSH) 金鑰，請參閱 [OS X 和 Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) 和 [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md)指引。 

* **服務主體用戶端識別碼和密碼** (僅限 Kubernetes)：如需建立服務主體的詳細資訊和指引，請參閱[關於 Kubernetes 叢集的服務主體](container-service-kubernetes-service-principal.md)。



## <a name="create-a-cluster-by-using-the-azure-portal"></a>使用 Azure 入口網站建立叢集
1. 登入 Azure 入口網站、選取[新增]，並在 Azure Marketplace 中搜尋 [Azure Container Service]。

    ![Marketplace 中的 Azure Container Service](media/container-service-deployment/acs-portal1.png)  <br />

2. 選取 [Azure Container Service]，然後按一下 [建立]。

    ![建立容器服務](media/container-service-deployment/acs-portal2.png)  <br />

3. 輸入以下資訊：

    * **使用者名稱**：在 Azure Container Service 叢集中的每部虛擬機器和虛擬機器擴展集上，帳戶的使用者名稱。
    * **訂用帳戶**：選取 Azure 訂用帳戶。
    * **資源群組**：選取現有資源群組或建立新的群組。
    * **位置**：選取 Azure Container Service 部署的 Azure 區域。
    * **SSH 公開金鑰**：新增要用於對 Azure Container Service 的虛擬機器進行驗證的公開金鑰。 請務必不要讓此金鑰包含分行符號，而讓它包含 `ssh-rsa` 前置詞。 `username@domain` 後置詞是選擇性的。 金鑰應如下所示：**ssh-rsa AAAAB3Nz...<...>...UcyupgH azureuser@linuxvm**。 

4. 準備好繼續時請按一下 [確定]  。

    ![基本設定](media/container-service-deployment/acs-portal3.png)  <br />

5. 選取 [協調流程] 類型。 可用選項包括：

  * **DC/OS**：部署 DC/OS 叢集。
  * **Swarm**：部署 Docker Swarm 叢集。
  * **Kubernetes**：部署 Kubernetes 叢集


6. 準備好繼續時請按一下 [確定]  。

    ![選擇 Orchestrator](media/container-service-deployment/acs-portal4-new.png)  <br />

7. 如果在下拉式清單中選取 [Kubernetes]，您將需要輸入服務主體用戶端識別碼和服務主體用戶端密碼。 如需詳細資訊，請參閱[關於 Kubernetes 叢集的服務主體](container-service-kubernetes-service-principal.md)。

    ![輸入 Kubernetes 的服務主體](media/container-service-deployment/acs-portal10.png)  <br />

7. 在 [Azure Container Service] 設定刀鋒視窗中，輸入下列資訊：

    * **主要主機計數**：叢集中主要主機的數目。 如果選取了 Kubernetes，主要主機數目會設定為預設值 1。
    * **代理程式計數**：若為 Docker Swarm 和 Kubernetes，此值是代理程式擴展集內的初始代理程式數目。 若為 DC/OS，此值是私人擴展集內的初始代理程式數目。 此外，也會建立 DC/OS 的公用擴展集，其中包含預先決定的代理程式數目。 此公用擴展集內的代理程式數目是由叢集中已建立的主要主機數目來決定：一個主要主機需要一個公用代理程式，三或五個主要主機則需要兩個公用代理程式。
    * **代理程式虛擬機器大小**：代理程式虛擬機器的大小。
    * **DNS 前置詞**：全球唯一的名稱，用來做為服務之完整網域名稱的主要前置部分。
    * **VM 診斷**︰對於某些 Orchestrator，您可以選擇啟用 VM 診斷。

8. 準備好繼續時請按一下 [確定]  。

    ![Container Service 設定](media/container-service-deployment/acs-portal5.png)  <br />

9. 服務驗證完成後請按一下 [確定]  。

    ![驗證](media/container-service-deployment/acs-portal6.png)  <br />

10. 檢閱條款。 若要啟動部署程序，請按一下 [購買]。

    ![購買](media/container-service-deployment/acs-portal7.png)  <br />

    如果您已選擇將部署釘選到 Azure 入口網站，您就可以看到部署狀態。

    ![部署狀態](media/container-service-deployment/acs-portal8.png)  <br />

部署需要數分鐘才能完成。 然後，Azure Container Service 叢集便可供使用。



## <a name="create-a-cluster-by-using-a-quickstart-template"></a>使用快速入門範本建立叢集
Azure 快速入門範本可供在 Azure Container Service 中部署叢集。 提供的快速入門範本可以修改為包含其他或進階 Azure 組態。 若要使用 Azure 快速入門範本建立 Azure Container Service 叢集，您需要 Azure 訂用帳戶。 如果您沒有訂用帳戶，請註冊[免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935)。 

請使用範本和 Azure CLI 2.0 (Preview) 並遵循下列步驟來部署叢集 (請參閱[安裝和設定指示](/cli/azure/install-az-cli2.md))。

> [!NOTE] 
> 如果您在 Windows 系統上，可以使用 Azure PowerShell 並以類似的步驟來部署範本。 請參閱本節稍後的步驟。 您也可以透過[入口網站](../azure-resource-manager/resource-group-template-deploy-portal.md)或其他方法來部署範本。

1. 若要部署 DC/OS、Docker Swarm 或 Kubernetes 叢集，請從 GitHub 選取下列其中一個範本。 請注意，除了預設的 Orchestrator 選項有所不同外，DC/OS 和 Swarm 範本完全相同。

    * [DC/OS 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
    * [Swarm 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
    * [Kubernetes 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)

2. 登入您的 Azure 帳戶 (`az login`)，並確定 Azure CLI 已連接至 Azure 訂用帳戶。 您可以使用下列命令來查看預設訂用帳戶：

    ```azurecli
    az account show
    ```
    
    如果您有多個訂用帳戶，而且需要設定不同的預設訂用帳戶，請執行 `az account set --subscription` 並指定訂用帳戶識別碼或名稱。

3. 最佳作法是使用新的資源群組進行部署。 若要建立資源群組，請使用 `az group create` 命令指定資源群組名稱和位置： 

    ```azurecli
    az group create --name "RESOURCE_GROUP" --location "LOCATION"
    ```

4. 建立包含必要範本參數的 JSON 檔案。 下載名為 `azuredeploy.parameters.json` 的參數檔，其隨附於 GitHub 中的 Azure Container Service 範本 `azuredeploy.json`。 輸入您叢集所需的參數值。 

    例如，若要使用 [DC/OS 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)，請提供 `dnsNamePrefix` 和 `sshRSAPublicKey` 的參數值。 請參閱 `azuredeploy.json` 中的說明和其他參數的選項。  
 

5. 藉由下列命令傳遞部署參數檔，以建立 Container Service 叢集，其中︰

    * **RESOURCE_GROUP** 是您在上一個步驟中建立的資源群組名稱。
    * **DEPLOYMENT_NAME** (選用) 是您提供的部署名稱。
    * **TEMPLATE_URI** 是部署檔案 `azuredeploy.json` 的位置。 此 URI 必須是 Raw 檔案，而不是 GitHub UI 的指標。 若要尋找這個 URI，請在 GitHub 中選取 `azuredeploy.json` 檔案，然後按一下 [Raw] 按鈕。  

    ```azurecli
    az group deployment create -g RESOURCE_GROUP -n DEPLOYMENT_NAME --template-uri TEMPLATE_URI --parameters @azuredeploy.parameters.json
    ```

    您也可以在命令列上以 JSON 格式字串提供參數。 使用類似下列的命令：

    ```azurecli
    az group deployment create -g RESOURCE_GROUP -n DEPLOYMENT_NAME --template-uri TEMPLATE_URI --parameters "{ \"param1\": {\"value1\"} … }"
    ```

    > [!NOTE]
    > 部署需要數分鐘才能完成。
    > 

### <a name="equivalent-powershell-commands"></a>對等的 PowerShell 命令
您也可以使用 PowerShell 部署 Azure Container Service 叢集範本。 這份文件以 1.0 版的 [Azure PowerShell 模組](https://azure.microsoft.com/blog/azps-1-0/)為基礎。

1. 若要部署 DC/OS、Docker Swarm 或 Kubernetes 叢集，請選取下列其中一個範本。 請注意，除了預設的 Orchestrator 選項有所不同外，DC/OS 和 Swarm 範本完全相同。

    * [DC/OS 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
    * [Swarm 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
    * [Kubernetes 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)

2. 在 Azure 訂用帳戶中建立叢集之前，請確認您的 PowerShell 工作階段已登入 Azure。 您若要這麼做，可透過 `Get-AzureRMSubscription` 命令：

    ```powershell
    Get-AzureRmSubscription
    ```

3. 如果您需要登入 Azure，請使用 `Login-AzureRMAccount` 命令：

    ```powershell
    Login-AzureRmAccount
    ```

4. 最佳作法是使用新的資源群組進行部署。 若要建立資源群組，請使用 `New-AzureRmResourceGroup` 命令，並指定資源群組名稱和目的地區域：

    ```powershell
    New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
    ```

5. 建立資源群組後，您就可以使用下列命令來建立叢集。 `-TemplateUri` 參數指定所需範本的 URI。 當您執行此命令時，PowerShell 會提示您輸入部署參數值。

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
    ```

#### <a name="provide-template-parameters"></a>提供範本參數
如果您熟悉 PowerShell，您知道您可以輸入減號 (-) 然後按下 TAB 鍵，循環 Cmdlet 的可用參數。 相同的功能也適用於您在範本中定義的參數。 在您輸入範本名稱之後，Cmdlet 便會立即擷取範本、剖析參數，並將範本參數動態新增至命令。 這會讓指定範本參數值變得再容易不過了。 另外，如果您忘記必要參數值，則 PowerShell 會出現此值的提示。

以下是包含參數的完整命令。 您可以對資源名稱提供您自己的值。

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## <a name="next-steps"></a>後續步驟
既然您有一個可運作的叢集，請參閱這些文件來了解連接和管理的詳細資料：

* [連接到 Azure Container Service 叢集](container-service-connect.md)
* [使用 Azure Container Service 和 DC/OS](container-service-mesos-marathon-rest.md)
* [使用 Azure Container Service 和 Docker Swarm](container-service-docker-swarm.md)
* [使用 Azure Container Service 和 Kubernetes](container-service-kubernetes-walkthrough.md)



<!--HONumber=Feb17_HO1-->


