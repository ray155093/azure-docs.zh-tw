---
title: "虛擬機器擴展集概觀 | Microsoft Docs"
description: "深入了解虛擬機器擴展集"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 1/25/2017
ms.author: guybo
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 54673cbc69e418bdbe7cb5791dfb5d20e7ebcb9f
ms.openlocfilehash: 2294587fd3f978a3f8383112ece329b47307db7a
ms.lasthandoff: 03/01/2017


---
# <a name="what-are-virtual-machine-scale-sets-in-azure"></a>什麼是 Azure 中的虛擬機器擴展集？
虛擬機器擴展集是一個您可以用來部署和管理一組相同 VM 的 Azure 計算資源。 藉由將所有的 VM 進行相同設定，設計 VM 擴展集以支援真正的自動調整 (不需要預先佈建 VM)，因而能夠更輕鬆地建置以大型計算、巨量資料和容器化工作負載為目標的大規模服務。

對於需要相應放大計算資源的應用程式，調整作業會隱含地平衡分散到容錯網域和更新網域。 如需 VM 擴展集的簡介，請參閱 [Azure 部落格通知](https://azure.microsoft.com/blog/azure-virtual-machine-scale-sets-ga/)。

若想進一步了解 VM 擴展集，請觀看下列影片：

* [Mark Russinovich 講述 Azure 擴展集](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  
* [Guy Bowerman 與虛擬機器擴展集](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## <a name="creating-and-managing-vm-scale-sets"></a>建立和管理 VM 擴展集
您可以在 [Azure 入口網站](https://portal.azure.com)中選取 [新增]，並在搜尋列中輸入「調整」來建立 VM 擴展集。 您將會在結果中看到「虛擬機器擴展集」。 您可以在這裡填寫必要的欄位，以自訂和部署您的擴展集。 請注意，入口網站中也有可根據 CPU 使用量設定基本自動調整規則的選項。

VM 擴展集也可以使用 JSON 範本和 [REST API](https://msdn.microsoft.com/library/mt589023.aspx) 來定義和部署，如同個別的 Azure Resource Manager VM 一樣。 因此，您可以使用任何標準 Azure 資源管理員部署方法。 如需範本的詳細資訊，請參閱 [編寫 Azure Resource Manager 範本](../azure-resource-manager/resource-group-authoring-templates.md)。

在 Azure 快速入門範本 GitHub 儲存機制[這裡](https://github.com/Azure/azure-quickstart-templates)可以找到 VM 擴展集的範例範本集  (尋找標題中包含 vmss 的範本)

在這些範本的詳細資料頁面中，您會看到連結至入口網站部署功能的按鈕。 若要部署 VM 擴展集，請按一下該按鈕，然後在入口網站中填入所需的任何參數。 如果您不確定某項資源是否支援大寫或混合大小寫，在參數值中一律使用小寫字母和數字會比較安全。 此外也有 VM 擴展集範本的視訊剖析，非常容易存取：

[VM 擴展集範本剖析](https://channel9.msdn.com/Blogs/Azure/VM-Scale-Set-Template-Dissection/player)

## <a name="scaling-a-vm-scale-set-out-and-in"></a>相應放大和相應縮小 VM 擴展集
若要增加或減少 VM 擴展集內的虛擬機器數目，請直接變更 capacity 屬性，並重新部署範本。 這樣的單純性，可讓您在想要定義不受 Azure 自動調整支援的自訂調整事件時，能更輕易地撰寫您的自訂調整層。

如果您想要重新部署範本以變更容量，可以定義較小的範本，使其僅包含 SKU 屬性套件和更新的容量。 範例如 [這裡](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing)

若要逐步完成相關步驟以建立會自動調整的擴展集，請參閱 [在虛擬機器擴展集中自動調整機器](virtual-machine-scale-sets-windows-autoscale.md)

## <a name="monitoring-your-vm-scale-set"></a>監視 VM 擴展集
[Azure 入口網站](https://portal.azure.com)會列出擴展集，並顯示基本屬性以及作業，包括擴展集中的 VM 清單和資源使用圖。 如需詳細資訊，可使用 [Azure 資源總管](https://resources.azure.com) 來檢視 VM 擴展集。 VM 擴展集是 Microsoft.Compute 之下的一項資源，因此您可以展開下列連結，從這個網站加以檢視：

**訂用帳戶 -> 您的訂用帳戶 -> resourceGroups -> 提供者 -> Microsoft.Compute -> virtualMachineScaleSets -> 您的 VM 擴展集 -> 等等**

## <a name="vm-scale-set-scenarios"></a>VM 擴展集案例
本節列出一些典型的 VM 擴展集案例。 某些較高階的 Azure 服務 (例如 Batch、Service Fabric、Azure Container Service) 也會使用這些案例。

* **透過 RDP/SSH 連接到 VM 擴展集執行個體** - VM 擴展集會建立於 VNET 內，且不會為擴展集中的個別 VM 配置公用 IP 位址。 這是件好事，因為您通常不希望計算方格中，為了要將個別公用 IP 位址配置給所有的無狀態資源，而產生支出與管理負擔，而且您可以輕鬆地從 VNET 中的其他資源連接到這些 VM，包括具有公用 IP 位址的資源，像是負載平衡器或獨立虛擬機器。
* **使用 NAT 規則連接到 VM** - 您可以建立一個公用 IP 位址、將它指派給負載平衡器，然後定義輸入 NAT 集區，以便將 IP 位址的連接埠對應至 VM 擴展集中某個 VM 的連接埠。 例如：
  
  | 來源 | 來源連接埠 | 目的地 | 目的地連接埠 |
  | --- | --- | --- | --- |
  |  公用 IP |連接埠 50000 |vmss\_0 |連接埠 22 |
  |  公用 IP |連接埠 50001 |vmss\_1 |連接埠 22 |
  |  公用 IP |連接埠 50002 |vmss\_2 |連接埠 22 |
  
   以下範例會建立一個使用 NAT 規則的 VM 擴展集，以便能使用單一公用 IP，對擴展集內的每個 VM 進行 SSH 連線： [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)
  
   以下是使用 RDP 和 Windows 執行相同作業的範例： [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)
* **使用 Jumpbox 連接到 VM** - 如果您在相同的 VNET 中建立 VM 擴展集和獨立 VM，獨立 VM 與 VMSS VM 將可使用 VNET/子網路為它們定義的內部 IP 位址彼此連接。 如果您建立公用 IP 位址，並將它指派給獨立 VM，您可以透過 RDP 或 SSH 連接到獨立 VM，然後從該部機器連接到 VM 擴展集執行個體。 此時您可能會發現，簡易 VM 擴展集在本質上比在預設組態中設定了公用 IP 位址的獨立 VM 來得安全。
  
   例如，此範本會部署具有獨立 VM 設定的簡單擴展集︰[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox)
* **對 VM 擴展集執行個體進行負載平衡** - 如果您想要使用「循環配置資源」方法來將工作傳送到 VM 的計算叢集，您可以使用第&4; 層負載平衡規則據以設定 Azure Load Balancer。 您可以定義探查，藉由使用指定的通訊協定、間隔和要求路徑對連接埠執行 ping，以驗證您的應用程式正在執行中。 Azure [應用程式閘道](https://azure.microsoft.com/services/application-gateway/) 也支援擴展集，以及第&7; 層和更複雜的負載平衡案例。
  
   以下範例會建立執行 Apache Web 伺服器的 VM 擴展集，並使用負載平衡器來收集每個 VM 收到的負載︰[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl) (請看 Microsoft.Network/loadBalancers 資源類型以及 virtualMachineScaleSet 中的 networkProfile 和 extensionProfile )。
* **在 PaaS 叢集管理員中，將 VM 擴展集部署為計算叢集** - VM 擴展集有時會被稱為新一代的背景工作角色。 雖然這可說是有效的說明，但可能會導致擴展集功能與 PaaS v1 背景工作角色功能的混淆。 就某方面來說，VM 擴展集提供了真正的「背景工作角色」或背景工作資源，因為它們提供了不會隨平台/執行階段而改變、可自訂且整合至 Azure Resource Manager IaaS 的一般化計算資源。
  
   PaaS v1 背景工作角色雖然在平台/執行階段支援方面有所限制 (僅限 Windows 平台映像)，但也包含像是 VIP 交換、可設定的升級設定、執行階段/應用程式部署的特定設定等服務，但這些服務「尚未」成為 VM 擴展集內的可用項目，或將在其他較高階的 PaaS 服務 (如 Service Fabric) 中提供。 了解這一點之後，您可以將 VM 擴展集視為支援 PaaS 的基礎結構。 亦即 PaaS 解決方案 (如 Service Fabric) 或叢集管理員 (如 Mesos) 可以建置在 VM 擴展集之上，作為可調整的計算層。
  
   如需這個方法的範例，Azure Container Service 會根據具有容器協調器的擴展集來部署叢集：[https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)。

## <a name="vm-scale-set-performance-and-scale-guidance"></a>VM 擴展集的效能和調整指南
* 使用包含未受控磁碟的擴展集時，規劃每個儲存體帳戶不超過 20 個 VM (除非您將 overprovision 屬性設定為 "false"，在這種情況下，最多可以有 40 個)。 使用受控磁碟，不會套用每個儲存體的帳戶限制。
* 使用包含未受控磁碟的擴展集時，盡可能分散儲存體帳戶名稱的第一個字母。 [Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates/) 中的範例 VMSS 範本提供如何執行這項操作的範例。
* 如果使用包含未受控磁碟的自訂 VM，在單一儲存體帳戶中，請勿為每個 VM 擴展集規劃超過 40 個 VM。 您必須將映像預先複製到儲存體帳戶中，才能開始進行 VM 擴展集部署。 包含受控磁碟的擴展集最多可支援 100 個自訂映像 VM。 如需詳細資訊，請參閱常見問題集。
* 請勿為每個 VNET 規劃超過 4096 個 VM。
* 您可以建立的 VM 數目受限於您正在部署之區域中的核心配額。 您可能需要連絡客戶支援人員，以提高您的計算配額限制，即使您目前用於雲端服務或 IaaS v1 的核心已具有較高的上限亦然。 若要查詢您的配額，您可以執行下列 Azure CLI 命令：`azure vm list-usage`，以及下列 PowerShell 命令：`Get-AzureRmVMUsage` (如果使用 1.0 以下的 PowerShell 版本，請使用 `Get-AzureVMUsage`)。

## <a name="vm-scale-set-frequently-asked-questions"></a>VM 擴展集常見問題集
**問：** 您在 VM 擴展集內可以有多少個 VM？

**答：** 使用受控磁碟，您的擴展集可以有 0 到 1000 個以平台映像為基礎的 VM，或 0-100 個以自訂映像為基礎的 VM。 未受控磁碟 (您在其中定義自己的儲存體帳戶) 的限制是 100 個以平台映像為基礎的 VM 和最多 40 個以自訂映像為基礎的 VM (如果 *overprovision* 屬性設定為 "false"，預設值為 20)，因為未受控磁碟的自訂映像受限於單一儲存體帳戶。

**問：** 在 VM 擴展集內是否支援資料磁碟？

**答：** 是。 使用受控儲存體定義的擴展集可以定義將套用至集合中所有 VM 的連接資料磁碟機組態。 未使用受控儲存體定義的擴展集沒有連接資料磁碟機。 其他用於儲存資料的選項包括：

* Azure 檔案 (SMB 共用磁碟機)
* OS 磁碟機
* 暫存磁碟機 (本機，未受 Azure 儲存體支援)
* Azure 資料服務 (例如 Azure 資料表、Azure Blob)
* 外部資料服務 (例如遠端 DB)

**問：** 哪些 Azure 區域支援 VM 擴展集？

**答：** 所有區域都支援 VM 擴展集。

**問：** 要如何使用自訂映像建立 VM 擴展集？

**答：** 讓 vhdContainers 屬性保留空白 (或省略)，並提供映像屬性的 uri。 例如：[201-vmss-windows-customimage].(https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-customimage)


**問：** 如果我將 VMS 擴展集容量從 20 減少為 15，VM 會被移除嗎？

**答：** 虛擬機器會跨升級網域和容錯網域從擴展集平均地移除，以達到最大的可用性。 系統會先移除具有最高識別碼的 VM。

**問：** 如果我後續又將容量從 15 增加到 18 呢？

**答：** 如果您將容量增加到 18，則會建立 3 個新的 VM。 每次 VM 執行個體識別碼都會從上一個最高值遞增 (例如 20、21、22)。 VM 會分散到各個 FD 和 UD 來進行平衡。

**問：** 在一個 VM 擴展集內使用多個延伸模組時，是否可以強制執行「執行順序」？

**答：** 不是直接，但針對 customScript 擴充，您的指令碼可以等候另一個擴充完成 ([例如透過監視擴充記錄](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh))。 有關擴充排序的其他指導方針，請參閱此部落格文章︰ [Extension Sequencing in Azure VM Scale Sets (Azure VM 擴展集中的擴充排序)](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/)。

**問：** VM 擴展集是否可與 Azure 可用性設定組組搭配使用？

**答：** 是。 VM 擴展集是隱含的可用性設定組，具有 5 個 FD 和 5 個 UD。 您不需要在 virtualMachineProfile 下進行任何設定。 超過 100 個 VM 的 VM 擴展集橫跨多個「放置群組」，這相當於多個可用性設定組。 VM 的可用性設定組可以存在於與 VM 擴展集相同的 VNET 中。 常見組態是在可用性設定組中放入通常需要唯一組態的控制節點 VM，以及在擴展集中放入資料節點。

