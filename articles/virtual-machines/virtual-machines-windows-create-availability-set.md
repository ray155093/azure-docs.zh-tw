---
title: "在 Azure 中建立 VM 可用性設定組 | Microsoft Docs"
description: "了解如何針對使用 Azure PowerShell 或入口網站的虛擬機器，在 Resource Manager 部署模型中建立受控可用性設定組或非受控可用性設定組。"
keywords: "可用性設定組"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a3db8659-ace8-4e78-8b8c-1e75c04c042c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 2f4ea765df71ed9aac7e8657e6be3706b635407e
ms.lasthandoff: 03/06/2017

---
# <a name="increase-vm-availability-by-creating-an-azure-availability-set"></a>建立 Azure 可用性設定組以增加 VM 可用性 
可用性設定組可為您的應用程式提供備援。 建議您在可用性設定組中，將兩部以上的虛擬機器組成群組。 這項組態可以確保在規劃或未規劃的維護事件發生期間，至少有一部虛擬機器可以使用，且符合 99.95% 的 Azure SLA。 如需相關資訊，請參閱 [虛擬機器的 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)。

> [!IMPORTANT]
> VM 必須建立於與可用性設定組相同的資源群組中。
> 

如果您希望 VM 成為可用性設定組的一部分，您必須在建立第一個 VM 時先建立可用性設定組。 如果您的 VM 將使用受控磁碟，則必須將此可用性設定組建立為受控可用性設定組。

如需建立和使用可用性設定組的詳細資訊，請參閱 [管理虛擬機器可用性](virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

## <a name="use-the-portal-to-create-an-availability-set-before-creating-your-vms"></a>建立 VM 之前，請使用入口網站建立可用性設定組
1. 在 [中樞] 功能表中，按一下 [瀏覽]，並選取 [可用性設定組]。
2. 在 [可用性設定組] 刀鋒視窗上，按一下 [新增]。
   
    ![顯示用來建立新的可用性設定組的 [新增] 按鈕的螢幕擷取畫面。](./media/virtual-machines-windows-create-availability-set/add-availability-set.png)
3. 在 [建立可用性設定組]  刀鋒視窗上，完成您的設定組的資訊。
   
    ![顯示您必須輸入以建立可用性設定組之資訊的螢幕擷取畫面。](./media/virtual-machines-windows-create-availability-set/create-availability-set.png)
   
   * **名稱** - 名稱應該是 1-80 個字元的數字、字母、句號、底線和連字號所組成。 第一個字元必須是字母或數字。 最後一個字元必須是字母、數字或底線。
   * **容錯網域** - 容錯網域定義共用通用電源和網路交換器的虛擬機器群組。 根據預設，VM 由最多三個容錯網域隔開，且可以於 1 到 3 之間變更。
   * **更新網域** - 預設會指派五個更新網域，且可以設定於 1 到 20 之間。 更新網域表示虛擬機器群組和可同時重新啟動的基礎實體硬體。 例如，如果我們指定五個更新網域，當一個可用性設定組中設定了超過五部虛擬機器，會將第六部虛擬機器放入與第一部虛擬機器相同的更新網域中，而第七部則會放入與第二部相同的更新網域中，以此類推。 重新啟動時可能不會按照順序重新啟動，但是一次僅會重新啟動一個更新網域。
   * **訂用帳戶** - 如果您有多個訂用帳戶，請選取要使用的訂用帳戶。
   * **資源群組** - 按一下箭號並從下拉式清單中選取一個資源群組，以選取現有的資源群組。 您也可以輸入名稱，來建立新的資源群組。 名稱可以包含任何下列字元：字母、數字、句號、連字號、底線和左或右括弧。 名稱不能以句號結束。 可用性群組中的所有 VM 必須建立在與可用性設定組相同的資源群組中。
   * **位置** - 從下拉式清單中選取 [位置]。
   * **受控** - 選取 [是] 來建立受控可用性設定組，以搭配使用受控磁碟進行儲存的 VM 使用。 如果將位於可用性設定組中的 VM 使用儲存體帳戶中的非受控磁碟，請選取 [否]。
   
4. 當您完成輸入資訊時，請按一下 [建立] 。 

## <a name="use-the-portal-to-create-a-virtual-machine-and-an-availability-set-at-the-same-time"></a>使用入口網站同時建立虛擬機器和可用性設定組
如果您使用入口網站建立新的 VM，您也可以在您於設定組中建立第一個 VM 的同時，也針對 VM 建立一個新的可用性設定組。 如果您選擇將受控磁碟使用於您的 VM，就會建立受控可用性設定組。

![顯示在您建立 VM 時，同時建立新的可用性設定組之程序的螢幕擷取畫面。](./media/virtual-machines-windows-create-availability-set/new-vm-avail-set.png)

## <a name="add-a-new-vm-to-an-existing-availability-set-in-the-portal"></a>在入口網站中將新的 VM 新增至現有的可用性設定組
針對設定組中您所建立的每一個其他 VM，請確定您是在相同的 **資源群組** 中建立，然後選取步驟 3 中現有的可用性設定組。 

![顯示如何選取現有的可用性設定組以用於您的 VM 的螢幕擷取畫面。](./media/virtual-machines-windows-create-availability-set/add-vm-to-set.png)

## <a name="use-powershell-to-create-an-availability-set"></a>使用 PowerShell 建立可用性設定組
這個範例會在位於 [美國西部] 的 **myResourceGroup** 資源群組中建立名為 **myAvailabilitySet** 的可用性設定組。 這需要在您建立設定組中的第一個 VM 之前完成。

開始之前，請確定您擁有最新版的 AzureRM.Compute PowerShell 模組。 執行下列命令來安裝它。

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
如需詳細資訊，請參閱 [Azure PowerShell 版本控制](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning)。


如果您將受控磁碟使用於您的 VM，請輸入︰

```powershell
    New-AzureRmAvailabilitySet -ResourceGroupName "myResourceGroup" '
    -Name "myAvailabilitySet" -Location "West US" -managed
```

如果您將自己的儲存體帳戶使用於您的 VM，請輸入︰

```powershell
    New-AzureRmAvailabilitySet -ResourceGroupName "myResourceGroup" '
    -Name "myAvailabilitySet" -Location "West US" 
```

如需詳細資訊，請參閱 [New-AzureRmAvailabilitySet](/powershell/new-azurermavailabilityset)。

## <a name="troubleshooting"></a>疑難排解
* 當您建立 VM 時，如果您想要的可用性設定組不在入口網站的下拉式清單中，您有可能將它建立在另一個資源群組中。 如果您不清楚您的可用性設定組的資源群組， 請移至 [中樞] 功能表，並按一下 [瀏覽] > [可用性設定組]，以查看可用性設定組清單及其所屬的資源群組。

## <a name="next-steps"></a>後續步驟
透過新增額外 [資料磁碟](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，將額外的存放裝置新增到您的 VM。


