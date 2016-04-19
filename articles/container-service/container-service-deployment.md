<properties
   pageTitle="部署 Azure 容器服務叢集 | Microsoft Azure"
   description="使用 Azure 入口網站、Azure CLI 或 PowerShell 來部署 Azure 容器服務叢集。"
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker、容器、微服務、Mesos、Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="rogardle"/>

# 部署 Azure 容器服務叢集

Azure 容器服務支援快速部署常用的開放原始碼容器叢集和協調流程解決方案。透過 Azure 容器服務，您可以使用 Azure Resource Manager 範本或 Azure 入口網站來部署 Marathon Mesos 和 Docker Swarm 叢集。您會使用 Azure 虛擬機器擴展集來部署這些叢集；這些叢集會利用 Azure 網路功能與儲存體供應項目。若要存取 Azure 容器服務，您需要有 Azure 訂用帳戶。如果您沒有帳戶，您可以註冊[免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935)。

本文件會逐步引導您使用 [Azure 入口網站](#creating-a-service-using-the-azure-portal)、[Azure 命令列介面 (CLI)](#creating-a-service-using-the-azure-cli) 和 [Azure PowerShell 模組](#creating-a-service-using-powershell)來部署 Azure 容器服務叢集。

## 使用 Azure 入口網站建立服務

若要部署 Mesos 或 Docker Swarm 叢集，請從 GitHub 選取下列其中一個範本。請注意，除了預設的 Orchestrator 選項有所不同外，下列兩個範本完全相同。

* [Mesos 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* [Swarm 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

當您選取任一範本頁面上的 [部署至 Azure] 按鈕時，Azure 入口網站便會開啟，並附有如下所示的表單：<br />

![使用表單來建立部署](media/create-mesos-params.png) <br />

請根據本指導方針來填寫表單，並在完成時選取 [確定]。<br />

欄位 | 說明
----------------|-----------
DNSNAMEPREFIX | 這必須是全球唯一的值。它會用來為每個服務的主要部分建立 DNS 名稱。請參閱本文稍候的詳細資訊。
AGENTCOUNT | 這是將在 Azure 容器服務代理程式擴展集中建立的虛擬機器數目。
AGENTVMSIZE | 這是代理程式虛擬機器的大小。請小心選取可提供足夠資源來裝載最大容器的大小。
ADMINUSERNAME | 這是在 Azure 容器服務叢集中的每一個虛擬機器和虛擬機器擴展集上，將用於帳戶的使用者名稱。
ORCHESTRATORTYPE| 這是要在 Azure 容器服務叢集中使用的 Orchestrator 類型。
MASTERCOUNT | 這是設定做為叢集主機的虛擬機器數目。您可以選取 1，但這樣無法提供叢集的任何恢復功能；此值只建議用於測試。生產叢集則建議選取 3 或 5。
SSHRSAPUBLICKEY | 您必須使用安全殼層 (SSH) 向虛擬機器進行驗證。這是您加入公開金鑰的地方。將金鑰值貼入此方塊時請特別小心。某些編輯器會在內容中插入分行符號，這將會破壞金鑰。請確認金鑰不含分行符號，而且包含 'ssh rsa' 前置詞和 'username@domain' 後置詞。看起來應該類似於 'ssh-rsa AAAAB3Nz...SNIPPEDCONTENT...UcyupgH azureuser@linuxvm'。如果您需要建立 SSH 金鑰，您可以在 Azure 文件網站上找到 [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md) 和 [Linux](../virtual-machines/virtual-machines-linux-ssh-from-linux.md) 的指引。

為參數設定適當值後，請選取 [確定]。接下來，提供資源群組名稱、選取區域，然後檢閱並同意法律條款。

> [AZURE.NOTE] 在預覽期間，Azure 容器服務不會有任何費用。只會收取標準計算費用，例如虛擬機器、儲存體和網路費用。

![選取資源群組](media/resourcegroup.png)

最後，選取 [建立]。返回儀表板。假設您未清除 [部署] 刀鋒視窗上的 [釘選到儀表板] 核取方塊，則會看到類似如下的動畫圖格：

![部署範本圖格](media/deploy.png)

正在建立叢集，現在可休息一下。叢集建立好後，您會看到一些刀鋒視窗，其中會顯示構成 Azure 容器服務叢集的資源。

![已完成](media/final.png)

## 使用 Azure CLI 建立服務

若要使用命令列建立 Azure 容器服務的執行個體，您需要有 Azure 訂用帳戶。如果您沒有帳戶，您可以註冊[免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935)。您也必須安裝並設定 Azure CLI。

若要部署 Mesos 或 Docker Swarm 叢集，請從 GitHub 選取下列其中一個範本。請注意，除了預設的 Orchestrator 選項有所不同外，下列兩個範本完全相同。

* [Mesos 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* [Swarm 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

接下來，請確定 Azure CLI 已連線至 Azure 訂用帳戶。您可以使用下列命令來達成目的：

```bash
Azure account show
```
如果沒有傳回 Azure 帳戶，請使用下列命令將 CLI 登入 Azure。

```bash
azure login -u user@domain.com
```

接下來，設定 Azure CLI 工具來使用 Azure Resource Manager。

```bash
azure config mode arm
```

如果您想要在新的資源群組中建立叢集，您必須先建立資源群組。使用下列命令，其中 `GROUP_NAME` 是您想要建立的資源群組的名稱，`REGION` 是您想要建立資源群組的區域。

```bash
azure group create GROUP_NAME REGION
```

建立好資源群組後，您就可以使用這個命令來建立叢集，其中：

- **RESOURCE\_GROUP** 是您想要用於此服務的資源群組名稱。
- **DEPLOYMENT\_NAME** 是此部署的名稱。
- **TEMPLATE\_URI** 是部署檔案的位置。請注意，這必須是 RAW 檔案，而不是 GitHub UI 的指標。若要尋找這個 URL，請在 GitHub 中選取 azuredeploy.json 檔案，並選取 [RAW] 按鈕。

> [AZURE.NOTE] 當您執行此命令時，殼層會提示您輸入部署參數值。

```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI
```

### 提供範本參數

此版本的命令需要您以互動方式定義參數。如果您想要提供參數 (例如 JSON 格式的字串)，您可以使用 `-p` 參數來這樣做。例如：

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
 ```

或者，您也可以使用 `-e` 參數來提供 JSON 格式的參數檔案：

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON
 ```

若要查看名為 `azuredeploy.parameters.json` 的範例參數檔，請在 GitHub 的 Azure 容器服務範本中尋找。

## 使用 PowerShell 建立服務

您也可以使用 PowerShell 部署 Azure 容器服務叢集。這份文件以 1.0 版的 [Azure PowerShell 模組](https://azure.microsoft.com/blog/azps-1-0/)為基礎。

若要部署 Mesos 或 Docker Swarm 叢集，請選取下列其中一個範本。請注意，除了預設的 Orchestrator 選項有所不同外，下列兩個範本完全相同。

* [Mesos 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* [Swarm 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

在 Azure 訂用帳戶中建立叢集之前，請確認您的 PowerShell 工作階段已登入 Azure。您若要這麼做，可透過 `Get-AzureRMSubscription` 命令：

```powershell
Get-AzureRmSubscription
```

如果您需要登入 Azure，請使用 `Login-AzureRMAccount` 命令：

```powershell
Login-AzureRmAccount
```

如果要部署到新的資源群組，您必須先建立資源群組。若要建立新的資源群組，請使用 `New-AzureRmResourceGroup` 命令，並指定資源群組名稱和目的地區域：

```powershell
New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
```

建立資源群組後，您就可以使用下列命令來建立叢集。`-TemplateUri` 參數指定所需範本的 URI。當您執行此命令時，PowerShell 會提示您輸入部署參數值。

```powershell
New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
 ```

### 提供範本參數

如果您熟悉 PowerShell，您知道您可以輸入減號 (-) 然後按下 TAB 鍵，循環 Cmdlet 的可用參數。相同的功能也適用於您在範本中定義的參數。在您輸入範本名稱之後，Cmdlet 便會立即擷取範本、剖析參數，並將範本參數動態新增至命令。這會讓指定範本參數值變得再容易不過了。另外，如果您忘記必要參數值，則 PowerShell 會出現此值的提示。

以下是包含參數的完整命令。您可以對資源名稱提供您自己的值。

```
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## 後續步驟

現在您已擁有正常運作的叢集，接下來請參閱下列文章來了解連接和管理的詳細資料。

- [連接到 Azure 容器服務叢集](./container-service-connect.md)
- [使用 Azure 容器服務和 Mesos](./container-service-mesos-marathon-rest.md)

<!---HONumber=AcomDC_0413_2016-->