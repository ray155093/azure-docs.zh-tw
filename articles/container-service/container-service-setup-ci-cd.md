---
title: "包含 Azure Container Service 與 DC/OS 的 CI/CD | Microsoft Docs"
description: "如何讓多容器 Docker 應用程式完全自動建置和部署到執行 DC/OS 的 Azure Container Service 叢集。"
services: container-service
documentationcenter: 
author: spboyer
manager: wpickett
editor: 
tags: acs, azure-container-service
keywords: "Docker、容器、微服務、Mesos、Azure"
ms.assetid: b16b767a-2eaa-418a-becc-8c032713a1f2
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: johnsta
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: acfba48585b960a1ce39b77e35d292189aa9732b
ms.lasthandoff: 04/03/2017


---

# <a name="continuous-integration-and-deployment-of-multi-container-docker-applications-to-azure-container-service"></a>將多容器的 Docker 應用程式連續整合和部署到 Azure Container Service 
在本教學課程中，我們將討論如何讓多容器 Docker 應用程式完全自動建置和部署到執行 DC/OS 的 Azure Container Service 叢集。 雖然已知連續整合和部署 (CI/CD) 的優勢，但是在將容器整合到工作流程時有一些新的考量。 使用新的 Azure 容器登錄和 CLI 命令，設定您可以自訂的端對端流程。

## <a name="get-started"></a>開始使用
您可以在 OS X、Windows 或 Linux 上執行此逐步解說。
- 您需要 Azure 訂用帳戶。 如果您沒有帳戶，您可以註冊[免費帳戶](https://azure.microsoft.com/)。
- 安裝 [Azure CLI 2.0](/cli/azure/install-az-cli2)。

## <a name="what-well-create"></a>我們所要建立的項目
我們將討論應用程式的一些重要層面，以及我們所要設定的部署流程︰
* **應用程式是由多個服務所組成**。 各自在不同容器中執行的 Docker 資產、Dockerfile 以及 docker-compose.yml，用於在我們的應用程式中定義服務。 這些項目能夠獨立調整應用程式的各個部分，而且可以用不同的程式設計語言和架構撰寫每項服務。 可以跨一個或多個 Git 來源儲存機制 (工具目前支援 GitHub 或 Visual Studio Team Services) 裝載的應用程式程式碼。

* 此應用程式會在**使用 DC/OS 設定的 ACS 叢集**中執行。 容器 Orchestrator 可以管理叢集的健康狀態，並確保我們所需數量的容器執行個體持續執行。 

* **建置和部署容器映像的程序已完全自動化，完全不需要停機**。 我們希望小組的開發人員 'git push' 至某個分支，以便自動觸發整合程序。 也就是，建置並標記容器映像、在每個容器上執行測試，並將這些映像推送至 Docker 私人登錄。 從該處，新的映像會自動部署至 ACS 叢集上的共用生產前環境進一步測試。

* **將版本從某個環境提升至下一個環境**，例如從 [開發] -> [測試] -> [預備]-> [生產]。 每次提升至下游環境時，我們不需要重建容器映像，即可確保部署在前一個環境中測試的相同映像。 這個程序是「不可變服務」的概念，可降低未偵測到的錯誤潛入生產環境的可能性。

* 若要最有效地利用 ACS 叢集中的計算資源，我們可利用相同的叢集來執行可將組建和部署步驟完全放入容器的建置工作。 此叢集也可裝載多個開發/測試/生產環境。


## <a name="create-an-azure-container-service-cluster-configured-with-dcos"></a>建立使用 DC/OS 設定的 Azure Container Service 叢集

>[!IMPORTANT]
> 若要建立安全的叢集，請在呼叫 `az acs create` 時傳遞您的 SSH 公用金鑰檔案。 您可以使用 `--generate-ssh-keys` 選項，讓 Azure CLI 2.0 為您產生金鑰並同時進行傳遞，或者可以使用 `--ssh-key-value` 選項來傳遞您的金鑰路徑 (在 Linux 上的預設位置是 `~/.ssh/id_rsa.pub`，而在 Windows 上的預設位置是 `%HOMEPATH%\.ssh\id_rsa.pub`，但可加以變更)。
<!---Loc Comment: What do you mean by "you pass your SSH public key file to pass"? Thank you.--->
> 若要在 Linux 上建立 SSH 公開和私密金鑰檔案，請參閱[在 Linux 和 Mac 上建立 SSH 金鑰](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fcontainer-services%2ftoc.json)。 
> 若要在 Windows 上建立 SSH 公開和私密金鑰檔案，請參閱[在 Windows 上建立 SSH 金鑰](../virtual-machines/linux/ssh-from-windows.md?toc=%2fazure%2fcontainer-services%2ftoc.json)。 

1. 首先，在終端機視窗中輸入 [az login](/cli/azure/#login) 命令，以使用 Azure CLI 登入您的 Azure 訂用帳戶︰ 

    `az login`

1. 建立資源群組，我們會使用 [az group create](/cli/azure/group#create) 在其中放置叢集：
    
    `az group create --name myacs-rg --location westus`

    您可以指定最靠近您的 [Azure 資料中心區域](https://azure.microsoft.com/regions)。 

1. 使用 [az acs create](/cli/azure/acs#create) 並傳遞公開 SSH 金鑰檔案的路徑，以建立採用預設設定的 ACS 叢集︰ 

    ```azurecli
    az acs create \
    --resource-group myacs-rg 
    --name myacs \
    --dns-prefix myacs \
    --ssh-key-value ~/.ssh/id_rsa.pub
    ```
    
這個步驟需要幾分鐘的時間，所以可繼續閱讀後面的內容。  `acs create` 命令會傳回新建叢集的相關資訊 (或者您可以使用 `az acs list` 列出您訂用帳戶中的 ACS 叢集)。 如需更多 ACS 組態選項，[請深入了解如何建立和設定 ACS 叢集](container-service-deployment.md)。

## <a name="set-up-sample-code"></a>設定範例程式碼
建立叢集時，我們可以設定我們部署至 ACS 的範例程式碼。

1. [分支處理](https://help.github.com/articles/fork-a-repo/)範例 GitHub 儲存機制，您即可擁有自己的複本︰[https://github.com/azure-samples/container-service-dotnet-continuous-integration-multi-container.git](https://github.com/azure-samples/container-service-dotnet-continuous-integration-multi-container.git)。 應用程式基本上是 "hello world" 的多容器版本。
1. 在自己的 GitHub 帳戶中建立分支後，請在本機複製您電腦上的儲存機制︰

    ```bash
    git clone  https://github.com/your-github-account/container-service-dotnet-continuous-integration-multi-container.git
    cd container-service-dotnet-continuous-integration-multi-container
    ```
    
讓我們仔細看看此程式碼：
* `/service-a` 是以 Angular.js 為基礎且具有 Node.js 後端的 Web 應用程式。
* `/service-b` 是 .NET 核心服務，而且由 `service-a` 透過 REST 呼叫。
* `service-a` 和 `service-b` 在其每個目錄中都包含 `Dockerfile`，分別描述 Node.js 和 .NET 核心架構容器映像。 
* `docker-compose.yml` 會宣告已建立和部署的服務集。
* 除了 `service-a` 和 `service-b` 以外，第三個名為 `cache` 的服務會執行 `service-a` 可以使用的 Redis 快取。 `cache` 不同於前兩個服務，我們在來源儲存機制中沒有其程式碼。 然而，我們會從 Docker 中樞擷取預製的 `redis:alpine` 映像並將它部署至 ACS。
* `/service-a/server.js` 包含程式碼，其中的 `service-a` 會呼叫 `service-b` 和 `cache`。 請注意，`service-a` 程式碼會依據 `service-b` 和 `cache` 在 `docker-compose.yml` 中命名的方式參考它們。 如果我們透過 `docker-compose` 在本機電腦上執行這些服務，Docker 可確保服務全都適合透過網路依據名稱尋找彼此。 透過負載平衡的網路在叢集環境中執行服務，通常比在本機執行更加複雜。 好消息是 Azure CLI 命令會設定 CI/CD 流程，以確保此簡單的服務探索程式碼能繼續在 ACS 中照舊執行。 

    ![多容器範例應用程式概觀](media/container-service-setup-ci-cd/multi-container-sample-app-overview.PNG)

## <a name="set-up-continuous-integration-and-deployment"></a>設定持續整合及部署
1. 確定 ACS 叢集已經就緒︰執行 [az acs list](/cli/azure/acs#list) 並確認已列出我們的 ACS 叢集。 (請注意︰ACS 必須執行 DC/OS 1.8 或更新版本。)

1. [建立 GitHub 個人存取權杖](https://help.github.com/articles/creating-an-access-token-for-command-line-use/)，並且最少授與 `repo` 範圍。  別忘了將權杖複製到剪貼簿，因為我們將在下一個命令中用到它 (它會在 GitHub 儲存機制上設定 [webhook](https://help.github.com/articles/about-webhooks/))。 

1. 將目前的目錄設定為所複製來源儲存機制的根目錄，並使用您剛建立的 _&lt;GitHubPersonalAccessToken&gt; 來建立組建和發行管線︰
    
    `cd container-service-dotnet-continuous-integration-multi-container`

    ```azurecli
    az container release create \
    --target-name myacs \
    --target-resource-group myacs-rg \
    --remote-access-token <GitHubPersonalAccessToken>
    ```

    其中 `--target-name` 是 ACS 叢集的名稱，而 `--target-resource-group` 是 ACS 叢集的資源群組名稱。

第一次執行時，此命令可能需要一分鐘左右的時間才能完成。 完成後，會傳回有關它所建立之組建和發行管線的重要資訊︰
* `sourceRepo`：已針對來源儲存機制設定 [Webhook](https://help.github.com/articles/about-webhooks/)，所以每次將原始程式碼推送至該 Webhook 時，就會自動觸發組建和發行管線。  
* `vstsProject`：已設定 [Visual Studio Team Services](https://www.visualstudio.com/team-services/) (VSTS) 來「推動」工作流程 (在 ACS 中容器內的實際組建和部署工作)。 如果您想要使用特定的 VSTS 帳戶和專案，您可以使用 `--vsts-account-name` 和 `--vsts-project-name` 參數進行定義。
* `buildDefinition`︰定義可針對每個組建執行的工作。 系統會針對 docker-compose.yml 中定義的每個服務產生容器映像，然後推送至 Docker 容器登錄。 
* `containerRegistry`：Azure 容器登錄是可執行 Docker 容器登錄的受管理服務。 使用預設名稱可建立新的 Azure Container Registry，您也可以透過 `--registry-name` 參數指定 Azure Container Registry 名稱。
* `releaseDefinition`︰定義針對每個部署執行的工作。 系統會從容器登錄提取 docker-compose.yml 中定義之服務的容器映像，並且部署至 ACS 叢集。 預設會建立三種環境︰[開發]、[測試] 和 [生產]。 發行定義已預設為在每次建置成功完成時，便會自動部署到 [開發] 環境。 您可以手動將發行提升為 [測試] 或 [生產]，而不需要重建。 您可以在 VSTS 中自訂預設流程。 
* `containerService`︰目標 ACS 叢集 (必須執行 DC/OS 1.8)。


如果您已經有名為 `myregistry` 的現有 Azure Container Registry，下列程式碼片段是您可以輸入的範例命令。 使用位於 `myvstsaccount.visualstudio.com` 的 VSTS 帳戶，以及現有的 VSTS 專案 `myvstsproject` 來建立和建置發行定義：
        
```azurecli
az container release create \
--target-name myacs \
--target-resource-group myacs-rg \
--registry-name myregistry \
--vsts-account-name myvstsaccount \
--vsts-project-name myvstsproject \
--remote-access-token <GitHubPersonalAccessToken>
```

## <a name="view-deployment-pipeline-progress"></a>檢視部署管線進度
建立管線後，就會自動開始首次組建和部署。 每次將程式碼推送至來源儲存機制時，會觸發後續建置。 將瀏覽器開啟到組建定義或發行定義 URL，即可檢查組建和/或發行的進度。

執行下列命令，您一定可以找到與 ACS 叢集相關聯的發行定義 URL︰

```azurecli
az container release list \
--target-name myacs \
--target-resource-group myacs-rg
``` 

![VSTS 組建](media/container-service-setup-ci-cd/vsts-build.PNG)

顯示多容器應用程式之 CI 結果的 VSTS螢幕擷取畫面

![VSTS 發行](media/container-service-setup-ci-cd/vsts-release.PNG)

具有多個環境的 VSTS docker-compose 版本

## <a name="view-the-application"></a>檢視應用程式
此時，我們的應用程式會部署至共用開發環境，而且不會公開。 在此同時，使用 DC/OS 儀表板來檢視和管理我們的服務，以及[建立 DC/OS 相關端點的 SSH 通道](https://azure.microsoft.com/documentation/articles/container-service-connect/)或執行 Azure CLI 所提供的便利命令。

> [!IMPORTANT]
> 在第一次部署時，先確認 VSTS 版本部署成功，再繼續執行。

> [!NOTE]
> 僅限 Windows︰您必須設定 [Pageant](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) 才能完成此區段。
> 
>* 啟動 *PuttyGen* 並載入用來建立 ACS 叢集的 SSH 私密金鑰 (%homepath%\id_rsa)。
>* 在同一個資料夾中將 SSH 私密金鑰儲存為 `id_rsa.ppk`。
>* 啟動 *Pageant* - 它會開始執行並在右下方的系統匣中顯示圖示。
>* 以滑鼠右鍵按一下系統匣，然後選取 [新增金鑰]。
>* 新增 `id_rsa.ppk` 檔案。
> 
> 

1. 使用 Azure CLI 便利命令，開啟 ACS 叢集的 DC/OS 儀表板︰
    
    `az acs dcos browse -g myacs-rg -n myacs`

    * `-g` 是目標 ACS 叢集的資源群組名稱。
    * `-n` 是目標 ACS 叢集的名稱。
    * 系統會提示您輸入本機帳戶密碼，因為此命令需要系統管理員權限。 此命令會建立 DC/OS 端點的 SSH 通道、將預設瀏覽器開啟至該端點，並且暫時設定瀏覽器的 Web proxy。 

    > [!TIP]
    > 如果您需要查閱 ACS 叢集的名稱，執行 `az acs list` 即可列出訂用帳戶中的所有 ACS 叢集。 

1. 在 DC/OS 儀表板中，按一下左導覽功能表上的 [服務] ([http://localhost/#/services](http://localhost/#/services))。 透過我們的管線部署的服務會分組在名為 dev 的根資料夾之下 (以 VSTS 發行定義中的環境命名)。 

![Marathon UI](media/container-service-setup-ci-cd/marathon-ui.png)

您可以在 DC/OS 儀表板中執行許多有用的事情

* 追蹤每項服務的部署狀態
* 檢視 CPU 和記憶體需求
* 檢視記錄檔
* 調整每項服務的執行個體數目

**若要檢視 service-a 的 Web 應用程式**︰請從 dev 根資料夾開始，然後向下切入資料夾階層，直到您到達 `service-a` 為止。 此檢視會列出 `service-a` 的執行中工作 (或容器執行個體)。

![service a](media/container-service-setup-ci-cd/service-a.png)

按一下工作來開啟它的檢視，然後按一下其中一個可用的端點。

![服務工作](media/container-service-setup-ci-cd/service-a-task.PNG)

我們的簡單 Web 應用程式會呼叫 `service-a`，而它會呼叫 `service-b` 並傳回 hello world 訊息。 每次提出要求時，Redis 上的計數器會累加。

![服務 Web 應用程式](media/container-service-setup-ci-cd/service-a-web-app.PNG)

### <a name="optional-reaching-a-service-from-the-command-line"></a>(選擇性) 從命令列觸達服務
如果您想要從命令列透過 curl 觸達服務︰

1. 執行 `az acs dcos browse --verbose -g myacs-rg -n myacs`，並且在輸入密碼之後，記下 "Proxy running on <ip-address>:<port>" 這一行。
1. 在新的終端機視窗中，輸入︰

    `export http_proxy=http://<web-proxy-service-ip>:<portnumber>`

    例如：`export http_proxy=http://127.0.0.1:55405`

1. 現在您可以針對您的服務端點 (`curl http://service-url`) 進行 curl 處理，其中 `service-url` 是當您從 Marathon UI 瀏覽至服務端點所會看到的位址。 若要從命令列取消設定 http_proxy 變數，請輸入 `unset http_proxy`。
 

## <a name="scale-services"></a>調整服務
我們在 DC/OS 儀表板中，調整我們的服務。
1. 瀏覽至 *dev* 子資料夾中的應用程式。
1. 將滑鼠移至 `service-b`，按一下齒輪圖示，然後選取 [調整]。

    ![動作功能表](media/container-service-setup-ci-cd/marathon-ui-action-menu.PNG)

1. 將數字提高至 3，然後按一下 [調整服務]。

    ![調整服務](media/container-service-setup-ci-cd/marathon-ui-scale-service.png)

1. 瀏覽回到執行中的 Web 應用程式，然後重複按 [請再說一次] 按鈕。 請注意，`service-b` 引動過程會開始將資源循環配置於一組主機名稱，而 `service-a` 的單一執行個體會繼續回報相同的主機。   

## <a name="promote-a-release-to-downstream-environments-without-rebuilding-container-images"></a>將版本提升至下游環境，而不需重建容器映像
VSTS 發行管線預設會設定三種環境︰[開發]、[測試] 和 [生產]。 到目前為止，我們已經部署到 [開發] 環境。 讓我們看看如何將版本提升至下一個下游環境 (測試)，而不需重建容器映像。 此工作流程可確保我們部署的映像與在前一個環境中測試的映像完全相同，而這就是「不可變服務」的概念，可降低未偵測到的錯誤潛入生產環境的可能性。

1. 在 VSTS Web UI 中，瀏覽至 [版本]

    ![VSTS 版本功能表](media/container-service-setup-ci-cd/vsts-releases-menu.PNG)

1. 開啟最新的版本。

1. 在發行定義的功能表列中，按一下 [部署]，然後選取 [測試] 做為想要部署的下一個環境，以啟動新的部署，並重複使用與先前部署至 [開發] 環境相同的映像。 如果您想要更詳細地追蹤部署，請按一下 [記錄檔]。

    ![VSTS 升級版本](media/container-service-setup-ci-cd/vsts-promote-release.PNG)

成功部署到 [測試] 環境後，Marathon UI 中名為 [測試] 的新根資料夾會包含針對該環境執行中的服務。 

![DC/OS 中每個環境的子資料夾](media/container-service-setup-ci-cd/marathon-ui-dev-test-environments.png)

## <a name="trigger-a-new-build-and-deployment"></a>觸發新的組建和部署
讓我們模擬我們小組的開發人員將程式碼變更推送至來源儲存機制後，會發生什麼狀況。

1. 返回程式碼編輯器，開啟 `service-a/public/index.html`。 
1. 將這行程式碼︰

    `<h2>Server Says</h2>`

    修改為：

    `<h2>Server Says Hello</h2>`

1. 儲存檔案，然後認可程式碼變更並推送至來源儲存機制。

    ```bash
    git commit -am 'updated title'
    git push
    ```

認可會自動開始新的組建，而新的版本會部署到 [開發] 環境。 下游環境中的服務 ([測試] 或 [生產]) 會維持不變，直到我們決定將特定版本提升到該環境為止。

如果您在 VSTS 中開啟組建定義，您會看到類似以下的畫面︰ 

![git push 所觸發的新組建](media/container-service-setup-ci-cd/new-build.png)



## <a name="expose-public-endpoint-for-production"></a>公開生產環境的公用端點

1. 將下列 yaml 程式碼新增至名為 `docker-compose.env.production.yml` 的新檔案，而該檔案為於來源儲存機制的根資料夾。 這會新增一個可導致對 `service-a` 公開公用端點的標籤。 
    
    ```yaml
    version: "2"
    services:
      service-a:
        labels:
          com.microsoft.acs.dcos.marathon.vhost: "<FQDN, or custom domain>"
    ```

    * 如需標籤值，您可以指定 ACS 代理程式的完整網域名稱 (FQDN) 或自訂網域 (例如，app.contoso.com) 的 URL。 若要尋找 ACS 代理程式的 FQDN，請執行 `az acs list` 命令，並檢查 `agentPoolProfiles.fqdn` 的屬性。 例如， `myacsagents.westus.cloudapp.azure.com`。
    * 範例應用程式預設是接聽連接埠 80，對於讓 Docker 應用程式接聽其他連接埠 (例如 `port 8080` 或 `443`) 的使用者，請將連接埠號碼附加到 FQDN。 例如， `myacsagents.westus.cloudapp.azure.com:8080`。 不過，當您嘗試從外部存取應用程式時，您必須於連接埠 80 對它進行查詢。
    * 遵循下列檔案名稱慣例 docker-compose.env.*environment-name*.yml，這些設定只會影響已命名的環境 (在此情況下是名為 [生產] 的環境)。 檢查 VSTS 中的發行定義，每個環境的部署工作是否已設定為從按此慣例命名的 docker-compose 檔案讀取。

1. 認可此檔案並推送至主要來源儲存機制，以啟動另一個組建。

    ```bash
    git add .
    git commit -am "expose public port for service-a"
    git push
    ```

1. 等到更新建置並部署至 [開發] 環境後，將它提升至 [測試] 環境，然後再提升至 [生產] 環境。 (基於本教學課程的目的，您可以直接部署到 [生產] 環境，但實際上也可以只部署到下一個下游環境。)

1. (選擇性) **如果您為 vhost 指定了自訂網域** (例如 app.contoso.com)，請在網域提供者的設定中新增 DNS 記錄。 登入網域提供者的系統管理 UI 並新增 DNS 記錄，如下所示︰

    * 類型︰CNAME
    * 主機︰您的自訂網域，例如 app.contoso.com
    * 答案︰ACS 代理程式 FQDN，例如 myacsagents.westus.cloudapp.azure.com
    * TTL (選用)︰有時候，您的網域提供者會讓您編輯 TTL。 較低的值會導致 DNS 記錄更新更快速地傳播。   

1. 將此版本部署到 [生產] 環境後，任何人都可以存取該版本。 將瀏覽器開啟至您針對 `com.microsoft.acs.dcos.marathon.vhost` 標籤指定的 URL。 (請注意︰生產前環境的版本會繼續私用)。

## <a name="summary"></a>摘要
恭喜！ 您已了解如何使用 DC/OS 建立 ACS 叢集，以及針對多容器應用程式設定完全自動化和容器化的組建和部署管線。

請瀏覽一些後續步驟︰
* **調整 VSTS 代理程式。** 如果您需要更多輸送量才能執行建置和發行工作，您可以增加 VSTS 代理程式執行個體的數目。 瀏覽至 DC/OS 儀表板中的 [服務]，開啟 vsts-agents 資料夾，然後體驗 VSTS 代理程式執行個體的數目調整。
* **整合單元測試。** 此 GitHub 儲存機制說明如何進行在容器中執行的單元測試和整合測試，並將它們包含在建置工作中︰[https://github.com/mindaro/sample-app](https://github.com/mindaro/sample-app)。 
    * 提示︰查看儲存機制中的這些檔案︰`service-a/unit-tests.js`、`service-a/service-tests.js`、`docker-compose.ci.unit-tests.yml` 和 `docker-compose.ci.service-tests.yml`。

## <a name="clean-up"></a>清除
若要限制與本教學課程相關的計算費用，請執行下列命令，並記下與 ACS 叢集相關的部署管線資源︰

```azurecli    
az container release list --resource-name myacs --resource-group myacs-rg
```

刪除 ACS 叢集︰
1. 登入 [Azure 入口網站](https://portal.azure.com)
1. 查詢含有您的 ACS 虛擬機器的資源群組。
1. 開啟資源群組的刀鋒視窗 UI，然後在刀鋒視窗的命令列中按一下 [刪除]。

刪除 Azure 容器登錄︰在 Azure 入口網站中，搜尋 Azure Container Registry 並加以刪除。 

[Visual Studio Team Services 帳戶會針對前五個使用者提供免費的基本存取層級](https://azure.microsoft.com/en-us/pricing/details/visual-studio-team-services/)，但您可以刪除組建和發行定義。

刪除 VSTS 組建定義：
        
1. 在瀏覽器中開啟組建定義 URL，然後按一下 [組建定義] 連結 (位於您目前檢視的組建定義名稱旁邊)。
2. 按一下您要刪除之組建定義旁邊的動作功能表，然後選取 [刪除定義]

`![刪除 VSTS 組建定義](media/container-service-setup-ci-cd/vsts-delete-build-def.png) 

刪除 VSTS 發行定義：

1. 在瀏覽器中開啟發行定義 URL。
2. 在左手邊的發行定義清單中，按一下您想要刪除之發行定義旁邊的下拉式清單，然後選取 [刪除]。

`![刪除 VSTS 發行定義](media/container-service-setup-ci-cd/vsts-delete-release-def.png)

