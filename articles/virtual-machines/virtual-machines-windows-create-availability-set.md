<properties
	pageTitle="建立 VM 可用性設定組 | Microsoft Azure"
	description="了解如何針對您使用 Azure 入口網站的虛擬機器或使用 Resource Manager 部署模型的 PowerShell 建立可用性設定組。"
	keywords="可用性設定組"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>
<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/27/2016"
	ms.author="cynthn"/>


# 建立可用性設定組 

使用入口網站時，如果您希望您的 VM 是可用性設定組的一部分，您必須先建立可用性設定組。

如需建立和使用可用性設定組的詳細資訊，請參閱[管理虛擬機器可用性](virtual-machines-windows-manage-availability.md)。


## 建立 VM 之前，請使用入口網站建立可用性設定組

1. 在 [中樞] 功能表中，按一下 [瀏覽] 並選取 [可用性設定組]。

2. 在 [可用性設定組] 刀鋒視窗上，按一下 [新增]。

	![顯示用來建立新的可用性設定組的 [新增] 按鈕的螢幕擷取畫面。](./media/virtual-machines-windows-create-availability-set/add-availability-set.png)

3. 在 [建立可用性設定組] 刀鋒視窗上，完成您的設定組的資訊。

	![顯示您必須輸入以建立可用性設定組之資訊的螢幕擷取畫面。](./media/virtual-machines-windows-create-availability-set/create-availability-set.png)

	- **名稱** - 名稱應該是 1-80 個字元的數字、字母、句號、底線和連字號所組成。第一個字元必須是字母或數字。最後一個字元必須是字母、數字或底線。
	- **容錯網域** - 容錯網域定義共用通用電源和網路交換器的虛擬機器群組。根據預設，VM 由最多三個容錯網域隔開，且可以於 1 到 3 之間變更。
	- **更新網域** - 預設會指派五個更新網域，且可以設定於 1 到 20 之間。更新網域表示虛擬機器群組和可同時重新啟動的基礎實體硬體。例如，如果我們指定五個更新網域，當一個可用性設定組中設定了超過五部虛擬機器，會將第六部虛擬機器放入與第一部虛擬機器相同的更新網域中，而第七部則會放入與第二部相同的更新網域中，以此類推。重新啟動時可能不會按照順序重新啟動，但是一次僅會重新啟動一個更新網域。
	- **訂用帳戶** - 如果您有多個訂用帳戶，請選取要使用的訂用帳戶。
	- **資源群組** - 按一下箭號並從下拉式清單中選取一個資源群組，以選取現有的資源群組。您也可以輸入名稱，來建立新的資源群組。名稱可以包含任何下列字元：字母、數字、句號、連字號、底線和左或右括弧。名稱不能以句號結束。可用性群組中的所有 VM 必須建立在與可用性設定組相同的資源群組中。
	- **位置** - 從下拉式清單中選取 [位置]。

4. 當您完成輸入資訊時，請按一下 [建立]。一旦建立可用性群組，您就可以在重新整理入口網站後於清單中看到它。

## 使用入口網站同時建立虛擬機器和可用性設定組

如果您使用入口網站建立新的 VM，您也可以在您於設定組中建立第一個 VM 的同時，也針對 VM 建立一個新的可用性設定組。

![顯示在您建立 VM 時，同時建立新的可用性設定組之程序的螢幕擷取畫面。](./media/virtual-machines-windows-create-availability-set/new-vm-avail-set.png)


## 將新的 VM 新增至現有的可用性設定組

針對設定組中您所建立的每一個其他 VM，請確定您是在相同的**資源群組**中建立，然後選取步驟 3 中現有的可用性設定組。

![顯示如何選取現有的可用性設定組以用於您的 VM 的螢幕擷取畫面。](./media/virtual-machines-windows-create-availability-set/add-vm-to-set.png)



## 使用 PowerShell 建立可用性設定組

這個範例會在**美國西部**位置的 **RMResGroup** 資源群組中建立一個可用性設定組。這需要在您建立設定組中的第一個 VM 之前完成。

	New-AzureRmAvailabilitySet -ResourceGroupName "RMResGroup" -Name "AvailabilitySet03" -Location "West US"
	
如需詳細資訊，請參閱 [New-AzureRmAvailabilitySet](https://msdn.microsoft.com/library/mt619453.aspx)。


## 疑難排解

- 當您建立 VM 時，如果您想要的可用性設定組不在入口網站的下拉式清單中，您有可能將它建立在另一個資源群組中。如果您不清楚您的可用性設定組的資源群組， 請移至 [中樞] 功能表，並按一下 [瀏覽] > [可用性設定組]，以查看可用性設定組清單及其所屬的資源群組。


## 後續步驟

透過新增額外[資料磁碟](virtual-machines-windows-attach-disk-portal.md)，將額外的存放裝置新增到您的 VM。

<!---HONumber=AcomDC_0928_2016-->