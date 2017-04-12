---
title: "在虛擬機器擴展集上部署應用程式| Microsoft Docs"
description: "在虛擬機器擴展集上部署應用程式"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: guybo
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: f39840ab2fb31775c9703799393d8c386a8451ee
ms.lasthandoff: 03/31/2017


---
# <a name="deploy-an-app-on-virtual-machine-scale-sets"></a>在虛擬機器擴展集上部署應用程式
在「VM 擴展集」上執行的應用程式通常是以三種方式的其中一種來進行部署︰

* 在部署階段，於平台映像上安裝新軟體
* 建立自訂 VM 映像，在單一 VHD 中包含 OS 和應用程式
* 部署平台或自訂映像做為容器主機，並部署您的應用程式做為一或多個容器

## <a name="install-new-software-on-a-platform-image-at-deployment-time"></a>在部署階段，於平台映像上安裝新軟體
此內容中的平台映像是來自 Azure Marketplace 的作業系統映像，例如 Ubuntu 16.04、Windows Server 2012 R2 等。

您可以使用 [VM 擴充功能](../virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)在平台映像上安裝新軟體。 VM 擴充功能是在部署 VM 時執行的軟體。 您可以使用自訂指令碼擴充功能，在部署階段執行您想要的任何程式碼。 [這裡 (英文)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) 是 Azure Resource Manager 範本範例，此範例使用 [Azure 期望狀態組態 (DSC) 延伸模組](virtual-machine-scale-sets-dsc.md)來安裝 IIS 以及已與 Azure 自動調整規模整合的 .NET MVC 應用程式。

此方法的優點是在您應用程式程式碼與 OS 之間有某種程度的分隔，而可以個別維護您的應用程式。 當然，這也意謂著有較多的移動組件，而且如果指令碼需要下載和設定的項目有很多，VM 部署時間也可能較長。

>[!NOTE]
>如果您在自訂指令碼擴充功能命令中傳遞機密資訊 (例如密碼)，請務必在自訂指令碼擴充功能的 `protectedSettings` 屬性 (而不是 `settings` 屬性) 中指定 `commandToExecute`。

## <a name="create-a-custom-vm-image-that-includes-both-the-os-and-the-application-in-a-single-vhd"></a>建立自訂 VM 映像，在單一 VHD 中包含 OS 和應用程式 
這裡的擴展集是由一組 VM 所組成，這些 VM 是從您建立的映像複製且您必須維護的 VM。 此方法不需要在 VM 部署階段進行任何額外的設定。 不過，在 `2016-03-30` 版 (及更舊版本) 的「VM 擴展集」中，擴展集內 VM 的 OS 磁碟受限於單一儲存體帳戶。 因此，一個擴展集內最多可以有 40 個 VM，而不像平台映像的每個擴展集限制為 100 個 VM。 如需詳細資訊，請參閱 [擴展集設計概觀](virtual-machine-scale-sets-design-overview.md)。

>[!NOTE]
>VM 擴展集 API 版本 `2016-04-30-preview` 支援使用 Azure 受控磁碟做為作業系統磁碟和任何額外的資料磁碟。 如需詳細資訊，請參閱[受控磁碟概觀](../storage/storage-managed-disks-overview.md)和[使用附加的資料磁碟](virtual-machine-scale-sets-attached-disks.md)。 

## <a name="deploy-a-platform-or-a-custom-image-as-a-container-host-and-your-app-as-one-or-more-containers"></a>部署平台或自訂映像做為容器主機，並部署您的應用程式做為一或多個容器
一個平台或自訂映像基本上就是一部容器主機，因此，您可以安裝應用程式做為一或多個容器。  您可以使用 Orchestrator 或組態管理工具來管理應用程式容器。 此方法的好處在於您已將雲端基礎結構從應用程式層抽離出來，而可以個別維護它們。

## <a name="what-happens-when-a-vm-scale-set-scales-out"></a>當 VM 擴展集相應放大時，會發生什麼情況？
當您透過增加容量將一或多個 VM 新增到擴展集時 (不論是以手動方式還是透過自動調整)，都會自動安裝應用程式。 例如，如果擴展集已有定義的擴充功能，則每次建立新 VM 時，這些擴充功能都會在新 VM 上執行。 如果擴展集是以自訂映像為基礎，則所有新 VM 都是來源自訂映像的複本。 如果擴展集 VM 是容器主機，則您可以讓啟動程式碼載入「自訂指令碼擴充功能」中的容器，或是擴充功能可以安裝會向叢集 Orchestrator (例如 Azure Container Service) 註冊的代理程式。

## <a name="how-do-you-manage-application-updates-in-vm-scale-sets"></a>要如何管理 VM 擴展集內的應用程式更新？
針對「VM 擴展集」內的應用程式更新，有三個衍生自前述三種應用程式部署方法的主要方法：

* 使用 VM 擴充功能來進行更新。 每次建立新 VM、重新安裝現有 VM 的映像，或更新 VM 擴充功能時，都會執行為「VM 擴展集」定義的所有 VM 擴充功能。 如果您需要更新應用程式，透過擴充功能直接更新應用程式是可行的方法，您只要更新擴充功能定義即可。 若要這麼做，有一個簡單的方式，就是將 fileUris 變更為指向新的軟體。

* 不可變的自訂映像方法。 當您將應用程式 (或應用程式元件) 製作成 VM 映像時，您可以將焦點放在建置可靠的管線來將映像的建置、測試及部署自動化。 您可以設計架構來協助將分段擴展集快速切換到生產環境。 此方法的其中一個好例子就是 [Azure Spinnaker 驅動程式工作](https://github.com/spinnaker/deck/tree/master/app/scripts/modules/azure) - [http://www.spinnaker.io/](http://www.spinnaker.io/)。

Packer 和 Terraform 也支援 Azure Resource Manager，因此您也可以「以程式碼方式」定義您的映像並在 Azure 中建置它們，然後在您的擴展集內使用 VHD。 不過，這麼做對 Marketplace 映像會造成問題，其中擴充功能/自訂指令碼會變得更加重要，因為您不是直接從 Marketplace 操縱位元。

* 更新容器。 將應用程式生命週期管理提取至高於雲端基礎結構的層級 (例如透過封裝應用程式的方式)，以及將應用程式元件提取至容器中，並透過容器 Orchestrator 和組態管理員 (例如 Chef/Puppet) 管理這些容器。

然後，擴展集 VM 就會變成容器的穩定基底，而只需要偶爾進行安全性和 OS 相關的更新。 如前所述，Azure Container Service 即是一個採用此方法並以其為中心建置服務的好例子。

## <a name="how-do-you-roll-out-an-os-update-across-update-domains"></a>如何在各個更新網域推出 OS 更新？
假設您想要在更新 OS 映像的同時，又讓「VM 擴展集」持續執行。 若要這麼做，有一個方法，就是一次更新一個 VM 的 VM 映像。 您可以使用 PowerShell 或 Azure CLI 來達到此目的。 有個別的命令可在個別 VM 上更新「VM 擴展集」模型 (其組態的定義方式)，以及發出「手動升級」呼叫。 [升級虛擬機器擴展集](./virtual-machine-scale-sets-upgrade-scale-set.md) Azure 文件也提供進一步的資訊，說明 VM 擴展集內執行 OS 升級的可用選項。


