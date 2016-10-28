<properties
 pageTitle="在 Azure VM 中建立 HPC Pack 前端節點 | Microsoft Azure"
 description="了解如何使用 Azure 入口網站和資源管理員部署模型，在 Azure VM 中建立 Microsoft HPC Pack 前端節點。"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="08/17/2016"
 ms.author="danlep"/>

# 使用 Marketplace 映像在 Azure VM 中建立 HPC Pack 叢集的前端節點


您可以使用來自 Azure Marketplace 的 [Microsoft HPC Pack 虛擬機器映像](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)和 Azure 入口網站，來建立 HPC 叢集的前端節點。此 HPC Pack VM 映像是基於已預先安裝 HPC Pack 2012 R2 Update 3 的 Windows Server 2012 R2 Datacenter。使用此前端節點當作 Azure 中 HPC Pack 的概念證明部署。然後您可以將計算節點加入叢集以執行 HPC 工作負載。



>[AZURE.TIP]若要在 Azure 中部署包含前端節點和計算節點的完整 HPC Pack 叢集，建議您使用自動化的方法。選項包括 [HPC Pack IaaS 部署指令碼](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)和 [HPC Pack cluster for Windows workloads (適用於 Windows 工作負載的 HPC Pack 叢集)](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/) Resource Manager 範本。如需其他範本，請參閱 [Azure 中的 HPC Pack 叢集選項](virtual-machines-windows-hpcpack-cluster-options.md)。


## 規劃考量

如下圖所示，您會將 HPC Pack 前端節點部署在 Azure 虛擬網路的 Active Directory 網域中。

![HPC Pack 前端節點][headnode]

* **Active Directory 網域** - 在您啟動 VM 上的 HPC 服務之前，HPC Pack 前端節點必須先加入 Azure 中的 Active Directory 網域。如本文中所示，針對概念證明部署，您可以在啟動 HPC 服務之前，將您為前端節點建立的 VM 升級為網域控制站。另一個選項則是在 Azure 中部署您要將前端節點 VM 加入其中的個別網域控制站和樹系。

* **Azure 虛擬網路** - 當您使用 Resource Manager 部署模型來部署前端節點時，您會指定或建立 Azure 虛擬網路。如果您需要將前端節點加入現有的 Active Directory 網域中，您就會用到虛擬網路。您稍後也需要使用它來將計算節點 VM 新增到叢集中。

    
## 建立前端節點的步驟

以下是使用 Azure 入口網站，利用 Resource Manager 部署模型為 HPC Pack 前端節點建立 Azure VM 的概略步驟。


1. 如果您想要以個別的網域控制站 VM 在 Azure 中建立新的 Active Directory 樹系，其中一個選項是使用 [Resource Manager 範本](https://azure.microsoft.com/documentation/templates/active-directory-new-domain-ha-2-dc/)。若要進行簡單的概念驗證部署，則可以略過此步驟，而將前端節點 VM 本身設定為網域控制站。本文稍後將說明此選項。
    
2. 在 Azure Marketplace 的 [[HPC Pack 2012 R2 on Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)] \(Windows Server 2012 R2 上的 HPC Pack 2012 R2) 頁面上，按一下 [建立虛擬機器]。

3. 在入口網站中，於 [HPC Pack 2012 R2 on Windows Server 2012 R2] \(Windows Server 2012 R2 上的 HPC Pack 2012 R2) 頁面上，選取 [Resource Manager] 部署模型，然後按一下 [建立]。

    ![HPC Pack 映像][marketplace]

4. 使用入口網站設定並建立該 VM。如果您不熟悉 Azure，請依照[在 Azure 入口網站中建立 Windows 虛擬機器](virtual-machines-windows-hero-tutorial.md)教學課程操作。若要進行概念驗證部署，您通常可以接受預設或建議的設定。

    >[AZURE.NOTE]如果您想要將前端節點加入 Azure 現有的 Active Directory 網域中，請務必在建立 VM 時，指定該網域的虛擬網路。
       
4. 建立 VM 且 VM 開始執行之後，請透過「遠端桌面」[連接到 VM](virtual-machines-windows-connect-logon.md)。

5. 將 VM 加入現有的網域樹系，或是在 VM 本身建立網域樹系。

    * 如果您是在具有現有網域樹系的 Azure 虛擬網路中建立了 VM，請使用標準的「伺服器管理員」或 Windows PowerShell 工具將該 VM 加入網域樹系中。然後重新啟動。

    * 如果您是在無現有網域樹系的新虛擬網路中建立了 VM，則請將該 VM 升級為網域控制站。請使用標準步驟在前端節點上安裝並設定「Active Directory 網域服務」角色。如需詳細步驟，請參閱[安裝新的 Windows Server 2012 Active Directory 樹系](https://technet.microsoft.com/library/jj574166.aspx)。

5. 在 VM 已開始執行並加入 Active Directory 樹系之後，請依下列方式啟動 HPC Pack 服務：

    a.使用具備本機 Administrators 群組成員身分的網域帳戶來連接到前端節點 VM。例如，使用建立前端節點 VM 時所設定的系統管理員帳戶。

    b.針對預設前端節點組態，請以系統管理員身分啟動 Windows PowerShell，並輸入下列命令：

    ```
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```

    可能需要幾分鐘的時間，HPC Pack 服務才能啟動。

    如需其他前端節點組態選項，請輸入 `get-help HPCHNPrepare.ps1`。


## 後續步驟

* 您現在已可以使用 HPC Pack 叢集的前端節點。例如，啟動「HPC 叢集管理員」並完成[部署待辦事項清單](https://technet.microsoft.com/library/jj884141.aspx)。
* 如果您想要視需要增加叢集計算能力，請在雲端服務中新增 [Azure 高載節點](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md)。

* 嘗試在叢集上執行測試工作負載。如需範例，請參閱 HPC Pack [快速入門指南](https://technet.microsoft.com/library/jj884144)。

<!--Image references-->
[headnode]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/marketplace.png

<!---HONumber=AcomDC_0824_2016-->