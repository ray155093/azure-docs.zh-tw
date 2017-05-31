---
title: "Azure Service Fabric 修補程式協調流程應用程式 | Microsoft Docs"
description: "在 Service Fabric 叢集上將 OS 修補自動化的應用程式。"
services: service-fabric
documentationcenter: .net
author: novino
manager: timlt
editor: 
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/9/2017
ms.author: nachandr
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: a3554881e7db894c602e73feb385b5b361837409
ms.contentlocale: zh-tw
ms.lasthandoff: 05/18/2017


---

# <a name="application-to-patch-windows-os-in-your-service-fabric-cluster"></a>在 Service Fabric 叢集中修補 Windows OS 的應用程式

修補程式協調流程應用程式是一項 Service Fabric 應用程式，可讓您在 Azure 上或內部部署的 Service Fabric 叢集上將 OS 修補自動化，而不需要停機。

修補程式協調流程應用程式包含下列主要功能︰

1. **自動安裝 OS 更新**︰修補程式協調流程應用程式可確保自動下載並安裝更新，並於適用時將節點重新開機。
    這會在所有叢集節點上完成，而不需要停機。

1. **叢集感知修補和健康情況整合**︰修補程式協調流程應用程式在套用更新時，會隨著叢集進行一次更新一個節點或一次升級一個網域，從而監視叢集的健康情況。 
    如果它在任何時間偵測到因修補程序而造成叢集的健康情況下降，就會將程序停止，以防止問題繼續惡化。

1. **支援所有的 Service Fabric 叢集**︰應用程式的泛型程度足以在 Azure Service Fabric 叢集和獨立叢集中運作。
    > [!NOTE]
    > 即將推出獨立叢集的支援。

## <a name="link-to-download-the-application-package"></a>下載應用程式套件的連結

從[下載連結](https://go.microsoft.com/fwlink/P/?linkid=849590)下載應用程式

## <a name="internal-details-of-the-application"></a>應用程式的內部詳細資料

修補程式協調流程應用程式包含下列子元件︰

- **協調員服務**︰是具狀態服務。 該服務將負責
    - 協調整個叢集上的 Windows Update 作業。
    - 儲存已完成之 Windows Update 作業的結果。
- **節點代理程式服務**︰是無狀態服務，它會在所有 Service Fabric 叢集節點上執行。 該服務將負責
    - 節點代理程式 NTService 的啟動程序。
    - 監視節點代理程式 NTService。
- **節點代理程式 NTService**︰是一項 Windows NT 服務。 節點代理程式 NTService 會在更高的權限 (系統) 上執行。 相反地，節點代理程式服務和協調員服務是在較低等級權限 (網路服務) 中執行。 服務會負責執行下列所有叢集節點上的 Windows Update 作業。
    - 將節點上的自動 Windows Update 停用。
    - 已提供根據原則使用者之 Windows Updates 安裝的下載。
    - Windows Update 安裝後請重新啟動電腦
    - 將 Windows Update 的結果上傳至協調員服務。
    - 萬一耗盡所有重試之後作業還是失敗，就報告健康情況報告。

> [!NOTE]
> 修補程式協調流程應用程式是使用 Service Fabric 系統服務 **Repair Manager**，將節點和執行健康情況檢查停用/啟用。 修補程式協調流程應用程式所建立的修復工作會追蹤每個節點的 Windows Update 進度。

## <a name="prerequisites-for-using-the-application"></a>使用應用程式的必要條件

### <a name="ensure-service-fabric-version-is-55-or-above"></a>請確定 Service Fabric 為 5.5 版或更新版本

在具有 Service Fabric 執行階段 5.5 版或更新版本的叢集上，可執行補充程式協調流程應用程式。

### <a name="enable-repair-manager-service-if-not-running-already"></a>啟用 Repair Manager 服務 (如果尚未執行中)

修補程式協調流程應用程式需要在叢集上啟用 Repair Manager 系統服務。

#### <a name="service-fabric-clusters-on-azure"></a>Azure 上的 Service Fabric 叢集

銀級耐久性層中的 Azure 叢集依預設會啟用 Repair Manager。 金級耐久性層中的 Azure 叢集可能會或可能不會啟用 Repair Manager，取決於這些叢集在多久前所建立。 Azure 叢集在銅級耐久性層時，依預設不會啟用 Repair Manager 服務。 

如果您看到 Repair Manager 服務尚未在 Service Fabric Explorer 的系統服務區段下執行，可以使用 [Azure Resource Manager 範本](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)，在 Repair Manager 服務新的 / 現有 Service Fabric 叢集上啟用 Repair Manager 服務。

首先，要取得您想要部署之叢集的範本。 您可以使用範例範本或建立自訂的 Resource Manager 範本。 然後，您可以使用下列步驟來啟用 Repair Manager︰

1. 請先檢查 `Microsoft.ServiceFabric/clusters` 資源的 `apiversion` 是否設定為 `2017-07-01-preview`，如下列程式碼片段所示。 如果不同，您就需要將 `apiVersion` 更新為 `2017-07-01-preview` 的值

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. 立即啟用 Repair Manager 服務，方法是在 `fabricSettings` 區段之後新增下列 `addonFeaturres` 區段，如下所示

    ```json
    "fabricSettings": [
        ...      
        ],
        "addonFeatures": [
            "RepairManager"
        ],
    ```

3. 一旦您使用這些變更將叢集範本進行更新之後，將它們加以套用，使升級完成。 完成後，您現在可以在 Service Fabric Explorer 的系統服務區段下，看到 Repair Manager 系統服務 (亦稱為 `fabric:/System/RepairManagerService`) 在叢集中執行。 

#### <a name="standalone-on-premise-clusters"></a>獨立內部部署叢集

> [!NOTE]
> 即將推出獨立叢集的支援

### <a name="disable-automatic-windows-update-on-all-nodes"></a>將所有節點上的自動 Windows Update 停用。

在 Service Fabric 叢集上啟用自動 Windows Update 可能會導致可用性遺失，因為在相同時間可能會重新啟動多個節點來完成更新。

依預設，修補程式協調流程應用程式會在每個叢集節點上嘗試將自動 Windows Update 停用。

不過，如果設定是由系統管理員 / 群組原則所管理，建議將 Windows Update 原則明確地設定為「下載前請通知」。


### <a name="optional-enable-windows-azure-diagnostics"></a>選擇性：啟用 Windows Azure 診斷

在接下來幾天，會收集修補程式協調流程應用程式的記錄，作為 Service Fabric 記錄本身的一部分。 不過在那之前，會在每個叢集節點上本機收集這些記錄。 若要從所有節點將記錄上傳到中央位置，我們建議您設定 Windows Azure 診斷 (WAD)。

啟用 WAD 的步驟詳細資料在[這裡。](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-how-to-setup-wad)

下列固定的提供者識別碼會產生修補程式協調流程應用程式的記錄。

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

在 Azure Resource Manager 範本的 "WadCfg" 區段內，新增下列區段︰ 

```json
"PatchOrchestrationApplication": \[
  {
    "provider": "e39b723c-590c-4090-abb0-11e3e6616346",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
      "eventDestination": "PatchOrchestrationApplicationTable"
    }
  },
  {
    "provider": "fc0028ff-bfdc-499f-80dc-ed922c52c5e9",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
    "eventDestination": " PatchOrchestrationApplicationTable"
    }
  },
  {
    "provider": "24afa313-0d3b-4c7c-b485-1047fd964b60",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
    "eventDestination": " PatchOrchestrationApplicationTable"
    }
  },
  {
    "provider": "05dc046c-60e9-4ef7-965e-91660adffa68",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
    "eventDestination": " PatchOrchestrationApplicationTable"
    }
  },
\]
```

> [!NOTE]
> 如果 Service Fabric 叢集是由多個節點類型所組成，就必須在所有 “WadCfg” 區段新增上述區段。

## <a name="configuring-the-application"></a>設定應用程式

使用者可以設定以下的設定，根據其需求來調整修補程式協調流程應用程式的行為。

使用者可以將預設值加以覆寫，方法是在應用程式建立/更新期間，將應用程式參數傳入。 可提供應用程式參數，方法是將 `ApplicationParameter` 指定為 Cmdlet `Start-ServiceFabricApplicationUpgrade` 或 `New-ServiceFabricApplication`

|**參數**        |**類型**                          | **詳細資料**|
|:-|-|-|
|MaxResultsToCache    |long                              | Windows Update 結果歷程記錄的最大數目，應加以快取。 <br>預設值為 3000 假設 <br> - 節點的數目為 20 <br> - 每個月在節點上發生的更新數目為 5 <br> - 每個作業的結果數目可為 10 <br> - 且過去 3 個月的結果應該加以儲存 |
|TaskApprovalPolicy   |例舉 <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy 會指出 CoordinatorService 要在 Service Fabric 叢集節點中用來安裝 Windows 更新的原則<br>                         允許的值包括： <br><br>                                                           <b>NodeWise</b> - Windows Update 一次會安裝一個節點 <br>                                                           <b>UpgradeDomainWise</b> - Windows Update 一次會安裝一個升級網域 (最多屬於升級網域的所有節點可適用於 Windows Update)
|LogsDiskQuotaInMB   |long  <br> (預設值︰1024)               |修補程式協調流程應用程式記錄的大小上限 (以 MB 為單位)，會在節點上本機保留
| WUQuery               | 字串<br>(預設值："IsInstalled=0")                | 查詢以取得 Windows 更新，如需詳細資訊，請參閱 [WuQuery。](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
| InstallWindowsOSOnlyUpdates | Bool <br> (預設值︰True)                 | 這個旗標可讓您安裝 Windows OS 更新。            |
| WUOperationTimeOutInMinutes | int <br>(預設值︰90)                   | 指定任何 Windows Update 作業的逾時 (搜尋/ 下載/ 安裝)。 如果作業未在指定的逾時內完成，它就會中止。       |
| WURescheduleCount     | int <br> (預設值︰5)                  | 此設定會決定如果作業持續失敗，服務會將 Windows Update 重新排程的次數上限          |
| WURescheduleTimeInMinutes | int <br>(預設值︰30) | 此設定會決定如果持續失敗，服務會將 Windows Update 重新排程的間隔 |
| WUFrequency           | 以逗號分隔的字串 (預設值︰"Weekly, Wednesday, 7:00:00")     | 安裝 Windows Update 的頻率。 格式與可能的值如下所示 <br>-   Monthly,DD,HH:MM:SS Ex: Monthly,5,12:22:32 <br> -   Weekly,DAY,HH:MM:SS Ex: Weekly, Tuesday, 12:22:32  <br> -   Daily, HH:MM:SS Ex: Daily, 12:22:32  <br> -  None - 表示不應該進行 Windows Update  <br><br> 注意︰所有時間都是 UTC 格式|
| AcceptWindowsUpdateEula | Bool <br>(預設值︰true) | 藉由設定這個旗標，應用程式會代表電腦的擁有者接受 Windows Update 的 EULA。              |


## <a name="steps-to-deploy-the-application"></a>部署應用程式的步驟

1. 完成準備叢集的所有必要條件步驟。
1. 部署應用程式 - 可使用[這裡](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)所述的步驟從 PowerShell 來完成。
1. 若要在部署時設定應用程式，可將 `ApplicationParamater` 用於 Cmdlet `New-ServiceFabricApplication`

    為了方便使用者，我們提供了 Deploy.ps1 指令碼以及我們的應用程式。 使用方式。

    - 使用 `Connect-ServiceFabricCluster` 連線至 Service Fabric 叢集
    - 利用適當的 `ApplicationParameter` 值執行 Powershell 指令碼 Deploy.ps1

> [!NOTE]
> 將指令碼和應用程式資料夾 PatchOrchestrationApplication 保存在同一個目錄中。

## <a name="steps-to-upgrade-the-application"></a>升級應用程式的步驟

若要使用 PowerShell 將現有的修補程式協調流程應用程式進行升級，請參閱[這裡](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell)所述的應用程式升級步驟。

若只要將應用程式的應用程式參數進行變更，請使用現有的應用程式版本將 `ApplicationParamater` 提供給 Cmdlet `Start-ServiceFabricApplicationUpgrade`。

## <a name="steps-to-remove-the-application"></a>將應用程式移除的步驟

若要將應用程式移除，請參閱[這裡](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)所述的步驟。

為了方便使用者，我們提供了 Undeploy.ps1 指令碼以及我們的應用程式。 使用方式。
    - 使用 ```Connect-ServiceFabricCluster``` 連線至 Service Fabric 叢集
    - 執行 Powershell 指令碼 Undeploy.ps1

> [!NOTE]
> 將指令碼和應用程式資料夾 PatchOrchestrationApplication 保存在同一個目錄中。

## <a name="viewing-the-windows-update-results"></a>檢視 Windows Update 結果

修補程式協調流程應用程式會公開 REST API，以向使用者顯示歷程記錄的結果。

可以透過記錄至叢集，然後找出協調員服務的主要複本位址，並從瀏覽器點閱 URL 來查詢 Windows Update 結果。
http://&lt;REPLICA-IP&gt;：&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetWindowsUpdateResults CoordinatorService 的 REST 端點具有動態連接埠，可檢查正確的 URL 參照 ServiceFabric Explorer。
範例︰下列範例中，結果會顯示在 `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults`
![REST 端點的映像](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)


如果在叢集上啟用反向 Proxy，使用者也從叢集外部存取 URL。
需要點閱的端點︰http://&lt;SERVERURL&gt;:&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults 反向 Proxy 可以在叢集上加以啟用，方法是遵循[這裡](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy)的步驟。 

> 
> [!WARNING]
> 一旦設定了反向 Proxy 後，叢集中公開 HTTP 端點的所有微服務就能從叢集外部尋址。

## <a name="diagnostics--health-events"></a>診斷/健康情況事件

### <a name="collecting-patch-orchestration-application-logs"></a>收集修補程式協調流程應用程式記錄

在接下來幾天，會收集修補程式協調流程應用程式的記錄，作為 Service Fabric 記錄的一部分。
在那之前，可以使用下列其中一個方式來收集記錄

#### <a name="locally-on-each-node"></a>本機的每個節點上

會在每個 Service Fabric 叢集節點上本機收集記錄。 存取記錄的位置為 \[Service Fabric\_Installation\_Drive\]:\\PatchOrchestrationApplication\\logs

例如︰如果 Service Fabric 是安裝在 "D" 磁碟機上，則路徑為 D:\\PatchOrchestrationApplication\\logs

#### <a name="central-location"></a>中央位置

如果 WAD 設為必要條件步驟的一部分，就可在 Azure 儲存體中使用修補程式協調流程應用程式的記錄。

### <a name="health-reports"></a>健康情況報告

修補程式協調流程應用程式在下列情況下也會發佈 CoordinatorService 或 NodeAgentService 健康情況報告

#### <a name="windows-update-operation-failed"></a>Windows Update 作業失敗

如果節點上的 Windows Update 作業失敗，就會產生對 NodeAgentService 的健康情況報告。
健康情況報告的詳細資料會包含有問題的節點名稱。

在有問題的節點上順利完成修補後，會自動將報告清除。

#### <a name="node-agent-ntservice-is-down"></a>節點代理程式 NTService 已關閉

如果節點上的 NodeAgentNTService 已關閉，系統就會針對 NodeAgentService 產生警告等級的健康情況報告

#### <a name="repair-manager-is-not-enabled"></a>Repair Manager 尚未啟用

如果在叢集上找不到 Repair Manager 服務，系統就會產生 CoordinatorService 的警告等級健康情況報告。

## <a name="frequently-asked-questions"></a>常見問題集：

問： **當修補程式協調流程應用程式執行時，我看到叢集處於錯誤狀態**。

A. 修補程式協調流程應用程式會在安裝程序期間將節點停用及/或重新啟動，可能會暫時導致叢集的健康情況關閉。

根據應用程式的原則，可能是一個節點在修補作業期間關閉，或是整個升級網域可能會同時關閉。

請注意，Windows Update 安裝結束時，在重新啟動前會將節點重新啟用。

範例︰在以下案例中，由於 2 個節點關閉且違反 MaxPercentageUnhealthNodes 原則，叢集會暫時進入錯誤狀態。 它是暫時性錯誤，直到修補作業進行中為止。

![健康情況不良之叢集的映像](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

如果問題持續發生，請參閱〈疑難排解〉一節

問： **可以使用獨立叢集的補充程式協調流程應用程式嗎？**

A. 還不行，獨立叢集的支援即將推出。

問： **修補程式協調流程應用程式處於警告狀態**

A. 檢查針對應用程式所公佈的健康情況報告是否為根本原因。 警告通常會包含問題的詳細資料。
如果是暫時性的問題，應用程式預期會從這個狀態中自動復原。

問： **我的叢集健康情況不良。不過，我需要緊急進行 OS 更新**

A. 當叢集健康情況不良時，修補程式協調流程應用程式就不會安裝更新。
請嘗試使叢集處於良好的健康情況，以便將修補程式協調流程應用程式工作流程解除封鎖。

問： **為什麼跨叢集修補執行時間這麼久？**

A. 修補程式協調流程應用程式所花費的時間大部分是取決於下列因素︰

- CoordinatorService 的原則 - `NodeWise` 的預設原則會導致一次只能修補一個節點，特別是在我們所建議的較大叢集是使用 `UpgradeDomainWise` 原則在叢集間達到更快速修補的情況下。
- 適用於下載和安裝的更新數目 - 平均 下載和安裝更新所需的時間不應超過幾個小時。
- VM 及網路頻寬的效能。

## <a name="disclaimers"></a>免責聲明

- 修補程式協調流程應用程式會代表使用者接受 Windows Update 的 EULA。 可在應用程式的設定中選擇性地將設定關閉。

- 修補程式協調流程應用程式會收集遙測來追蹤使用情形和效能。
    應用程式的遙測會遵循 Service Fabric 執行階段遙測設定 (預設為開啟) 的設定。

## <a name="troubleshooting-help"></a>疑難排解說明

### <a name="node-not-coming-back-to-up-state"></a>節點不會回到開啟狀態

**節點可能會卡在停用狀態** 當排程進行作業的節點由於暫止安全性檢查而無法停用時，可能會發生這種情形。 若要補救這種情況，請確定有足夠的健康情況良好之節點。

**節點可能會由於下列原因而卡在停用狀態**

- 已將節點手動停用。
- 因 Azure 基礎結構作業正在進行中而導致節點停用。
- 修補程式協調流程應用程式已暫時將節點停用，以修補節點。

**節點可能會由於下列原因而卡在關閉狀態**

- 已透過手動方式將節點置於關閉狀態。
- 節點正在進行重新啟動 (可能會由修補程式協調流程應用程式觸發)。
- 由於 VM/電腦故障或網路連線問題，已將節點關閉。

### <a name="updates-were-skipped-on-some-nodes"></a>已略過某些節點上的更新

修補程式協調流程應用程式會根據重新排定的原則嘗試安裝 Windows Update。 服務會根據應用程式原則嘗試將節點修復並略過更新。

在這種情況下，系統會針對節點代理程式服務產生警告等級的健康情況報告。
Windows Update 的結果也會包含可能的失敗原因。

### <a name="health-of-the-cluster-goes-to-error-while-update-was-getting-installed"></a>開始安裝更新時，叢集的健康情況會出現錯誤

如果不正確的 Windows Update 關閉了特定節點或升級網域上的應用程式/叢集之健康情況，修補程式協調流程應用程式就不會繼續進行任何後續的 Windows Update 作業，直到叢集再度變成健康情況良好為止。

系統管理員必須介入並了解 Windows Update 造成應用程式/叢集的健康情況不良的原因。

