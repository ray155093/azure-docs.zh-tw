---
title: "Log Analytics 中的容器方案 | Microsoft Docs"
description: "Log Analytics 中的容器方案可協助您在單一位置檢視及管理 Docker 容器主機。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: e1e4b52b-92d5-4bfa-8a09-ff8c6b5a9f78
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 6cdc0730d7632e41b393c4abb17badc255e21a8d
ms.openlocfilehash: 0bc5366417f08c63f5fd5588c94381faf6a2397d


---
# <a name="containers-preview-solution-log-analytics"></a>Log Analytics 中的容器 (預覽) 方案
本文說明如何設定及使用 Log Analytics 中的容器方案，協助您在單一位置檢視及管理 Docker 容器主機。 Docker 是用來建立容器的軟體虛擬化系統，自動將軟體部署至其 IT 基礎結構。

利用此解決方案，您可以查看容器主機上有哪些正在執行的容器，以及容器中有哪些正在執行的映像。 您可以檢視詳細的稽核資訊，其中顯示搭配容器使用的命令。 而且，藉由檢視及搜尋集中式記錄檔，而不需從遠端檢視 Docker 主機，即可針對容器進行疑難排解。 您可能會找到有雜訊且耗用過多主機資源的容器。 而且，您可以檢視容器的集中式 CPU、記憶體、儲存體以及網路使用量和效能資訊。

## <a name="installing-and-configuring-the-solution"></a>安裝和設定方案
請使用下列資訊來安裝和設定方案。

使用[從方案庫新增 Log Analytics 方案](log-analytics-add-solutions.md)中所述的程序，將容器方案新增至 OMS 工作區。

安裝和搭配 OMS 使用 Docker 的方式有兩種：

* 在支援的 Linux 作業系統上，安裝和執行 Docker，然後安裝並設定 OMS Agent for Linux
* 在 CoreOS 上，您無法執行 OMS Agent for Linux。 相反地，您可以執行 OMS Agent for Linux 的容器化版本。

在 [GitHub](https://github.com/Microsoft/OMS-docker) 上檢閱容器主機支援的 Docker 和 Linux 作業系統版本。

> [!IMPORTANT]
> 在容器主機上安裝 [OMS Agent for Linux](log-analytics-linux-agents.md)**之前**，Docker 必須已在執行中。 如果您已在安裝 Docker 前安裝此代理程式，您必須重新安裝 OMS Agent for Linux。 如需 Docker 的詳細資訊，請參閱 [Docker 網站](https://www.docker.com)。
>
>

您必須先在容器主機上進行下列設定，才可以監視容器。

## <a name="configure-settings-for-the-linux-container-host"></a>進行 Linux 容器主機設定

下列 x64 Linux 散發套件可支援做為容器主機︰

- Ubuntu 14.04 LTS、16.04 LTS
- CoreOS (穩定版)
- Amazon Linux 2016.03
- openSUSE 13.2
- CentOS 7
- SLES 12
- RHEL 7.2

在您安裝 Docker 之後，使用容器主機的下列設定來設定可搭配 Docker 使用的代理程式。 您需要有 [OMS 工作區識別碼和金鑰](log-analytics-linux-agents.md)。

### <a name="for-all-container-hosts-except-coreos"></a>適用於 CoreOS 以外的所有容器主機

- 請遵循 [OMS Agent for Linux 安裝步驟](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md)中的指示。

### <a name="for-all-container-hosts-including-coreos"></a>適用於包括 CoreOS 在內的所有容器主機

啟動您要監視的 OMS 容器。 修改並使用下列範例。

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

### <a name="switching-from-using-an-installed-agent-to-one-in-a-container"></a>從使用已安裝的代理程式切換為使用容器中的代理程式
如果您先前使用了直接安裝的代理程式，而且想要改為使用在容器中執行的代理程式，您必須先移除 OMSAgent。 請參閱[安裝 OMS Agent for Linux 的步驟](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md)。

## <a name="containers-data-collection-details"></a>容器資料收集詳細資料
容器方案會從使用您已啟用之 OMS Agents for Linux 的容器主機和容器，以及從在容器中執行的 OMSAgent，收集各種效能計量和記錄檔資料。

下表顯示容器的資料收集方法及如何收集資料的其他詳細資料。

| 平台 | OMS Agent for Linux | SCOM 代理程式 | Azure 儲存體 | SCOM 是否為必要項目？ | 透過管理群組傳送的 SCOM 代理程式資料 | 收集頻率 |
| --- | --- | --- | --- | --- | --- | --- |
|  Linux |![是](./media/log-analytics-containers/oms-bullet-green.png) |![否](./media/log-analytics-containers/oms-bullet-red.png) |![否](./media/log-analytics-containers/oms-bullet-red.png) |![否](./media/log-analytics-containers/oms-bullet-red.png) |![否](./media/log-analytics-containers/oms-bullet-red.png) |每隔 3 分鐘 |

下表顯示容器解決方案所收集之資料類型以及記錄檔搜尋和結果中所使用之資料類型的範例︰

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

![容器查詢](./media/log-analytics-containers/containers-queries.png)

## <a name="saving-log-search-queries"></a>儲存記錄檔搜尋查詢
儲存查詢是 Log Analytics 的標準功能。 藉由儲存查詢，您可將您覺得有用的查詢放在容易取得的地方，以便日後使用。

建立您覺得有用的查詢之後，按一下 [記錄檔搜尋] 頁面頂端的 [我的最愛] 來儲存它。 然後您稍後即可輕易地從 [我的儀表板] 頁面進行存取。

## <a name="next-steps"></a>後續步驟
* [搜尋記錄檔](log-analytics-log-searches.md)以檢視詳細的容器資料記錄。



<!--HONumber=Nov16_HO5-->


