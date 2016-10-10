<properties
	pageTitle="將資料磁碟與 Windows VM 中斷連結 | Microsoft Azure"
	description="了解如何使用 Resource Manger 部署模型將資料磁碟與 Azure 中的虛擬機器中斷連結。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/27/2016"
	ms.author="cynthn"/>



# 如何從 Windows 虛擬機器卸離資料磁碟


當不再需要某個連接至虛擬機器的資料磁碟時，卸離此資料磁碟很簡單。這會將磁碟從虛擬機器中卸離，但這不會將它從儲存體中移除。

> [AZURE.WARNING] 將磁碟中斷連結時，並不會自動將它刪除。如果您已訂閱「進階」儲存體，您將會繼續因該磁碟而導致產生儲存體費用。如需詳細資訊，請參閱[使用進階儲存體時的價格和計費](../storage/storage-premium-storage.md#pricing-and-billing)。

如果您想要再次使用磁碟上現有的資料，您可以將磁碟重新連接至相同或其他虛擬機器。


## 使用入口網站來中斷資料磁碟連結

1. 在入口網站中樞中，選取 [虛擬機器]。

2. 選取含有您想要中斷連結之資料磁碟的虛擬機器，然後按一下 [所有設定]。

3. 在 [設定] 刀鋒視窗中，選取 [磁碟]。

4. 在 [磁碟] 刀鋒視窗中，選取您想要中斷連結的資料磁碟。

5. 在該資料磁碟的刀鋒視窗中，按一下 [中斷連結]。


	![顯示 [中斷連結] 按鈕的螢幕擷取畫面。](./media/virtual-machines-windows-detach-disk/detach-disk.png)

磁碟仍留在儲存體中，但不再連接至虛擬機器。


## 使用 PowerShell 來中斷資料磁碟連結

在此範例中，第一個命令會使用 Get-AzureRmVM Cmdlet 來取得 **RG11** 資源群組中名為 **MyVM07** 的虛擬機器。此命令會將虛擬機器儲存在 **$VirtualMachine** 變數中。

第二個命令會將名為 DataDisk3 的資料磁碟從虛擬機器中移除。

最後一個命令會更新虛擬機器的狀態，以完成移除資料磁碟的程序。

	$VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" 
	Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
	Update-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" -VM $VirtualMachine


如需詳細資訊，請參閱 [Remove-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx)

## 後續步驟

如果您想要重複使用該資料磁碟，只要[將它連結至另一個 VM](virtual-machines-windows-attach-disk-portal.md) 即可。

<!---HONumber=AcomDC_0928_2016-->