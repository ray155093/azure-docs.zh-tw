---
title: "在 Windows Server 上升級獨立的 Service Fabric 叢集 | Microsoft Docs"
description: "升級執行獨立 Service Fabric 叢集的 Service Fabric 程式碼和/或組態，包括設定叢集更新模式"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: e9d7e1b5976719c07de78b01408b2546b4fec297
ms.openlocfilehash: 217715ad1657582eb35008b765de6d19bd2a8b0b
ms.lasthandoff: 02/16/2017


---
# <a name="upgrade-your-standalone-service-fabric-cluster-on-windows-server"></a>在 Windows Server 上升級獨立的 Service Fabric 叢集
> [!div class="op_single_selector"]
> * [Azure 叢集](service-fabric-cluster-upgrade.md)
> * [獨立叢集](service-fabric-cluster-upgrade-windows-server.md)
> 
> 

對於任何現代系統，可升級性的設計是產品達到長期成功的關鍵。 Service Fabric 叢集是您所擁有的資源。 本文說明您可以如何確定叢集一律會執行支援版本的 Service Fabric 程式碼和組態。

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>控制叢集上執行的網狀架構版本
您可以設定您的叢集 (當 Microsoft 發行新版本時) 下載 Service Fabric 更新，或選擇選取您想讓叢集執行的受支援網狀架構版本。 

您可以將 "fabricClusterAutoupgradeEnabled" 叢集組態設定為 true 或 false。

> [!NOTE]
> 請務必保持您的叢集一律執行支援的 Service Fabric 版本。 當我們宣布發行新版本的 Service Fabric 時，從當日起至少 60 天後，舊版就會標示為結束支援。 新的版本會於 [Service Fabric 小組部落格上](https://blogs.msdn.microsoft.com/azureservicefabric/)發佈。 那時就有新的版本可選擇。 
> 
> 

只有當您使用生產樣式節點組態，其中每個 Service Fabric 節點是配置在不同實體或虛擬機器時，您才可以將叢集升級到新的版本。 如果您有開發叢集，其中在單一實體或虛擬機器上有一個以上的 Service Fabric 節點，您必須停止您的叢集，並重新建立新的版本。

有兩個不同工作流程可以將您的叢集升級至最新或支援的 Service Fabric 版本。 一個用於具有連線可以自動下載最新版本的叢集，第二個用於沒有連線可以下載最新版本 Service Fabric 的叢集。

### <a name="upgrade-the-clusters-with-connectivity-to-download-the-latest-code-and-configuration"></a>升級具有連線可以下載最新程式碼和組態的叢集
如果您的叢集節點具有與 [http://download.microsoft.com](http://download.microsoft.com) 的網際網路連線，則使用這些步驟將您的叢集升級至支援的版本 

對於可以連線至 [http://download.microsoft.com](http://download.microsoft.com) 的叢集，我們會定期檢查新版本 Service Fabric 的可用性。

當新的 Service Fabric 版本可用時，會將套件下載至本機叢集並且佈建以進行升級。 除了通知客戶這個新版本之外，系統會顯示明確的叢集健康情況警告，如下所示︰

「目前叢集版本 [版本#] 支援結束 [日期]。」一旦叢集執行最新版本，警告就會消失。

#### <a name="cluster-upgrade-workflow"></a>叢集升級工作流程。
一旦您看到叢集健康情況警告，您需要執行下列操作︰

1. 從具有系統管理員存取權的任何電腦，將叢集連接至列為叢集中節點的所有電腦。 執行此指令碼所在的電腦不一定是叢集的一部分
   
    ```powershell
   
    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3" 
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```
2. 取得您可以升級的 Service Fabric 版本的清單
   
    ```powershell
   
    ###### Get the list of available service fabric versions 
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```
   
    您應該會看到如下的輸出：
   
    ![取得網狀架構版本][getfabversions]
3. 使用 [Start-ServiceFabricClusterUpgrade PowerShell cmd ](https://msdn.microsoft.com/library/mt125872.aspx)開始將叢集升級至其中一個可用版本
   
    ```Powershell
   
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback
   
    ###### Here is a filled out example
   
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
   
    ```
   您可以在 Service Fabric Explorer 上或執行下列 Power Shell 命令以監視升級的進度
   
    ```powershell
   
    Get-ServiceFabricClusterUpgrade
    ```
   
    如果不符合叢集健康狀態原則，則會回復升級。 您可以指定當時 Start-ServiceFabricClusterUpgrade 命令的自訂健康情況原則，如需詳細資訊，請參閱[這份文件](https://msdn.microsoft.com/library/mt125872.aspx)。 

在解決導致復原的問題後，您需要依照之前的相同步驟再次起始升級。

### <a name="upgrade-the-clusters-with-uno-connectivityu-to-download-the-latest-code-and-configuration"></a>升級「沒有連線」<U></u>可以下載最新程式碼和組態的叢集
如果您的叢集節點**沒有**與 [http://download.microsoft.com](http://download.microsoft.com) 的網際網路連線，則使用這些步驟將您的叢集升級至支援的版本 

> [!NOTE]
> 如果您正在執行的叢集未連接至網際網路，您必須監視 Service Fabric 小組部落格，取得新發行的通知。 系統**不會**顯示任何叢集健康情況警告來警示您。  
> 
> 

1. 修改您的叢集組態以將下列屬性設定為 false。
   
        "fabricClusterAutoupgradeEnabled": false,

並開始組態升級。 請參閱 [Start-ServiceFabricClusterConfigurationUpgrade PS cmd ](https://msdn.microsoft.com/en-us/library/mt788302.aspx) 以取得使用方式詳細資料。 請確定在您開始組態升級之前，更新 JSON 中的 'clusterConfigurationVersion'。

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File> 

```

#### <a name="cluster-upgrade-workflow"></a>叢集升級工作流程。
1. 從叢集中的其中一個節點執行 Get-ServiceFabricClusterUpgrade，並記下 TargetCodeVersion。
2. 從與網際網路連線的電腦執行下列程式碼，以根據目前版本列出所有升級相容版本 ，並從相關聯的下載連結下載對應封裝。
   ```powershell
   
    ###### Get list of all upgrade compatible packages
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1>
    ```
3. 從具有系統管理員存取權的任何電腦，將叢集連接至列為叢集中節點的所有電腦。 執行此指令碼所在的電腦不一定是叢集的一部分 
   
    ```powershell
   
    ###### connect to the cluster
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3" 
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```
4. 將下載的套件複製到叢集映像存放區。
   
    ```powershell
   
   ###### Get the list of available service fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"
   
   ###### Here is a filled out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"

    ```

5. 註冊所複製的套件 
   
    ```powershell
   
    ###### Get the list of available service fabric versions 
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file> 
   
    ###### Here is a filled out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab
   
     ```
6. 開始將叢集升級至其中一個可用版本。 
   
    ```Powershell
   
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback
   
    ###### Here is a filled out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
   
    ```
   您可以在 Service Fabric Explorer 上或執行下列 Power Shell 命令以監視升級的進度
   
    ```powershell
   
    Get-ServiceFabricClusterUpgrade
    ```
   
    如果不符合叢集健康狀態原則，則會回復升級。 您可以指定當時 start-serviceFabricClusterUpgrade 命令的自訂健康情況原則，如需詳細資訊，請參閱[這份文件](https://msdn.microsoft.com/library/mt125872.aspx)。 

在解決導致復原的問題後，您需要依照之前的相同步驟再次起始升級。


## <a name="cluster-configuration-upgrade"></a>叢集組態升級
若要執行叢集組態升級，請執行 Start-ServiceFabricClusterConfigurationUpgrade。 組態升級是按升級網域逐一處理。

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File> 

```

### <a name="cluster-certificate-config-upgrade-pls-hold-on-till-v55-is-released-because-cluster-cert-upgrade-doesnt-work-till-v55"></a>叢集憑證組態升級 (請等到 v5.5 發行，因為叢集憑證升級要到 v5.5 才能運作)
叢集憑證是用來在叢集節點之間進行驗證，所以執行憑證變換應更加謹慎，因為失敗將會封鎖叢集節點之間的通訊。
從技術角度來看，支援兩個選項：

1. 單一憑證升級：升級路徑為「憑證 A (主要) -> 憑證 B (主要) -> 憑證 C (主要) -> ...」。 
2. 雙重憑證升級：升級路徑為「憑證 A (主要) -> 憑證 A (主要) 和 B (次要) -> 憑證 B (主要) -> 憑證 B (主要) 和 C (次要) -> 憑證 C (主要) -> ...」


## <a name="next-steps"></a>後續步驟
* 了解如何自訂一些 [Service Fabric 叢集網狀架構設定](service-fabric-cluster-fabric-settings.md)
* 了解如何 [相應放大和相應縮小叢集](service-fabric-cluster-scale-up-down.md)
* 了解 [應用程式升級](service-fabric-application-upgrade.md)

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG

