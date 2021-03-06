---
title: "Azure Log Analytics 中的容器解決方案 | Microsoft Docs"
description: "Log Analytics 中的容器方案可協助您在單一位置檢視及管理 Docker 和 Windows 容器主機。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: e1e4b52b-92d5-4bfa-8a09-ff8c6b5a9f78
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: banders
ms.translationtype: HT
ms.sourcegitcommit: 79bebd10784ec74b4800e19576cbec253acf1be7
ms.openlocfilehash: 524c63d358ce22c10b7a23e5bcf0b33e9f2e5f26
ms.contentlocale: zh-tw
ms.lasthandoff: 08/03/2017

---
# <a name="containers-preview-solution-in-log-analytics"></a>Log Analytics 中的容器 (預覽) 解決方案

![容器符號](./media/log-analytics-containers/containers-symbol.png)

本文說明如何設定及使用 Log Analytics 中的容器方案，協助您在單一位置檢視及管理 Docker 和 Windows 容器主機。 Docker 是用來建立容器的軟體虛擬化系統，自動將軟體部署至其 IT 基礎結構。

利用此解決方案，您可以查看容器主機上有哪些正在執行的容器，以及容器中有哪些正在執行的映像。 您可以檢視詳細的稽核資訊，其中顯示搭配容器使用的命令。 而且，藉由檢視及搜尋集中式記錄檔，而不需從遠端檢視 Docker 或 Windows 主機，即可針對容器進行疑難排解。 您可能會找到有雜訊且耗用過多主機資源的容器。 而且，您可以檢視容器的集中式 CPU、記憶體、儲存體以及網路使用量和效能資訊。 您可以在執行 Windows 的電腦上集中管理，並從 Windows Server、HYPER-V 和 Docker 容器比較記錄檔。

下圖顯示各種容器主機和代理程式與 OMS 之間的關聯性。

![容器圖表](./media/log-analytics-containers/containers-diagram.png)

## <a name="system-requirements"></a>系統需求
開始之前請檢閱下列詳細資料，以確認符合必要條件。

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>容器監視解決方案支援 Docker Orchestrator 和作業系統平台 
下表概述對於使用 Log Analytics 之容器清查、效能和記錄的 Docker 協調流程和作業系統監視支援。   

| | ACS | Linux | Windows | 容器<br>清查 | 映像<br>清查 | 節點<br>清查 | 容器<br>效能 | 容器<br>Event | Event<br>記錄檔 | 容器<br>記錄檔 | 
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Kubernetes | 是 | 是 | | 是 | 是 | 是 | 是 | 是 | 是 | 是 | 
| Mesosphere<br>DC/OS | 是 | 是 | | 是 | 是 | 是 | 是| 是 | 是 | 是 | 
| Docker<br>Swarm | 是 | 是 | 是 | 是 | 是 | 是 | 是 | 是 | | 是 |
| 服務<br>網狀架構 | | | 是 | 是 | 是 | 是 | 是 | 是 | 是 | 是 | 
| Red Hat 開啟<br>移位 | | 是 | | 是 | 是| 是 | 是 | 是 | | 是 | 
| Windows Server<br>(獨立) | | | 是 | 是 | 是 | 是 | 是 | 是 | | 是 |
| Linux 伺服器<br>(獨立) | | 是 | | 是 | 是 | 是 | 是 | 是 | | 是 |


### <a name="supported-linux-operating-system"></a>支援的 Linux 作業系統

- Docker 1.11 至 1.13
- Docker CE 和 EE v17.03

下列 x64 Linux 散發套件可支援做為容器主機︰

- Ubuntu 14.04 LTS、16.04 LTS
- CoreOS (穩定版)
- Amazon Linux 2016.09.0
- openSUSE 13.2
- openSUSE LEAP 42.2
- CentOS 7.2、7.3
- SLES 12
- RHEL 7.2、7.3

### <a name="supported-windows-operating-system"></a>支援的 Windows 作業系統

- Windows Server 2016
- Windows 10 年度版 (Professional 或 Enterprise)

### <a name="docker-versions-supported-on-windows"></a>在 Windows 上支援 Docker 版本

- Docker 1.12 – 1.13
- Docker 17.03.0 

## <a name="installing-and-configuring-the-solution"></a>安裝和設定方案
請使用下列資訊來安裝和設定方案。

從 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) 或使用[從方案庫新增 Log Analytics 方案](log-analytics-add-solutions.md)中所述的程序，將「容器」解決方案新增至您的 OMS 工作區。

安裝和搭配 OMS 使用 Docker 的方式有幾種：

* 在支援的 Linux 作業系統上，安裝和執行 Docker，然後安裝並設定 OMS Agent for Linux。
* 在 CoreOS 上，您無法執行 OMS Agent for Linux。 相反地，您可以執行 OMS Agent for Linux 的容器化版本。
* 在 Windows Server 2016 和 Windows 10 上，安裝 Docker 引擎及用戶端，然後連接代理程式以收集資訊，並將它傳送至 Log Analytics。


您可以在 [GitHub](https://github.com/Microsoft/OMS-docker) 上檢閱容器主機支援的 Docker 和 Linux 作業系統版本。

### <a name="container-services"></a>容器服務

- 如果您有使用 Azure Container Service 的 Kubernetes 叢集，請深入了解[使用 Microsoft Operations Management Suite (OMS) 監視 Azure Container Service 叢集](../container-service/kubernetes/container-service-kubernetes-oms.md)。
- 如果您擁有 Azure Container Service DC/OS 叢集，請深入了解[使用 Operations Management Suite 監視 Azure Container Service DC/OS 叢集](../container-service/dcos-swarm/container-service-monitoring-oms.md)。
- 如果您有 Docker Swarm 模式環境，詳細資訊請參閱[為 Docker Swarm 設定 OMS 代理程式](#configure-an-oms-agent-for-docker-swarm)。
- 如果您搭配 Service Fabric 使用容器，請參閱 [Azure Service Fabric 概觀](../service-fabric/service-fabric-overview.md)以深入了解。
- 檢閱 [Windows 上的 Docker 引擎](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon)文章，以取得有關如何在執行 Windows 的電腦上安裝和設定您 Docker 引擎的資訊。

> [!IMPORTANT]
> 在容器主機上安裝 [OMS Agent for Linux](log-analytics-agent-linux.md)**之前**，Docker 必須已在執行中。 如果您已在安裝 Docker 前安裝此代理程式，您必須重新安裝 OMS Agent for Linux。 如需 Docker 的詳細資訊，請參閱 [Docker 網站](https://www.docker.com)。
>
>

您必須先在容器主機上進行下列設定，才可以監視容器。

## <a name="linux-container-hosts"></a>Linux 容器主機

在您安裝 Docker 之後，使用容器主機的下列設定來設定可搭配 Docker 使用的代理程式。 您需要有 [OMS 工作區識別碼和金鑰](log-analytics-agent-linux.md)。


### <a name="for-all-linux-container-hosts-except-coreos"></a>適用於 CoreOS 以外的所有 Linux 容器主機

- 如需如何安裝 OMS Agent for Linux 的詳細資訊和步驟，請參閱[將 Linux 電腦連線至 Operations Management Suite (OMS)](log-analytics-agent-linux.md)。

### <a name="for-all-linux-container-hosts-including-coreos"></a>適用於包含 CoreOS 的所有 Linux 容器主機

啟動您要監視的 OMS 容器。 修改並使用下列範例。

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

### <a name="for-all-azure-government-linux-container-hosts-including-coreos"></a>適用於包含 CoreOS 的所有 Azure Government Linux 容器主機

啟動您要監視的 OMS 容器。 修改並使用下列範例。

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```


### <a name="switching-from-using-an-installed-linux-agent-to-one-in-a-container"></a>從使用已安裝的代理程式切換為使用容器中的 Linux 代理程式
如果您先前使用了直接安裝的代理程式，而且想要改為使用在容器中執行的代理程式，您必須先移除 OMS Agent for Linux。 請參閱[解除安裝 OMS Agent for Linux](log-analytics-agent-linux.md#uninstalling-the-oms-agent-for-linux)。

### <a name="configure-an-oms-agent-for-docker-swarm"></a>為 Docker Swarm 設定 OMS 代理程式

您可以在 Docker Swarm 上以全域服務的方式執行 OMS 代理程式。 使用下列資訊建立 OMS 代理程式服務。 您需要插入工作區識別碼與主索引鍵。

- 在主要節點上執行下列命令。

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock  -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

### <a name="secure-your-secret-information-for-container-services"></a>保護容器服務的祕密資訊

您可以針對 Docker Swarm 和 Kubernetes 保護祕密 OMS 工作區識別碼與主索引鍵。

#### <a name="secure-secrets-for-docker-swarm"></a>保護 Docker Swarm 的祕密

針對 Docker Swarm，建立工作區識別碼與主索引鍵的祕密後，您便可為 OMSagent 建立 Docker 服務。 使用下列資訊建立您的祕密資訊。

1. 在主要節點上執行下列命令。

    ```
    echo "WSID" | docker secret create WSID -
    echo "KEY" | docker secret create KEY -
    ```

2. 確認已正確建立祕密。

    ```
    keiko@swarmm-master-13957614-0:/run# sudo docker secret ls
    ```

    ```
    ID                          NAME                CREATED             UPDATED
    j2fj153zxy91j8zbcitnjxjiv   WSID                43 minutes ago      43 minutes ago
    l9rh3n987g9c45zffuxdxetd9   KEY                 38 minutes ago      38 minutes ago
    ```

3. 執行下列命令，將祕密掛接至容器化的 OMS 代理程式。

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="secure-secrets-for-kubernetes-with-yaml-files"></a>使用 yaml 檔案保護 Kubernetes 的祕密

針對 Kubernetes，您可以使用指令碼為工作區識別碼與主索引鍵產生祕密 .yaml 檔案。 [OMS Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) 頁面含有您可以在有或無祕密資訊的情況下使用的檔案。

- 沒有祕密資訊的預設 OMS 代理程式 DaemonSet (omsagent.yaml)
- 使用祕密資訊的 OMS 代理程式 DaemonSet yaml 檔案 (omsagent-ds-secrets.yaml) 具有產生祕密 yaml (omsagentsecret.yaml) 檔案的祕密產生指令碼。

您可以選擇在有或無祕密的情況下建立 omsagent DaemonSet。

##### <a name="default-omsagent-daemonset-yaml-file-without-secrets"></a>沒有祕密的預設 OMSagent DaemonSet yaml 檔案

- 針對預設 OMS 代理程式 DaemonSet yaml 檔案，用您的 WSID 和索引鍵取代 `<WSID>` 和 `<KEY>`。 將檔案複製到您的主要節點，並執行下列命令：

    ```
    sudo kubectl create -f omsagent.yaml
    ```

##### <a name="default-omsagent-daemonset-yaml-file-with-secrets"></a>有祕密的預設 OMSagent DaemonSet yaml 檔案

1. 若要使用具有祕密資訊的 OMS 代理程式 DaemonSet，請先建立祕密。
    1. 複製指令碼和祕密範本檔案，並確定它們位於相同的目錄。
        - 祕密產生指令碼 - secret-gen.sh
        - 祕密範本 - secret-template.yaml
    2. 執行指令碼，如下列範例所示。 指令碼會要求 OMS 工作區識別碼與主索引鍵，在您輸入這兩個資訊之後，指令碼會建立一個祕密 .yaml 檔案讓您能夠執行它。   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. 執行以下命令來建立祕密 Pod：
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. 若要確認，請執行下列命令：

        ```
        keiko@ubuntu16-13db:~# sudo kubectl get secrets
        ```

        輸出會像下面這樣：

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        ```

        ```
        keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
        ```

        輸出會像下面這樣：

        ```
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. 執行 ``` sudo kubectl create -f omsagent-ds-secrets.yaml ``` 以建立您的 omsagent daemon-set

2. 確認 OMS 代理程式 DaemonSet 正在執行，如下所示：

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```


針對 Kubernetes，使用指令碼為工作區識別碼與主索引鍵產生祕密 yaml 檔案。 搭配 [omsagent yaml 檔案](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml)使用下列範例資訊來保護您的祕密資訊。

```
keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
Name:           omsagent-secret
Namespace:      default
Labels:         <none>
Annotations:    <none>

Type:   Opaque

Data
====
WSID:   36 bytes
KEY:    88 bytes
```


## <a name="windows-container-hosts"></a>Windows 容器主機

### <a name="preparation-before-installing-windows-agents"></a>安裝 Windows 代理程式之前的準備動作

在執行 Windows 的電腦上安裝代理程式之前，您需要設定 Docker 服務。 組態可讓 Windows 代理程式或 Log Analytics 虛擬機器擴充功能使用 Docker TCP 通訊端，讓代理程式可以從遠端存取的 Docker 精靈並擷取監視資料。

#### <a name="to-start-docker-and-verify-its-configuration"></a>若要啟動 Docker 並確認其組態

若要設定 Windows Server 的 TCP 具名管道，需要執行一些步驟︰

1. 在 Windows PowerShell 中啟用 TCP 管道和具名的管道。

    ```
    Stop-Service docker
    dockerd --unregister-service
    dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
    Start-Service docker
    ```

2. 使用 TCP 管道和具名管道的組態檔來設定 Docker。 此組態檔位於 C:\ProgramData\docker\config\daemon.json。

    在 daemon.json 檔案中，您將需要下列項目：

    ```
    {
    "hosts": ["tcp://0.0.0.0:2375", "npipe://"]
    }
    ```

如需 Windows 容器使用之 Docker 精靈組態的詳細資訊，請參閱 [Windows 上的 Docker 引擎](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon)。


### <a name="install-windows-agents"></a>安裝 Windows 代理程式

若要啟用 Windows 和 Hyper-V 容器監視，請在容器主機的 Windows 電腦上安裝代理程式。 若需要在內部部署環境中執行 Windows 的電腦，請參閱[連接 Windows 電腦至 Log Analytics](log-analytics-windows-agents.md)。 若需要在 Azure 中執行的虛擬機器，可使用[虛擬機器擴充功能](log-analytics-azure-vm-extension.md)將它們連接至 Log Analytics。

您可以監視 Service Fabric 上執行的 Windows 容器。 不過，Service Fabric 目前只支援 [Azure 中執行的虛擬機器](log-analytics-azure-vm-extension.md)和[在內部部署環境中執行 Windows 的電腦](log-analytics-windows-agents.md)。

若要確認已正確設定容器解決方案︰

- 檢查管理組件是否正確下載，請找出 ContainerManagement.xxx。
    - 這些檔案應在 C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs 資料夾。
- 確認 OMS 工作區識別碼是否正確，可前往 [控制台] > [系統及安全性]。
    - 開啟 [Microsoft 監視代理程式] 並確認工作區資訊是否正確。


## <a name="containers-data-collection-details"></a>容器資料收集詳細資料
容器解決方案會從使用您已啟用之代理程式的容器主機和容器收集各種效能計量和記錄檔資料。

下表顯示容器的資料收集方法及如何收集資料的其他詳細資料。

| 平台 | [OMS Agent for Linux](log-analytics-linux-agents.md) | SCOM 代理程式 | Azure 儲存體 | SCOM 是否為必要項目？ | 透過管理群組傳送的 SCOM 代理程式資料 | 收集頻率 |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |![是](./media/log-analytics-containers/oms-bullet-green.png) |![否](./media/log-analytics-containers/oms-bullet-red.png) |![否](./media/log-analytics-containers/oms-bullet-red.png) |![否](./media/log-analytics-containers/oms-bullet-red.png) |![否](./media/log-analytics-containers/oms-bullet-red.png) |每隔 3 分鐘 |

| 平台 | [Windows 代理程式](log-analytics-windows-agents.md) | SCOM 代理程式 | Azure 儲存體 | SCOM 是否為必要項目？ | 透過管理群組傳送的 SCOM 代理程式資料 | 收集頻率 |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |![是](./media/log-analytics-containers/oms-bullet-green.png) |![否](./media/log-analytics-containers/oms-bullet-red.png) |![否](./media/log-analytics-containers/oms-bullet-red.png) |![否](./media/log-analytics-containers/oms-bullet-red.png) |![否](./media/log-analytics-containers/oms-bullet-red.png) |每隔 3 分鐘 |

| 平台 | [Log Analytics VM 延伸模組](log-analytics-azure-vm-extension.md) | SCOM 代理程式 | Azure 儲存體 | SCOM 是否為必要項目？ | 透過管理群組傳送的 SCOM 代理程式資料 | 收集頻率 |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |![是](./media/log-analytics-containers/oms-bullet-green.png) |![否](./media/log-analytics-containers/oms-bullet-red.png) |![否](./media/log-analytics-containers/oms-bullet-red.png) |![否](./media/log-analytics-containers/oms-bullet-red.png) |![否](./media/log-analytics-containers/oms-bullet-red.png) |每隔 3 分鐘 |

下表顯示容器解決方案所收集之資料類型以及記錄檔搜尋和結果中所使用之資料類型的範例。

| 資料類型 | 記錄檔搜尋中的資料類型 | 欄位 |
| --- | --- | --- |
| 主機和容器的效能 | `Type=Perf` | Computer、ObjectName、CounterName &#40;%Processor Time、Disk Reads MB、Disk Writes MB、Memory Usage MB、Network Receive Bytes、Network Send Bytes、Processor Usage sec、Network&#41;、CounterValue、TimeGenerated、CounterPath、SourceSystem |
| 容器清查 | `Type=ContainerInventory` | TimeGenerated、Computer、container name、ContainerHostname、Image、ImageTag、ContinerState、ExitCode、EnvironmentVar、Command、CreatedTime、StartedTime、FinishedTime、SourceSystem、ContainerID、ImageID |
| 容器映像清查 | `Type=ContainerImageInventory` | TimeGenerated、Computer、Image、ImageTag、ImageSize、VirtualSize、Running、Paused、Stopped、Failed、SourceSystem、ImageID、TotalContainer |
| 容器記錄檔 | `Type=ContainerLog` | TimeGenerated、Computer、image ID、container name、LogEntrySource、LogEntry、SourceSystem、ContainerID |
| 容器服務記錄檔 | `Type=ContainerServiceLog`  | TimeGenerated、Computer、TimeOfCommand、Image、Command、SourceSystem、ContainerID |

## <a name="monitor-containers"></a>監視容器
在 OMS 入口網站中啟用此方案之後，您會看到 [容器] 圖格顯示容器主機和主機中執行之容器的相關摘要資訊。

![容器圖格](./media/log-analytics-containers/containers-title.png)

此圖格顯示環境中有多少容器以及其為失敗、執行中或已停止的概觀。

### <a name="using-the-containers-dashboard"></a>使用容器儀表板
按一下 [容器] 圖格。 在這裡，您會看到依下列各項組織的檢視︰

* 容器事件
* 錯誤數
* 容器狀態
* 容器映像清查
* CPU 和記憶體效能

儀表板中的每個窗格都是對所收集資料執行之搜尋的視覺表示方式。

![容器儀表板](./media/log-analytics-containers/containers-dash01.png)

![容器儀表板](./media/log-analytics-containers/containers-dash02.png)

在 [容器狀態] 刀鋒視窗中，按一下如下所示的頂端區域。

![容器狀態](./media/log-analytics-containers/containers-status.png)

[記錄檔搜尋] 隨即開啟，其中顯示主機和在其中執行之容器的相關資訊。

![容器的記錄檔搜尋](./media/log-analytics-containers/containers-log-search.png)

在這裡，您可以編輯搜尋查詢來進行修改，以尋找您感興趣的特定資訊。 如需記錄檔搜尋的詳細資訊，請參閱 [Log Analytics 中的記錄檔搜尋](log-analytics-log-searches.md)。

例如，您可以在搜尋查詢中將 [執行中] 變更為 [已停止]藉此修改搜尋查詢，以便顯示所有已停止的容器，而不是執行中的容器。

## <a name="troubleshoot-by-finding-a-failed-container"></a>尋找失敗的容器以進行疑難排解
如果容器已透過非零結束代碼結束，則 OMS 會將容器標示為 [失敗]。 您可以在 [失敗的容器] 刀鋒視窗中，大致了解環境中的錯誤和失敗。

### <a name="to-find-failed-containers"></a>尋找失敗的容器
1. 按一下 [容器事件] 刀鋒視窗。  
   ![容器事件](./media/log-analytics-containers/containers-events.png)
2. [記錄檔搜尋] 隨即開啟，其中顯示如下所示的容器狀態。  
   ![容器狀態](./media/log-analytics-containers/containers-container-state.png)
3. 接下來，按一下失敗的值以檢視其他資訊，例如映像大小及已停止和失敗映像的數目。 展開 [顯示更多] 以檢視映像識別碼。  
   ![失敗的容器](./media/log-analytics-containers/containers-state-failed.png)
4. 接下來，尋找正在執行此映像的容器。 在搜尋查詢中輸入下列內容︰
   `Type=ContainerInventory <ImageID>`這會顯示記錄檔。 您可以捲動以查看失敗的容器。  
   ![失敗的容器](./media/log-analytics-containers/containers-failed04.png)

## <a name="search-logs-for-container-data"></a>搜尋容器資料的記錄檔
當您針對特定錯誤進行疑難排解時，這助於查看您的環境中發生此錯誤的位置。 下列記錄檔類型將協助您建立查詢，以傳回您想要的資訊。

* **ContainerInventory** – 當您需要有關容器位置、其名稱，以及所執行映像的資訊時，請使用這個類型。
* **ContainerImageInventory** – 當您嘗試尋找依照映像組織的資訊，以及檢視映像資訊 (例如映像識別碼或大小) 時，請使用這個類型。
* **ContainerLog** – 當您想要尋找特定錯誤記錄檔資訊和項目時，請使用這個類型。
* **ContainerServiceLog** – 當您嘗試尋找 Docker 精靈的稽核追蹤資訊 (例如啟動、停止、刪除或提取命令) 時，請使用這個類型。

### <a name="to-search-logs-for-container-data"></a>搜尋容器資料的記錄檔
* 選擇您知道最近失敗的映像並尋找其錯誤記錄檔。 首先，透過 **ContainerInventory** 搜尋來尋找正在執行該映像的容器名稱。 例如，搜尋 `Type=ContainerInventory ubuntu Failed`  
    ![搜尋 Ubuntu 容器](./media/log-analytics-containers/search-ubuntu.png)

  請記下 [名稱] 旁邊的容器名稱，並搜尋其記錄檔。 在此範例中為 `Type=ContainerLog adoring_meitner`。

**檢視效能資訊**

當您開始建構查詢時，這有助於先查看各種可能。 例如，若要查看所有效能資料，請輸入下列搜尋查詢，嘗試進行廣泛查詢。

```
Type=Perf
```

![容器效能](./media/log-analytics-containers/containers-perf01.png)

當您按一下結果中的 [計量] 時，您可以用更圖形化的形式查看此資訊。

![容器效能](./media/log-analytics-containers/containers-perf02.png)

您可以輸入查詢右邊的名稱，將您所見的效能資料範圍限制於特定容器。

```
Type=Perf <containerName>
```

其顯示針對個別容器所收集的效能計量清單。

![容器效能](./media/log-analytics-containers/containers-perf03.png)

## <a name="example-log-search-queries"></a>範例記錄檔搜尋查詢
從一或兩個範例開始建置查詢，然後加以修改以符合您的環境，通常很實用。 首先，您可以體驗 [值得注意的查詢] 刀鋒視窗，協助您建置更進階的查詢。

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![容器查詢](./media/log-analytics-containers/containers-queries.png)


## <a name="saving-log-search-queries"></a>儲存記錄檔搜尋查詢
儲存查詢是 Log Analytics 的標準功能。 藉由儲存查詢，您可將您覺得有用的查詢放在容易取得的地方，以便日後使用。

建立您覺得有用的查詢之後，按一下 [記錄檔搜尋] 頁面頂端的 [我的最愛] 來儲存它。 然後您稍後即可輕易地從 [我的儀表板] 頁面進行存取。

## <a name="next-steps"></a>後續步驟
* [搜尋記錄檔](log-analytics-log-searches.md)以檢視詳細的容器資料記錄。

