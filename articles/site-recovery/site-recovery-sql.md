---
title: "使用 SQL Server 和 Azure Site Recovery 複寫應用程式 | Microsoft Docs"
description: "本文說明如何使用 SQL Server 災害復原功能的 Azure Site Recovery 複寫 SQL Server。"
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: pratshar
translationtype: Human Translation
ms.sourcegitcommit: 9ea73dd91c9637692bbc3d6d2aa97fbed7ae500d
ms.openlocfilehash: 79c110031a47f1bdb78f4acfcadd7bff1e909807
ms.lasthandoff: 02/23/2017


---
# <a name="protect-sql-server-using-sql-server-disaster-recovery-and-azure-site-recovery"></a>使用 SQL Server 災害復原和 Azure Site Recovery 保護 SQL Server

本文說明如何使用一套 SQL Server 商務持續性和災害復原 (BCDR) 技術，加上 [Azure Site Recovery](site-recovery-overview.md)，以保護應用程式的 SQL Server 後端。

開始之前，請確定您了解 SQL Server 災害復原功能，包括容錯移轉叢集、AlwaysOn 可用性群組、資料庫鏡像和記錄傳送。


## <a name="sql-server-deployments"></a>SQL Server 部署

許多工作負載都使用 SQL Server 做為基礎，還可以與 SharePoint、Dynamics 和 SAP 等應用程式整合，以實作資料服務。  有許多方法可以部署 SQL Server：

* **獨立 SQL Server**：SQL Server 和所有資料庫都裝載在單一電腦 (實體或虛擬) 上。 如果是虛擬，則主機叢集用於高可用性。 不實作來賓層級的高可用性。
* **SQL Server 容錯移轉叢集執行個體 (Always ON FCI)**：在 Windows 容錯移轉叢集中，設定&2; 個或更多個具有共用磁碟的 SQL Server 執行個體節點。 如果有一個節點關閉，叢集可以將 SQL Server 容錯移轉至另一個執行個體。 這項設定通常用在主要網站實作高可用性。 這種部署無法防止共用儲存體層中發生失敗或中斷。 共用磁碟可以使用 iSCSI、光纖通道或共用 vhdx 來實作。
* **SQL Always On 可用性群組**：在不共用任何內容的叢集中設定兩個節點，其中此叢集的 SQL Server 資料庫是設定在具有同步複寫和自動容錯移轉功能的可用性群組中。

 本文利用下列原生 SQL 災害復原技術，將資料庫復原至遠端網站︰

* 支援 SQL Server 2012 或 2014 Enterprise 版本之災害復原的「SQL AlwaysOn 可用性群組」。
* SQL Server Standard 版本 (任何版本) 或 SQL Server 2008 R2 的高安全性模式「SQL 資料庫鏡像」。

## <a name="site-recovery-support"></a>Site Recovery 支援

### <a name="supported-scenarios"></a>支援的案例
資料表中摘要說明 Site Recovery 可以保護 SQL Server。

**案例** | **至次要網站** | **至 Azure**
--- | --- | ---
**Hyper-V** | 是 | 是
**VMware** | 是 | 是
**實體伺服器** | 是 | 是

### <a name="supported-sql-server-versions"></a>支援的 SQL Server 版本
在支援的案例中，支援這些 SQL Server 版本：

* SQL Server 2014 Enterprise 和 Standard
* SQL Server 2012 Enterprise 和 Standard
* SQL Server 2008 R2 Enterprise 和 Standard

### <a name="supported-sql-server-integration"></a>支援的 SQL Server 整合

Site Recovery 可以與資料表中摘要說明的原生 SQL Server BCDR 技術整合，以提供災害復原解決方案。

**功能** | **詳細資料** | **SQL Server** |
--- | --- | ---
**AlwaysOn 可用性群組** | 多個 SQL Server 獨立執行個體中，每個都在含有多個節點的容錯移轉叢集中執行。<br/><br/>資料庫可以分組到可以在 SQL Server 執行個體上複製 (鏡像) 的容錯移轉群組，因此不需要任何共用儲存體。<br/><br/>在主要站台與一或多個次要站台之間提供災害復原功能。 使用同步複寫與自動容錯移轉在可用性群組中設定 SQL Server 資料庫時，可以在不共用任何內容的叢集中設定兩個節點。 | SQL Server 2014 和 2012 Enterprise 版本
**容錯移轉叢集 (AlwaysOn FCI)** | SQL Server 會針對內部部署 SQL Server 工作負載的高可用性，運用 Windows 容錯移轉叢集。<br/><br/>使用共用磁碟執行 SQL Server 執行個體的節點是在容錯移轉叢集中設定。 如果執行個體關閉，叢集會容錯移轉至另一個節點。<br/><br/>叢集無法防止共用儲存體失敗或中斷。 共用磁碟可以使用 iSCSI、光纖通道或共用 VHDX 實作。 | SQL Server Enterprise Edition<br/><br/>SQL Server Standard 版本 (僅限兩個節點)
**資料庫鏡像 (高安全性模式)** | 將單一資料庫保護為單一次要複本。 提供高安全性 (同步) 和高效能 (非同步) 複寫模式。 不需要容錯移轉叢集。 | SQL Server 2008 R2<br/><br/>SQL Server Enterprise 所有版本
**獨立式 SQL Server** | SQL Server 和資料庫裝載在單一伺服器 (實體或虛擬) 上。 如果是虛擬伺服器，則主機叢集用於高可用性。 沒有來賓層級的高可用性。 | Enterprise 或 Standard Edition

## <a name="deployment-recommendations"></a>部署建議

下表摘要說明我們將 SQL Server BCDR 技術與 Site Recovery 整合的建議。

| **版本** | **版本** | **部署** | **內部部署到內部部置** | **內部部署到 Azure** |
| --- | --- | --- | --- | --- |
| SQL Server 2014 或 2012 |Enterprise |容錯移轉叢集執行個體 |AlwaysOn 可用性群組 |AlwaysOn 可用性群組 |
|| Enterprise |高可用性的 AlwaysOn 可用性群組 |AlwaysOn 可用性群組 |AlwaysOn 可用性群組 | |
|| 標準 |容錯移轉叢集執行個體 (FCI) |包含本機鏡像的 Site Recovery 複寫 |包含本機鏡像的 Site Recovery 複寫 | |
|| Enterprise 或 Standard |獨立 |Site Recovery 複寫 |Site Recovery 複寫 | |
| SQL Server 2008 R2 |Enterprise 或 Standard |容錯移轉叢集執行個體 (FCI) |包含本機鏡像的 Site Recovery 複寫 |包含本機鏡像的 Site Recovery 複寫 |
|| Enterprise 或 Standard |獨立 |Site Recovery 複寫 |Site Recovery 複寫 | |
| SQL Server (任何版本) |Enterprise 或 Standard |容錯移轉叢集執行個體 - DTC 應用程式 |Site Recovery 複寫 |不支援 |

## <a name="deployment-prerequisites"></a>部署必要條件

* 執行支援的 SQL Server 版本的內部部署 SQL Server 部署。 通常，您的 SQL Server 也需要 Active Directory。
* 您要部署之案例的需求。 深入了解[複寫至 Azure](site-recovery-support-matrix-to-azure.md) 和[內部部署](site-recovery-support-matrix.md)的支援需求，以及[部署必要條件](site-recovery-prereq.md)。
* 如果要在 Azure 中設定復原，請在您的 SQL Server 虛擬機器上執行 [Azure 虛擬機器整備評估](http://www.microsoft.com/download/details.aspx?id=40898)工具，以確定它們與 Azure 和 Site Recovery 相容。

## <a name="set-up-active-directory"></a>設定 Active Directory

在次要復原網站上設定 Active Directory，讓 SQL Server 正常運作。

* **小型企業** — 如果只有少數應用程式，且內部部署網站只有一個網域控制站，當您想要容錯移轉整個網站時，建議您使用 Site Recovery 複寫，將網域控制站複寫至次要資料中心或 Azure。
* **中大型企業** — 如果您有大量的應用程式和一個 Active Directory 樹系，而您想要依應用程式或工作負載容錯移轉，建議您在次要資料中心或 Azure 中設定其他網域控制站。 如果您使用 AlwaysOn 可用性群組復原至遠端網站，建議您在次要網站或 Azure 上設定其他不同的網域控制站，供已復原的 SQL Server 執行個體使用。

本文中的指示假設在次要位置中有網域控制站。 [閱讀更多](site-recovery-active-directory.md) 有關使用 Site Recovery 保護 Active Directory。

## <a name="integrate-with-sql-server-alwayson-for-replication-to-azure-classic-portal-with-a-vmmconfiguration-server"></a>與 SQL Server Always On 整合以複寫至 Azure (傳統入口網站搭配 VMM/組態伺服器)


Site Recovery 原生支援 SQL AlwaysOn。 如果您已經建立 SQL 可用性群組且 Azure 虛擬機器設定為次要位置，則您可以使用 Site Recovery 管理可用性群組的容錯移轉。

> [!NOTE]
> 這項功能目前為預覽狀態。 當主要網站有在 System Center VMM 雲端中管理的 Hyper-V 主機伺服器，或您已經設定 [VMware 複寫](site-recovery-vmware-to-azure.md)時，就可使用此功能。 新的 Azure 入口網站中目前無法使用此功能。 如果您使用新的 Azure 入口網站，請依照[本節](site-recovery-sql.md#integrate-with-sql-server-alwayson-for-replication-to-azure-azure-portalclassic-portal-with-no-vmmconfiguration-server)中的步驟操作。
>
>


#### <a name="before-you-start"></a>開始之前

若要整合 SQL AlwaysOn 與 Site Recovery，您需要：

* 內部部署 SQL Server (獨立伺服器或容錯移轉叢集)。
* 一或多個已安裝 SQL Server 的 Azure 虛擬機器。
* 在內部部署 SQL Server 和在 Azure 中執行的 SQL Server 之間設定的 SQL Server 可用性群組。
* 在內部部署 SQL Server 上啟用的 PowerShell 遠端。 VMM 伺服器或組態伺服器必須能夠對 SQL Server 電腦進行 PowerShell 遠端呼叫。
* 應該將使用者帳戶新增至內部部署 SQL Server 電腦。 在至少具有這些權限的 SQL Server 群組中新增︰
  * ALTER AVAILABILITY GROUP - 權限[這裡](https://msdn.microsoft.com/library/hh231018.aspx)和[這裡](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
  * ALTER DATABASE - 權限 [這裡](https://msdn.microsoft.com/library/ff877956.aspx#Security)
* 如果您執行 VMM，應該在 VMM 伺服器上建立 RunAs 帳戶
- 如果您執行 VMware，應該使用 CSPSConfigtool.exe 在組態伺服器上建立帳戶
* 應該在內部部署執行的 SQL Server 和在 Azure VM 上安裝 SQL PS 模組。
* 應該在 Azure VM 上安裝 VM 代理程式。
* 在 Azure VM 上執行的 SQL Server 中，NTAUTHORITY\System 應該具有下列權限。
  * ALTER AVAILABILITY GROUP - 權限[這裡](https://msdn.microsoft.com/library/hh231018.aspx)和[這裡](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
  * ALTER DATABASE - 權限 [這裡](https://msdn.microsoft.com/library/ff877956.aspx#Security)

### <a name="add-a-sql-server"></a>新增 SQL Server
1. 按一下 [加入 SQL]  以加入新的 SQL Server。

    ![加入 SQL](./media/site-recovery-sql/add-sql.png)
2. 在 [設定 SQL 設定] > [名稱] 中，提供 SQL Server 的易記名稱。
3. 在 [SQL Server (FQDN)] 中，指定您想要新增之來源 SQL Server 的 FQDN。 如果 SQL Server 安裝在容錯移轉叢集上，請提供叢集的 FQDN，而不是叢集節點的 FQDN。  
4. 在 [SQL Server 執行個體] 中，選擇預設執行個體或提供自訂名稱。
5. 在 [管理伺服器] 中，選取保存庫中註冊的 VMM 伺服器或組態伺服器。 Site Recovery 會使用此伺服器與 SQL Server 通訊。
6. 在 [執行身分帳戶] 中，提供 VMM RunAs 帳戶或組態伺服器帳戶的名稱。 此帳戶會用來存取 SQL Server，而且對於 SQL Server 電腦上的可用性群組，應該具有讀取和容錯移轉權限。

    ![加入 SQL 對話方塊](./media/site-recovery-sql/add-sql-dialog.png)

新增 SQL Server 之後，它就會顯示在 [SQL Server]  索引標籤中。

![SQL Server 清單](./media/site-recovery-sql/sql-server-list.png)

### <a name="add-a-sql-availability-group"></a>新增 SQL 可用性群組

1. 在您新增的 SQL Server 中，按一下 [新增 SQL 可用性群組]。

    ![加入 SQL AG](./media/site-recovery-sql/add-sqlag.png)
2. 可用性群組可以複寫至一或多個 Azure VM。 新增群組時，針對您希望 Site Recovery 將群組容錯移轉到的那個 Azure VM，您需要提供名稱和訂用帳戶。

    ![加入 SQL AG 對話方塊](./media/site-recovery-sql/add-sqlag-dialog.png)
3. 在此範例中，可用性群組 DB1-AG 於容錯移轉之後，在訂用帳戶 DevTesting2 內執行的虛擬機器 SQLAGVM2 上，就會變成主要群組。

> [!NOTE]
> 在新增的 SQL Server 上，只有主要的可用性群組才能新增至 Site Recovery。 如果您讓一個可用性群組在 SQL Server 上成為主要群組，或在新增它之後又在 SQL Server 新增更多可用性群組，請在 SQL Server 上重新整理該群組。
>
>

### <a name="create-a-recovery-plan"></a>建立復原計畫

使用虛擬機器和可用性群組建立復原計劃。 選取 VMM 伺服器或組態伺服器做為來源，選取 Azure 做為目標。

![建立復原計畫](./media/site-recovery-sql/create-rp1.png)

![建立復原計畫](./media/site-recovery-sql/create-rp2.png)

在範例中，Sharepoint 應用程式包含三個虛擬機器，使用 SQL 可用性群組做為後端。 在此復原計畫中，我們可以選取可用性群組和應用程式的 VM。 您可以進一步自訂復原計劃，將 VM 移至不同的容錯移轉群組，以排列容錯移轉順序。 可用性群組做為應用程式後端，永遠是最先容錯移轉。

![自訂復原計劃](./media/site-recovery-sql/customize-rp.png)

### <a name="failover"></a>容錯移轉

將可用性群組新增至復原計劃之後，就可以使用不同的容錯移轉選項。

**容錯移轉** | **詳細資料**
--- | ---
**計劃性容錯移轉** | 規劃的容錯移轉意味著不會遺失資料的容錯移轉。 為了達到此目標，將群組新增至 Site Recovery 時，SQL 可用性群組模式會設定為同步，然後觸發容錯移轉，讓可用性群組在提供的虛擬機器上成為主要群組。 容錯移轉完成之後，可用性模式會設定為觸發規劃的容錯移轉之前的相同值。
**非計劃性容錯移轉** | 未規劃的容錯移轉可能會導致資料遺失。 觸發未規劃的容錯移轉時，群組的可用性模式不變。 將可用性群組新增至 Site Recovery 時，它在提供的虛擬機器上會成為主要群組。 在未規劃的容錯移轉完成之後，且執行 SQL Server 的內部部署伺服器再次可用時，必須在可用性群組上觸發反向複寫。 這個動作在 **SQL Server** > **SQL 可用性群組** 中可用，而不是在復原計劃上。
**測試容錯移轉** |不支援 SQL 可用性群組的測試容錯移轉。 如果您觸發測試容錯移轉，將會略過可用性群組的容錯移轉。

請嘗試下列容錯移轉選項。

**選項** | **詳細資料**
--- | ---
**選項 1** | 1.執行應用程式和前端層的測試容錯移轉。<br/><br/>2.更新應用程式層，以便在唯讀模式下存取副本，並執行應用程式的唯讀測試。
**選項 2** | 1.建立 SQL Server 虛擬機器執行個體的副本 (使用站對站的 VMM 複製，或 Azure 備份)，並在測試網路中啟動它<br/><br/> 2.使用復原計畫執行測試容錯移轉。

### <a name="fail-back"></a>容錯回復

如果您想要再次在內部部署 SQL Server 上讓可用性群組成為主要群組，您可以在復原計劃上觸發規劃的容錯移轉，並選取從 Microsoft Azure 到內部部署 VMM 伺服器的方向。

> [!NOTE]
> 在未計劃的容錯移轉之後，應該在可用性群組上觸發反向複寫，以繼續複寫。  在完成此動作之前，複寫會保持暫止。
>
>

## <a name="integrate-with-sql-server-alwayson-for-replication-to-azure-azure-portalclassic-portal-with-no-vmmconfiguration-server"></a>與 SQL Server Always On 整合以複寫至 Azure (Azure 入口網站/不含 VMM/組態伺服器的傳統入口網站)

如果您在新的 Azure 入口網站或傳統入口網站 (如果不使用 VMM 伺服器或組態定伺服器) 中與 SQL Server 可用性群組整合，這些指示很重要。 在此案例中，Azure 自動化 Runbook 可以用於設定 SQL 可用性群組的指令碼式容錯移轉。

以下是您需要採取的動作：

1. 建立指令碼的本機檔案，以容錯移轉可用性群組。 此範例指令碼會在 Azure 複本上指定可用性群組的路徑，並將可用性群組容錯移轉至該複本執行個體。 此指令碼會藉由使用自訂指令碼擴充來傳遞該指令碼，以便在 SQL Server 複本虛擬機器上執行。

        ``Param(
           [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force``

2. 將指令碼上傳至 Azure 儲存體帳戶中的 Blob。 使用範例：

        ``$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
        Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context``

3. 建立 Azure 自動化 Runbook，以便在 Azure 中叫用 SQL Server 複本虛擬機器上的指令碼。 若要這樣做，請使用此範例指令碼。 [深入](site-recovery-runbook-automation.md) 如何在復原計畫中使用自動化 Runbook。

4. 當您建立應用程式的復原計畫時，請新增 "pre-Group 1 boot" 指令碼步驟，以叫用自動化 Runbook 來容錯移轉可用性群組。


5. SQL AlwaysOn 原本就不支援測試容錯移轉。 因此，我們建議您採取下列動作︰
    1. 在 Azure 中裝載可用性群組複本的虛擬機器上，設定 [Azure 備份](../backup/backup-azure-vms.md)。
    1. 觸發復原計劃的測試容錯移轉之前，從上一步所使用的備份來復原虛擬機器。
    1. 執行復原計劃的測試容錯移轉。


> [!NOTE]
> 下面的指令碼假設 SQL 可用性群組裝載於傳統 Azure VM，且在步驟&2; 中的還原虛擬機器名稱是 SQLAzureVM-Test。 根據您用於已復原虛擬機器的名稱修改指令碼。
>
>


     ``workflow SQLAvailabilityGroupFailover
     {

         param (
             [Object]$RecoveryPlanContext
         )

         $Cred = Get-AutomationPSCredential -name 'AzureCredential'

         #Connect to Azure
         $AzureAccount = Add-AzureAccount -Credential $Cred
         $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
         Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

         InLineScript
         {
          #Update the script with name of your storage account, key and blob name
          $context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key";
          $sasuri = New-AzureStorageBlobSASToken -Container "script-container"- Blob "AGFailover.ps1" -Permission r -FullUri -Context $context;

          Write-output "failovertype " + $Using:RecoveryPlanContext.FailoverType;

          if ($Using:RecoveryPlanContext.FailoverType -eq "Test")
                {
                    Write-output "tfo"

                    Write-Output "Creating ILB"
                    Add-AzureInternalLoadBalancer -InternalLoadBalancerName SQLAGILB -SubnetName Subnet-1 -ServiceName SQLAzureVM-Test -StaticVNetIPAddress #IP
                    Write-Output "ILB Created"

                    #Update the script with name of the virtual machine recovered using Azure Backup
                    Write-Output "Adding SQL AG Endpoint"
                    Get-AzureVM -ServiceName "SQLAzureVM-Test" -Name "SQLAzureVM-Test"| Add-AzureEndpoint -Name sqlag -LBSetName sqlagset -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName SQLAGILB | Update-AzureVM

                    Write-Output "Added Endpoint"

                    $VM = Get-AzureVM -Name "SQLAzureVM-Test" -ServiceName "SQLAzureVM-Test"

                    Write-Output "UnInstalling custom script extension"
                    Set-AzureVMCustomScriptExtension -Uninstall -ReferenceName CustomScriptExtension -VM $VM |Update-AzureVM
                    Write-Output "Installing custom script extension"
                    Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $vm -Publisher Microsoft.Compute -Version 1.*| Update-AzureVM   

                    Write-output "Starting AG Failover"
                    Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument "-Path sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag"  | Update-AzureVM
                    Write-output "Completed AG Failover"
                }
          else
                {
                Write-output "pfo/ufo";
                #Get the SQL Azure Replica VM.
                #Update the script to use the name of your VM and Cloud Service
                $VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     

                Write-Output "Installing custom script extension"
                #Install the Custom Script Extension on teh SQL Replica VM
                Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.*| Update-AzureVM;

                Write-output "Starting AG Failover";
                #Execute the SQL Failover script
                #Pass the SQL AG path as the argument.

                $AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";

                Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;

                Write-output "Completed AG Failover";

                }

         }
     }``

## <a name="integrate-with-sql-server-alwayson-for-replication-to-a-secondary-on-premises-site"></a>與 SQL Server Always On 整合以複寫至次要內部部署網站

如果 SQL Server 為了高可用性而使用可用性群組 (或 FCI)，建議您也在復原網站上使用可用性群組。 請注意，這適用於不使用分散式交易的應用程式。

1. [設定資料庫](https://msdn.microsoft.com/library/hh213078.aspx) 。
2. 在次要網站上建立虛擬網路。
3. 在虛擬網路和主要網站之間設定網站間 VPN 連線。
4. 在復原網站上建立虛擬機器，並在此虛擬機器上安裝 SQL Server。
5. 將現有的 AlwaysOn 可用性群組延伸至新的 SQL Server VM。 將此 SQL Server 執行個體設定為非同步複本。
6. 建立可用性群組接聽程式，或更新現有的接聽程式，以包含非同步複本虛擬機器。
7. 請確定應用程式伺服器陣列是使用接聽程式來設定。 如果是使用資料庫伺服器名稱來設定，請更新為使用接聽程式，如此您就不需要在容錯移轉後重新設定。

對於使用分散式交易的應用程式，建議您使用 [SAN 複寫](site-recovery-vmm-san.md)或 [VMware/實體伺服器站對站複寫](site-recovery-vmware-to-vmware.md)來部署 Site Recovery。

### <a name="recovery-plan-considerations"></a>復原計畫考量
1. 將此範例指令碼新增至主要和次要網站上的 VMM 程式庫。

        ``Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force``
2. 當您建立應用程式的復原計畫時，新增 "pre-Group 1 boot" 指令碼式步驟，以叫用指令碼來容錯移轉可用性群組。

## <a name="protect-a-standalone-sql-server"></a>保護獨立式 SQL Server

在此案例中，建議您使用 Site Recovery 複寫保護 SQL Server 電腦。 確切步驟將取決於 SQL Server 是 VM 還是實體伺服器，以及您想要複寫至 Azure 還是次要內部部署網站。 了解 [Site Recovery 案例](site-recovery-overview.md)。

## <a name="protect-a-sql-server-cluster-standard-editionwindows-server-2008-r2"></a>保護 SQL Server 叢集 (標準版/Windows Server 2008 R2)

對於執行 SQL Server Standard Edition 或 SQL Server 2008 R2 的叢集，建議您使用 Site Recovery 複寫保護 SQL Server。

### <a name="on-premises-to-on-premises"></a>內部部署至內部部署

* 如果應用程式使用分散式交易，建議您針對 Hyper-V 環境部署 [Site Recovery 搭配 SAN 複寫](site-recovery-vmm-san.md)，或者，針對 VMware 環境，則部署 [VMware/實體伺服器至 VMware](site-recovery-vmware-to-vmware.md)。
* 針對非 DTC 應用程式，請利用本機高安全性資料庫鏡像，依上述方式將叢集復原成獨立伺服器。

### <a name="on-premises-to-azure"></a>內部部署至 Azure

複寫至 Azure 時，Site Recovery 不提供來賓叢集支援。 SQL Server 也不會為 Standard Edition 提供低成本的災害復原解決方案。 在此案例中，建議您將內部部署 SQL Server 叢集保護至獨立式 SQL Server，並在 Azure 中復原。

1. 在內部部署站台上設定一個額外的獨立 SQL Server 執行個體。
2. 設定此執行個體做為您要保護之資料庫的鏡像。 在高安全性模式下設定鏡像。
3. 針對 [Hyper-V](site-recovery-hyper-v-site-to-azure.md) 或 [VMware WM/實體伺服器](site-recovery-vmware-to-azure-classic.md)，在內部部署網站上設定 Site Recovery。
4. 使用 Site Recovery 複寫將新的 SQL Server 執行個體複寫至 Azure。 因為它是高安全性鏡像副本，它會與主要叢集同步處理，但是會使用 Site Recovery 複寫將它複寫至 Azure。


![標準叢集](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)

### <a name="failback-considerations"></a>容錯回復考量

就 SQL Server 標準叢集而言，若要在未規劃的容錯移轉之後進行容錯回復，必須從鏡像執行個體進行 SQL 備份並還原至原始叢集，並重新建立鏡像。。

## <a name="next-steps"></a>後續步驟
[深入了解](site-recovery-components.md) Site Recovery 架構。

