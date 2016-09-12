<properties
	pageTitle="使用 Powershell 建立並上傳 VM 映像 | Microsoft Azure"
	description="了解如何使用傳統部署模型和 Azure Powershell 來建立並上傳一般化 Windows Server 映像 (VHD)。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="cynthn"/>

# 建立並上傳 Windows Server VHD 到 Azure

本文說明如何上傳您自己的一般化 VM 映像做為虛擬硬碟 (VHD)，以便使用它來建立虛擬機器。如需 Microsoft Azure 中磁碟和 VHD 的詳細資訊，請參閱[關於虛擬機器的磁碟和 VHD](virtual-machines-linux-about-disks-vhds.md)。


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]。您也可以使用 Resource Manager 模型來[擷取](virtual-machines-windows-capture-image.md)和[上傳](virtual-machines-windows-upload-image.md)虛擬機器。

## 必要條件

本文假設您具有：

- **Azure 訂用帳戶** - 如果您沒有帳戶，您可以[免費申請 Azure 帳戶](/pricing/free-trial/?WT.mc_id=A261C142F)。

- **[Microsoft Azure PowerShell](../powershell-install-configure.md)** - 您已安裝 Microsoft Azure PowerShell 模組，並設定成使用您的訂用帳戶。

- **.VHD 檔案** - 儲存在 .vhd 檔案中並連接至虛擬機器的受支援 Windows 作業系統。檢查以查看 Sysprep 是否支援在 VHD 上執行的伺服器角色。如需詳細資訊，請參閱 [Sysprep Support for Server Roles (伺服器角色的 Sysprep 支援)](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)。

> [AZURE.IMPORTANT] Microsoft Azure 不支援 VHDX 格式。您可以使用 Hyper-V 管理員或 [Convert-VHD Cmdlet](http://technet.microsoft.com/library/hh848454.aspx)，將磁碟轉換為 VHD 格式。如需詳細資料，請參閱[部落格文章](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx)。

## 步驟 1：準備 VHD 

將 VHD 上傳至 Azure 之前，必須使用 Sysprep 工具來一般化。這要準備 VHD 以做為映像。如需 Sysprep 的詳細資訊，請參閱[如何使用 Sysprep：簡介](http://technet.microsoft.com/library/bb457073.aspx)。執行 Sysprep 前，請先備份 VM。

從安裝作業系統的虛擬機器，完成下列程序：

1. 登入作業系統。

2. 以系統管理員身分開啟 [命令提示字元] 視窗。切換至 **%windir%\\system32\\sysprep** 目錄，然後執行 `sysprep.exe`。

	![開啟 [命令提示字元] 視窗](./media/virtual-machines-windows-classic-createupload-vhd/sysprep_commandprompt.png)

3.	[系統準備工具] 對話方塊隨即出現。

	![啟動 Sysprep](./media/virtual-machines-windows-classic-createupload-vhd/sysprepgeneral.png)

4.  在 [系統準備工具] 中，選取 [進入系統全新體驗 (OOBE)]，並確認已勾選 [一般化]。

5.  在 [關機選項]中選取 [關機]。

6.  按一下 [確定]。

## 步驟 2：建立儲存體帳戶和容器

您需要 Azure 中的儲存體帳戶，讓您有空間可上傳 .vhd 檔案。此步驟說明如何建立帳戶，或從現有的帳戶取得您需要的資訊。以您自己的資訊取代 &lsaquo; 括號 &rsaquo; 中的變數。

1. 登入

		Add-AzureAccount

1. 設定您的 Azure 訂用帳戶

    	Select-AzureSubscription -SubscriptionName <SubscriptionName> 

2. 建立新的儲存體帳戶。儲存體帳戶的名稱應該是唯一的且包含 3-24 個字元。名稱可以是字母和數字的任意組合。您也必須指定一個位置，例如「美國東部」。
    	
		New-AzureStorageAccount –StorageAccountName <StorageAccountName> -Location <Location>

3. 將新儲存體帳戶設定為預設值。
    	
		Set-AzureSubscription -CurrentStorageAccountName <StorageAccountName> -SubscriptionName <SubscriptionName>

4. 建立新的容器。

    	New-AzureStorageContainer -Name <ContainerName> -Permission Off

 

## 步驟 3：上傳 .vhd 檔案

使用 [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx) 來上傳 VHD。

從您在上一個步驟中使用的 Azure PowerShell 視窗中，輸入下列命令並以您自己的資訊取代 &lsaquo; 括號 &rsaquo; 中的變數。

		Add-AzureVhd -Destination "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -LocalFilePath <LocalPathtoVHDFile>


## 步驟 4：將映像新增到您的自訂映像清單

使用 [Add-AzureVMImage])(https://msdn.microsoft.com/library/mt589167.aspx) Cmdlet，將映像新增到您的自訂映像清單。

		Add-AzureVMImage -ImageName <ImageName> -MediaLocation "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -OS "Windows"


## 後續步驟

您現在可以使用上傳的映像來[建立自訂的 VM](virtual-machines-windows-classic-createportal.md)。

<!---HONumber=AcomDC_0831_2016-->