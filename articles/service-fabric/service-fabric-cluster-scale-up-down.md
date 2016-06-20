<properties
   pageTitle="相應增加或減少 Service Fabric 叢集規模 | Microsoft Azure"
   description="設定每個節點類型/VM 擴展集的自動調整規模規則，以相應增加或減少 Service Fabric 叢集使其符合需求。"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/04/2016"
   ms.author="chackdan"/>


# 使用自動調整規模規則相應增加或減少 Service Fabric 叢集

虛擬機器擴展集是一個 Azure 計算資源，可以將一個 VM 集合當成一組來部署和管理。在 Service Fabric 叢集中定義的每個節點類型，會安裝為不同的 VM擴展集。然後每個節點類型可以獨立相應增加或相應減少，可以開啟不同組的連接埠，並可以有不同的容量度量。若要深入了解，請參閱 [Service Fabric 節點類型](service-fabric-cluster-nodetypes.md)文件。因為叢集中的 Service Fabric 節點類型是在後端由 VM 擴展集建立，所以您必須為每個節點類型/VM 擴展集設定自動調整規模規則。

>[AZURE.NOTE] 您的訂用帳戶必須要有足夠的核心，來新增將構成此叢集的 虛擬機器。目前沒有模型驗證，所以如果叫用任一配額限制，就會收到部署時間失敗。

## 選擇要調整規模的節點類型/VM 擴展集

目前，您不能使用入口網站指定 VM 擴展集的自動調整規模規則，所以請讓我們使用 Azure PowerShell (1.0+) 列出節點類型，然後將自動調整規模規則加入它們。

若要取得建立叢集的 VM 擴展集清單，請執行下列 Cmdlet：

```powershell
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzureRmVmss -ResourceGroupName <RGname> -VMScaleSetName <VM Scale Set name>
```

## 設定節點類型/VM 擴展集的自動調整規模規則

如果您的叢集有多個節點類型，您就需要對每個要相應增加或減少的節點類型/VM 擴展集執行這項作業。請先考慮一定要有的節點數目，再設定自動調整規模。主要節點類型一定要有的節點數目下限是由您已選擇的可靠性層級決定。深入了解[可靠性層級](service-fabric-cluster-capacity.md)。

>[AZURE.NOTE]  主要節點類型相應減少至小於數目下限，會造成叢集不穩定或關閉。這可能導致應用程式和系統服務資料遺失。

自動調整規模功能目前不是由應用程式可能向 Service Fabric 報告的負載所驅動。所以，您現在取得的自動調整規模只由每個 VM 擴展集執行個體所發出的效能計數器驅動。

請遵循下列指示[設定每個 VM 擴展集的自動調整規模](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md)。

>[AZURE.NOTE] 在相應減少的案例中，除非節點類型有黃金級或銀級的持久性，否則必須以適當的節點名稱呼叫 [Remove-ServiceFabricNodeState](https://msdn.microsoft.com/library/azure/mt125993.aspx) Cmdlet。

## Service Fabric Explorer 可能出現的行為

當您相應增加叢集時，Service Fabric Explorer 會反映屬於叢集的節點數目 (VM 擴展集執行個體)。不過，當您相應減少叢集時，除非您以適當的節點名稱呼叫 [Remove-ServiceFabricNodeState](https://msdn.microsoft.com/library/mt125993.aspx) Cmdlet，否則仍然會看到顯示狀況不良狀態的已移除節點/VM 執行個體。

以下是這種行為的說明。

Service Fabric Explorer 列出的節點會反映出 Service Fabric 系統服務 (特別是 FM) 所知叢集曾經有過/現有擁有的節點數目。當您相應減少 VM 擴展集時會刪除 VM，但 FM 系統服務仍然認為節點 (也就是已刪除的對應 VM) 會回復。所以 Service Fabric Explorer 會繼續顯示該節點 (雖然健全狀況狀態可能是錯誤或未知)。

為確保移除 VM 時也移除節點，您有兩個選項︰

1) 為叢集中的節點類型選擇黃金級或銀級持久性 (即將開放)，讓您獲得基礎結構整合。它會在您相應減少時，從我們的系統服務 (FM) 狀態自動移除節點。請參閱[持久性層級詳細資訊](service-fabric-cluster-capacity.md)

2) VM 執行個體一旦相應減少，您就必須呼叫 [Remove-ServiceFabricNodeState](https://msdn.microsoft.com/library/mt125993.aspx) Cmdlet。

>[AZURE.NOTE] Service Fabric 叢集需要有一定數量的節點可隨時啟動，以維護可用性並維持狀態 - 稱為「維持仲裁」。因此，除非您已先執行[狀態的完整備份](service-fabric-reliable-services-backup-restore.md)，否則關閉叢集中的所有電腦通常並不安全。

## 後續步驟
亦請參閱下列文章，了解規劃叢集容量、升級叢集和分割服務︰

- [規劃叢集容量](service-fabric-cluster-capacity.md)
- [叢集升級](service-fabric-cluster-upgrade.md)
- [分割具狀態服務以達最大規模](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png

<!---HONumber=AcomDC_0608_2016-->