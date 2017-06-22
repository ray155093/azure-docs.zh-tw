---
title: "建立具有執行 Windows 之 Azure VM 的獨立叢集 | Microsoft Docs"
description: "了解如何在執行 Windows Server 的 Azure 虛擬機器上建立和管理 Azure Service Fabric 叢集。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 7eeb40d2-fb22-4a77-80ca-f1b46b22edbc
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/21/2017
ms.author: ryanwi;chackdan
redirect_url: /azure/service-fabric/service-fabric-cluster-creation-via-arm
ms.translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: ba1f6e5662700c309fcf198a4e114fd9b2b47c5f
ms.contentlocale: zh-tw
ms.lasthandoff: 03/29/2017


---
# <a name="create-a-three-node-standalone-service-fabric-cluster-with-azure-virtual-machines-running-windows-server"></a>建立三個具有執行 Windows Server 之 Azure 虛擬機器的節點獨立 Service Fabric 叢集
本文說明如何使用適用於 Windows Server 的獨立 Service Fabric 安裝程式，在 Windows 型 Azure 虛擬機器 (VM) 上建立叢集。 這此案例為[建立和管理在 Windows Server 上執行的叢集](service-fabric-cluster-creation-for-windows-server.md)的特殊案例，其 VM 是[執行 Windows Server 的 Azure VM](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，但您不是在建立 [Azure 雲端型 Service Fabric 叢集](service-fabric-cluster-creation-via-portal.md)。 遵循此模式的差別，在於透過下列步驟所建立的獨立 Service Fabric 叢集是完全由您管理，而 Azure 雲端型 Service Fabric 叢集則是由 Service Fabric 資源提供者來管理和升級。

## <a name="steps-to-create-the-standalone-cluster"></a>獨立叢集的建立步驟
1. 登入 Azure 入口網站，並在資源群組中建立新的 Windows Server 2012 R2 Datacenter 或 Windows Server 2016 Datacenter VM。 如需詳細資訊，請閱讀[在 Azure 入口網站中建立 Windows VM](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 一文。
2. 將數個額外的 VM 新增至相同的資源群組。 確定每個 VM 在建立時具有相同的系統管理員使用者名稱和密碼。 一旦建立好，您應該會在相同的虛擬網路中看到這三個 VM。
3. 使用 [[伺服器管理員] &gt; [本機伺服器]](https://technet.microsoft.com/library/jj134147.aspx)儀表板連接到每個 VM 並關閉 Windows 防火牆。 這可確保網路流量可在電腦之間進行通訊。 在連接到每個電腦時，透過開啟命令提示字元並輸入 `ipconfig`來取得 IP 位址。 或者您可以在入口網站上看見每一部機器的 IP 位址，方法是選取資源群組的虛擬網路資源，並檢查為這些機器建立的網路介面。
4. 連接到其中一個 VM，並測試您是否可以成功 ping 到其他兩個 VM。
5. 連接到其中一個 VM，並 [下載適用於 Windows Server 的獨立 Service Fabric 封裝](http://go.microsoft.com/fwlink/?LinkId=730690) 到電腦上的新資料夾，然後解壓縮封裝。
6. 在記事本開啟「ClusterConfig.Unsecure.MultiMachine.json」  檔案，然後使用電腦的三個 IP 位址編輯每個節點。 在頂端變更叢集名稱，然後儲存檔案。  叢集資訊清單的部分範例如下所示。
   
    ```
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "01-2017",
        "nodes": [
        {
            "nodeName": "standalonenode0",
            "iPAddress": "10.1.0.4",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        },
        {
            "nodeName": "standalonenode1",
            "iPAddress": "10.1.0.5",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc2/r0",
            "upgradeDomain": "UD1"
        },
        {
            "nodeName": "standalonenode2",
            "iPAddress": "10.1.0.6",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc3/r0",
            "upgradeDomain": "UD2"
        }
        ],
    ```
7. 若您想要讓此叢集成為安全的叢集，請決定您要使用的安全性措施，並遵循相關連結的步驟：[X509 憑證](service-fabric-windows-cluster-x509-security.md)或 [Windows 安全性](service-fabric-windows-cluster-windows-security.md)。 若使用 Windows 安全性設定叢集，您必須設定網域控制站以管理 Active Directory。 請注意，不支援使用網域控制站電腦做為 Service Fabric 節點。
8. 開啟 [PowerShell ISE 視窗](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise)。 瀏覽至所下載獨立安裝程式封裝的解壓縮資料夾，然後儲存叢集設定檔案。 執行下列 PowerShell 命令來部署叢集：
   
    ```
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
    ```

該指令碼將會於遠端設定 Service Fabric 叢集，並應該會隨著部署推出報告進度。

9. 大約 1 分鐘過後，您就可以透過使用其中一部電腦的 IP 位址連接到 Service Fabric Explorer (例如使用 `http://10.1.0.5:19080/Explorer/index.html`)，來檢查叢集是否已運作。 

## <a name="next-steps"></a>後續步驟
* [在 Windows Server 或 Linux 上建立獨立的 Service Fabric 叢集](service-fabric-deploy-anywhere.md)
* [在獨立 Service Fabric 叢集中新增或移除節點](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [獨立 Windows 叢集的組態設定](service-fabric-cluster-manifest.md)
* [使用 Windows 安全性保護 Windows 上的獨立叢集](service-fabric-windows-cluster-windows-security.md)
* [使用 X509 憑證保護 Windows 上的獨立叢集](service-fabric-windows-cluster-x509-security.md)


