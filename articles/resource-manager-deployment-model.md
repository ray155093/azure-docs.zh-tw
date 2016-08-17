<properties
   pageTitle="Resource Manager 與傳統部署 | Microsoft Azure"
   description="描述資源管理員部署模型與傳統 (或服務管理) 部署模型之間的差異。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/29/2016"
   ms.author="tomfitz"/>

# Azure Resource Manager vs. 傳統部署：了解資源的部署模型和狀態

本主題中，您將了解 Azure Resource Manager 和傳統部署模型、您的資源的狀態、以及您的資源相互部署的原因。Resource Manager 部署模型與傳統部署模型有重要的差異，且兩個模型之間並非完全相容。為了簡化資源的部署和管理，Microsoft 建議您針對新的資源使用資源管理員，同時，可能的話，透過資源管理員重新部署現有的資源。

如果您是 Resource Manager 新手，您可能想要先檢閱 [Azure Resource Manager 概觀](resource-group-overview.md)中定義的詞彙。

## 部署模型的歷程記錄

Azure 原本指提供傳統部署模型。在此模型中，每個資源會獨立存在；沒辦法將相關的資源群組在一起。因此，您必須手動追蹤哪些資源組成您的解決方案或應用程式，並記得要以協調的方法進行管理。若要部署解決方案，您必須透過傳統入口網站個別建立每個資源，或建立會以正確的順序部署所有資源的指令碼。若要刪除解決方案，您必須個別刪除每個資源。您無法輕易套用和更新相關資源的存取控制原則。最後，您無法將標記套用至資源，以可協助您監視資源和管理計費的詞彙標示資源。

在 2014 年，Azure 引進了加入資源群組概念的 Resource Manager。資源群組是共用共同生命週期的資源容器。「資源管理員」部署模型提供數個優點：

- 您可以以群組形式部署、管理及監視方案的所有服務，而不是個別處理這些服務。
- 您可以在整個生命週期中重複部署方案，並確信您的資源會以一致的狀態部署。
- 您可以將存取控制套用至資源群組中的所有資源，而新資源加入至資源群組時，會自動套用這些原則。
- 您可以將標籤套用至資源，以便以邏輯方式組織訂用帳戶中的所有資源。
- 您可以使用 JavaScript 物件標記法 (JSON) 來定義您的解決方案的基礎結構。JSON 檔案也稱為 Resource Manager 範本。
- 您可以定義之間的相依性，使得以正確的順序部署資源。

加入資源管理員時，所有資源會追溯地加入至預設資源群組。如果您現在透過傳統部署建立資源，資源會自動在該服務的預設資源群組內建立，即使您在部署時未指定該資源群組。不過，只是存在於資源群組內並不表示該資源已轉換成資源管理員模型。我們將在下一節中說明每個服務如何處理這兩種部署模型。

## 了解模型支援 

決定要用於您的資源的部署模型時，請留意以下三個案例︰

1. 服務支援 Resource Manager 且只提供單一類型。
2. 服務支援 Resource Manager，但提供兩個類型 - 一個用於 Resource Manager，一個用於傳統。這只適用於虛擬機器、儲存體帳戶和虛擬網路。
3. 服務不支援 Resource Manager。

若要探索服務是否支援 Resource Manager，請參閱 [Resource Manager 支援的提供者](resource-manager-supported-services.md)。

如果您想要使用的服務不支援 Resource Manager，您必須繼續使用傳統部署。

如果服務支援 Resource Manager 且**不是**虛擬機器、儲存體帳戶或虛擬網路，您可以直接使用 Resource Manager。

若為虛擬機器、儲存體帳戶和虛擬網路，如果資源是透過傳統部署建立，您必須繼續透過傳統作業來運作。如果虛擬機器、儲存體帳戶或虛擬網路是透過 Resource Manager 部署建立，您必須繼續使用 Resource Manager 作業。當您的訂用帳戶包含透過 Resource Manager 和傳統部署建立的各種資源時，此區別特別容易混淆。此資源的組合可能建立非預期的結果，因為資源不支援相同的作業。

在某些情況下，Resource Manager 命令可以擷取透過傳統部署建立之資源的相關資訊，或可以執行系統管理工作 (例如將傳統資源移至另一個資源群組)，但這些情況下，不應讓您認為該類型支援 Resource Manager 作業。例如，假設您的資源群組包含使用傳統部署所建立的虛擬機器。若您執行下列 Resource Manager PowerShell 命令：

    Get-AzureRmResource -ResourceGroupName ExampleGroup -ResourceType Microsoft.ClassicCompute/virtualMachines

它會傳回虛擬機器：
    
    Name              : ExampleClassicVM
    ResourceId        : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.ClassicCompute/virtualMachines/ExampleClassicVM
    ResourceName      : ExampleClassicVM
    ResourceType      : Microsoft.ClassicCompute/virtualMachines
    ResourceGroupName : ExampleGroup
    Location          : westus
    SubscriptionId    : {guid}

不過，**Get-AzureRmVM** 只會傳回透過 Resource Manager 部署的虛擬機器。下列命令不會傳回透過傳統部署所建立的虛擬機器。

    Get-AzureRmVM -ResourceGroupName ExampleGroup

使用虛擬機器時有一些其他重要的考量。

- 使用傳統部署模型部署的虛擬機器不能包含在使用資源管理員部署的虛擬網路中。
- 使用資源管理員部署模型部署的虛擬機器必須包含在虛擬網路中。
- 使用傳統部署模型部署的虛擬機器不一定要包含在虛擬網路中。

若要了解從不同部署模型連接虛擬網路，請參閱[將傳統 VNet 連接到新的 VNet](./virtual-network/virtual-networks-arm-asm-s2s.md)。

只有透過資源管理員建立的資源支援標記。您無法將標籤套用到傳統資源。如需有關在資源管理員中使用標記的詳細資訊，請參閱[使用標記來組織您的 Azure 資源](resource-group-using-tags.md)。

## 資源管理員特性

為了協助您了解兩個模型，讓我們來檢閱 Resource Manager 類型的特性︰

- 透過 [Azure 入口網站](https://portal.azure.com/)建立。

     ![Azure 入口網站](./media/resource-manager-deployment-model/portal.png)

     針對計算、儲存體及網路資源，您可以選擇使用資源管理員或傳統部署。選擇**資源管理員**。

     ![資源管理員部署](./media/resource-manager-deployment-model/select-resource-manager.png)

- 使用 Azure PowerShell Cmdlet 的 Resource Manager 版本建立。這些命令的格式是 *Verb-AzureRmNoun*，如下所示。

        New-AzureRmResourceGroupDeployment

- 透過適用於 REST 作業的 [Azure Resource Manager REST API](https://msdn.microsoft.com/library/azure/dn790568.aspx) 建立。

- 透過在 **arm** 模式中執行的 Azure CLI 命令建立。

        azure config mode arm
        azure group deployment create 

- 資源類型的名稱中不包括 **(傳統)**。下圖顯示的類型為**儲存體帳戶**。

    ![Web 應用程式](./media/resource-manager-deployment-model/resource-manager-type.png)

下圖所示的應用程式示範如何將透過資源管理員所部署的資源包含在單一資源群組中。

  ![Resource Manager 架構](./media/virtual-machines-azure-resource-manager-architecture/arm_arch3.png)

此外，位於資源提供者內的資源之間具有關聯性：

- 虛擬機器取決於定義於 SRP 的特定儲存體帳戶，以將其磁碟儲存於 Blob 儲存體中。
- 虛擬機器會參考定義於 NRP 的特定 NIC (必要)，以及定義於 CRP 的可用性設定組 (選擇性)。
- NIC 會參考虛擬機器指派的 IP 位址 (必要)、虛擬機器之虛擬網路的子網路 (必要)，以及網路安全性群組 (選擇性)。
- 虛擬網路內的子網路會參考網路安全性群組 (選擇性)。
- 負載平衡器執行個體會參考 IP 位址的後端集區，其中包含虛擬機器的 NIC (選擇性)，以及參考負載平衡器的公用或私人 IP 位址 (選擇性)。

## 傳統部署特性

您可能也知道傳統部署模型也就是服務管理模型。

在傳統部署模型中建立的資源都具有下列特性：

- 透過[傳統入口網站](https://manage.windowsazure.com)建立。

     ![傳統入口網站](./media/resource-manager-deployment-model/classic-portal.png)

     或者，安裝 Azure 入口網站並指定進行**傳統**部署 (範圍包括計算、儲存體及網路)

     ![傳統部署](./media/resource-manager-deployment-model/select-classic.png)

- 透過 Azure PowerShell cmdlet 的服務管理版本建立。這些命令名稱的格式是 *Verb-AzureNoun*，如下所示。

        New-AzureVM 

- 透過 REST 作業的[服務管理 REST API](https://msdn.microsoft.com/library/azure/ee460799.aspx)建立。
- 透過在 **asm** 模式中執行的 Azure CLI 命令建立。

        azure config mode asm
        azure vm create 

- 資源類型的名稱中包括 **(傳統)**。下圖顯示的類型為**儲存體帳戶 (傳統)**。

    ![傳統類型](./media/resource-manager-deployment-model/classic-type.png)

您仍然可以使用 Azure 入口網站，來管理透過傳統部署所建立的資源。

在 Azure 服務管理中，用以裝載虛擬機器的計算、儲存體或網路資源是透過下列方式來提供：

- 用來做為裝載虛擬機器 (計算) 之容器所需的雲端服務。虛擬機器是利用網路介面卡 (NIC) 和 Azure 所指派的 IP 位址自動提供。此外，雲端服務包含外部負載平衡器執行個體、共用 IP 位址及預設端點，以允許 Windows 架構虛擬機器的遠端桌面與遠端 PowerShell 流量，以及 Linux 架構虛擬機器的安全殼層 (SSH) 流量。
- 儲存虛擬機器之 VHD 所需的儲存體帳戶，包括作業系統、暫存磁碟及其他資料磁碟 (儲存體)。
- 可用來做為額外容器的選擇性虛擬網路，您可以在其中建立子網路的結構，並指定虛擬機器所在的子網路 (網路)。

以下是 Azure 服務管理的元件及其關聯性。

  ![傳統架構](./media/virtual-machines-azure-resource-manager-architecture/arm_arch1.png)

## 從傳統移轉到 Resource Manager

如果您準備好將資源從傳統部署移轉至 Resource Manager 部署，請參閱：

1. [平台支援的從傳統移轉至 Azure Resource Manager 的技術深入探討](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
2. [支援將 IaaS 資源從傳統移轉至 Azure Resource Manager 的平台](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
3. [使用 Azure PowerShell 將 IaaS 資源從傳統移轉至 Azure Resource Manager](./virtual-machines/virtual-machines-windows-ps-migration-classic-resource-manager.md)
4. [使用 Azure CLI 將 IaaS 資源從傳統移轉至 Azure Resource Manager](./virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md)

## 後續步驟

- 針對定義虛擬機器、儲存體帳戶和虛擬網路的範本，若需該範本的建立逐步解說，請參閱 [Resource Manager 範本逐步解說](resource-manager-template-walkthrough.md)。
- 若要了解 Resource Manager 範本的結構，請參閱[製作 Azure Resource Manager 範本](resource-group-authoring-templates.md)。
- 若要查看部署範本的命令，請參閱[使用 Azure 資源管理員範本部署應用程式](resource-group-template-deploy.md)。

<!---HONumber=AcomDC_0803_2016-->