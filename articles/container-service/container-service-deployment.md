---
title: "部署 Azure Container Service 叢集 | Microsoft Docs"
description: "使用 Azure 入口網站、Azure CLI 或 PowerShell 來部署 Azure 容器服務叢集。"
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
ms.date: 12/20/2016
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: dc7ce9f0524567b861a40940f02cd07e0b7b22bf
ms.openlocfilehash: 52331c92a4e3e254c044c9cba85a937b6ba95011


---
# <a name="deploy-an-azure-container-service-cluster"></a>部署 Azure 容器服務叢集
Azure 容器服務支援快速部署常用的開放原始碼容器叢集和協調流程解決方案。 透過 Azure Container Service，您可以使用 Azure Resource Manager 範本或 Azure 入口網站來部署 DC/OS、Kubernetes 和 Docker Swarm 叢集。 您會使用 Azure 虛擬機器擴展集來部署這些叢集，而這些叢集會利用 Azure 網路功能與儲存體供應項目。 若要存取 Azure 容器服務，您需要有 Azure 訂用帳戶。 如果您沒有帳戶，您可以註冊 [免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935)。

> [!NOTE]
> Azure Container Service 中的 Kubernetes 支援目前為預覽狀態。
>

本文件會逐步引導您使用 [Azure 入口網站](#creating-a-service-using-the-azure-portal)、[Azure 命令列介面 (CLI)](#creating-a-service-using-the-azure-cli) 和 [Azure PowerShell 模組](#creating-a-service-using-powershell)來部署 Azure 容器服務叢集。  

## <a name="create-a-service-by-using-the-azure-portal"></a>使用 Azure 入口網站建立服務
1. 登入 Azure 入口網站、選取[新增]，並在 Azure Marketplace 中搜尋 [Azure 容器服務]。

    ![建立部署 1](media/acs-portal1.png)  <br />

2. 選取 [Azure 容器服務]，然後按一下 [建立]。

    ![建立部署 2](media/acs-portal2.png)  <br />

3. 輸入以下資訊：

    * **使用者名稱**：這是在 Azure 容器服務叢集中的每一個虛擬機器和虛擬機器擴展集上，將用於帳戶的使用者名稱。
    * **訂用帳戶**：選取 Azure 訂用帳戶。
    * **資源群組**：選取現有資源群組或建立新的群組。
    * **位置**：選取 Azure 容器服務部署的 Azure 區域。
    * **SSH 公開金鑰**：新增將用於對 Azure 容器服務的虛擬機器進行驗證的公開金鑰。 請務必不要讓此金鑰包含分行符號，並且務必讓它包含 'ssh-rsa' 前置詞和 'username@domain' 後置詞。 看起來應該類似於下列：**ssh-rsa AAAAB3Nz...<...>...UcyupgH azureuser@linuxvm**。 如需建立安全殼層 (SSH) 金鑰的指引，請參閱 [Linux](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-linux/) 和 [Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-windows/) 文章。

4. 準備好繼續時請按一下 [確定]  。

    ![建立部署 3](media/acs-portal3.png)  <br />

5. 選取 [協調流程] 類型。 可用選項包括：

    * **DC/OS**：部署 DC/OS 叢集。
    * **Swarm**：部署 Docker Swarm 叢集。
    * **Kubernetes**：部署 Kubernetes 叢集。

6. 準備好繼續時請按一下 [確定]  。

    ![建立部署 4](media/acs-portal4-new.png)  <br />

7. 如果在下拉式清單中選取 [Kubernetes]，您將需要輸入服務主體用戶端識別碼和服務主體用戶端密碼。 如需詳細資訊，請參閱[關於 Kubernetes 叢集的服務主體](container-service-kubernetes-service-principal.md)。

    ![建立部署 4.5](media/acs-portal10.PNG)  <br />

7. 在 [Azure Container Service] 設定刀鋒視窗中，輸入下列資訊：

    * **主要主機計數**：叢集中主要主機的數目。 如果選取了 [Kubernetes]，主要主機數目會設定為預設值 1
    * **代理程式計數**：若為 Docker Swarm 和 Kubernetes，這會是代理程式擴展集內的初始代理程式數目。 若為 DC/OS，這會是私人調整集內的初始代理程式數目。 此外，也會建立包含預先決定的代理程式數目的公用調整集。 此公用調整集內的代理程式數目是由叢集中已建立的主要主機數目來決定：一個主要主機需要一個公用代理程式，三或五個主要主機則需要兩個公用代理程式。
    * **代理程式虛擬機器大小**：代理程式虛擬機器的大小。
    * **DNS 前置詞**：全球唯一的名稱，將用來做為服務之完整網域名稱的主要前置部分。

8. 準備好繼續時請按一下 [確定]  。

    ![建立部署 5](media/acs-portal5.png)  <br />

9. 服務驗證完成後請按一下 [確定]  。

    ![建立部署 6](media/acs-portal6.png)  <br />

10. 按一下 [購買] 以啟動部署程序。

    ![建立部署 7](media/acs-portal7.png)  <br />

    如果您已選擇將部署釘選到 Azure 入口網站，您就可以看到部署狀態。

    ![建立部署 8](media/acs-portal8.png)  <br />

當部署完成時，Azure 容器服務叢集便可供使用。

## <a name="create-a-service-by-using-the-azure-cli"></a>使用 Azure CLI 建立服務
若要使用命令列建立 Azure 容器服務的執行個體，您需要有 Azure 訂用帳戶。 如果您沒有帳戶，您可以註冊 [免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935)。 您也必須[安裝](../xplat-cli-install.md)並[設定](../xplat-cli-connect.md) Azure CLI。

1. 若要部署 DC/OS、Docker Swarm 或 Kubernetes 叢集，請從 GitHub 選取下列其中一個範本。 

    * [DC/OS 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
    * [Swarm 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
    * [Kubernetes 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)

2. 接下來，請確定 Azure CLI 已連線至 Azure 訂用帳戶。 您可以使用下列命令來達成目的：

    ```bash
    azure account show
    ```
    如果沒有傳回 Azure 帳戶，請使用下列命令將 CLI 登入 Azure。

    ```bash
    azure login -u user@domain.com
    ```

3. 設定 Azure CLI 工具來使用 Azure Resource Manager。

    ```bash
    azure config mode arm
    ```

4. 使用下列命令建立 Azure 資源群組和容器服務叢集，其中︰

    * **RESOURCE_GROUP** 是您想要用於此服務的資源群組名稱。
    * **LOCATION** 是要建立資源群組和 Azure 容器服務部署的 Azure 區域。
    * **TEMPLATE_URI** 是部署檔案的位置。 請注意，這必須是 RAW 檔案，而不是 GitHub UI 的指標。 若要尋找這個 URL，請在 GitHub 中選取 azuredeploy.json 檔案，並按一下 [RAW]  按鈕。

    > [!NOTE]
    > 當您執行此命令時，殼層會提示您輸入部署參數值。
    > 

    ```bash
    azure group create -n RESOURCE_GROUP DEPLOYMENT_NAME -l LOCATION --template-uri TEMPLATE_URI
    ```

### <a name="provide-template-parameters"></a>提供範本參數
此版本的命令需要您以互動方式定義參數。 如果您想要提供參數 (例如 JSON 格式的字串)，您可以使用 `-p` 參數來這樣做。 例如：

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
```

或者，您也可以使用 `-e` 參數來提供 JSON 格式的參數檔案：

```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON
```

若要查看名為 `azuredeploy.parameters.json`的範例參數檔，請在 GitHub 的 Azure 容器服務範本中尋找。

## <a name="create-a-service-by-using-powershell"></a>使用 PowerShell 建立服務
您也可以使用 PowerShell 部署 Azure 容器服務叢集。 這份文件以 1.0 版的 [Azure PowerShell 模組](https://azure.microsoft.com/blog/azps-1-0/)為基礎。

1. 若要部署 DC/OS、Docker Swarm 或 Kubernetes 叢集，請選取下列其中一個範本。 請注意，除了預設的 Orchestrator 選項有所不同外，下列兩個範本完全相同。

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

4. 如果要部署到新的資源群組，您必須先建立資源群組。 若要建立新的資源群組，請使用 `New-AzureRmResourceGroup` 命令，並指定資源群組名稱和目的地區域：

    ```powershell
    New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
    ```

5. 建立資源群組後，您就可以使用下列命令來建立叢集。 `-TemplateUri` 參數指定所需範本的 URI。 當您執行此命令時，PowerShell 會提示您輸入部署參數值。

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
    ```

### <a name="provide-template-parameters"></a>提供範本參數
如果您熟悉 PowerShell，您知道您可以輸入減號 (-) 然後按下 TAB 鍵，循環 Cmdlet 的可用參數。 相同的功能也適用於您在範本中定義的參數。 在您輸入範本名稱之後，Cmdlet 便會立即擷取範本、剖析參數，並將範本參數動態新增至命令。 這會讓指定範本參數值變得再容易不過了。 另外，如果您忘記必要參數值，則 PowerShell 會出現此值的提示。

以下是包含參數的完整命令。 您可以對資源名稱提供您自己的值。

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## <a name="next-steps"></a>後續步驟
既然您有一個可運作的叢集，請參閱這些文件來了解連接和管理的詳細資料：

* [連接到 Azure 容器服務叢集](container-service-connect.md)
* [使用 Azure 容器服務和 DC/OS](container-service-mesos-marathon-rest.md)
* [使用 Azure 容器服務和 Docker Swarm](container-service-docker-swarm.md)
* [使用 Azure Container Service 和 Kubernetes](container-service-kubernetes-walkthrough.md)




<!--HONumber=Dec16_HO3-->


