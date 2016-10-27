<properties
   pageTitle="在獨立 Service Fabric 叢集中新增或移除節點 | Microsoft Azure"
   description="了解如何在執行 Windows Server 的實體或虛擬電腦上 (無論是在內部部署或任何雲端) 對 Azure Service Fabric 叢集新增或移除節點。"
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/20/2016"
   ms.author="dkshir;chackdan"/>



# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>在執行於 Windows Server 上的獨立 Service Fabric 叢集中新增或移除節點

[在 Windows Server 電腦上建立了獨立 Service Fabric 叢集](service-fabric-cluster-creation-for-windows-server.md)後，您的業務需求可能會改變，因此您可能需要在叢集中新增或移除多個節點。 本文提供用以達成此目標的詳細步驟。


## <a name="add-nodes-to-your-cluster"></a>將節點新增至叢集

1. 按照 [準備電腦以符合叢集部署的必要條件](service-fabric-cluster-creation-for-windows-server.md#preparemachines) 一節所提到的步驟進行，以準備要在叢集中新增的 VM/電腦。
2. 規劃要用來新增此 VM/電腦的容錯網域和升級網域。
3. 以遠端桌面 (RDP) 登入到要在叢集中新增的 VM/電腦。
4. 複製或 [下載適用於 Windows Server 的 Service Fabric 獨立封裝](http://go.microsoft.com/fwlink/?LinkId=730690) 到此 VM/電腦，然後解壓縮封裝。
5. 以系統管理員身分執行 PowerShell，然後瀏覽至解壓縮封裝的位置。
6. 使用描述要新增之新節點的參數執行「AddNode.ps1」  Powershell。 下列範例會將稱為 VM5 的新節點 (類型 NodeType0、IP 位址 182.17.34.52) 新增至 UD1 和 FD1。 「ExistingClusterConnectionEndPoint」  是已在現有叢集中之節點的 Connect Endpoint。 對於此端點，您可以選擇叢集中「任何」  節點的 IP 位址。

```
.\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain FD1 -AcceptEULA

```

## <a name="remove-nodes-from-your-cluster"></a>從叢集移除節點

1. 根據為叢集選擇的「可靠性」等級而定，您無法移除主要節點類型的前 n 個 (3/5/7/9) 節點
2. 不支援在 dev 叢集上執行 RemoveNode 命令。
2. 以遠端桌面 (RDP) 登入到您想要從叢集移除的 VM/電腦。
2. 複製或 [下載適用於 Windows Server 的 Service Fabric 獨立封裝](http://go.microsoft.com/fwlink/?LinkId=730690) ，然後將此封裝解壓縮到此 VM/電腦。
3. 以系統管理員身分執行 PowerShell，然後瀏覽至解壓縮封裝的位置。
4. 執行「RemoveNode.ps1」  PowerShell。 下列範例會從叢集移除目前的節點。 「ExistingClusterConnectionEndPoint」  是已在現有叢集中之節點的 Connect Endpoint。 對於此端點，您必須選擇叢集中「任何」  **其他節點** 的 IP 位址。

```
.\RemoveNode.ps1 -ExistingClusterConnectionEndPoint 182.17.34.50:19000
```

下一版將修復的已知缺失 - 即使在移除節點後，節點在查詢和 SFX 中仍顯示為已關閉。 

## <a name="next-steps"></a>後續步驟
- [獨立 Windows 叢集的組態設定](service-fabric-cluster-manifest.md)
- [使用 Windows 安全性保護 Windows 上的獨立叢集](service-fabric-windows-cluster-windows-security.md)
- [使用 X509 憑證保護 Windows 上的獨立叢集](service-fabric-windows-cluster-x509-security.md)
- [建立具有執行 Windows 之 Azure VM 的獨立 Service Fabric 叢集](service-fabric-cluster-creation-with-windows-azure-vms.md)



<!--HONumber=Oct16_HO2-->


