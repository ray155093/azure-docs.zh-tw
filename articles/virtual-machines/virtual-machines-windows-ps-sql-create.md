<properties
    pageTitle="以 Azure PowerShell 建立 SQL Server 虛擬機器 (Resource Manager) | Microsoft Azure"
    description="提供使用 SQL Server 虛擬機器資源庫映像建立 Azure VM 的步驟和 PowerShell 指令碼。"
	services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="04/20/2016"
	ms.author="jroth"/>

# 使用 Azure PowerShell 佈建 SQL Server 虛擬機器 (Resource Manager)

> [AZURE.SELECTOR]
- [入口網站](virtual-machines-windows-portal-sql-server-provision.md)
- [PowerShell](virtual-machines-windows-ps-sql-create.md)

## 概觀

本教學課程示範如何使用 Azure PowerShell Cmdlet，建立採用 **Azure Resource Manager** 部署模型的單一 Azure 虛擬機器。在本教學課程中，我們將從 SQL 資源庫中的映像建立使用單一磁碟機的單一虛擬機器。我們將為虛擬機器所要使用的儲存體、網路和計算資源，建立新的提供者。如果上述任何資源有現有的提供者，您可以改用這些提供者。

如果您需要本主題的傳統版本，請參閱[使用 Azure PowerShell 佈建 SQL Server 虛擬機器 (傳統)](virtual-machines-windows-classic-ps-sql-create.md)。

## 必要條件

本教學課程中，您將需要：

- 在開始之前，您需要有 Azure 帳戶和訂用帳戶。如果您沒有帳戶，請註冊[免費試用](https://azure.microsoft.com/pricing/free-trial/)。
- [Azure PowerShell)](../powershell-install-configure.md)，最低版本 1.4.0 或更新版本 (本教學課程是以 1.5.0 版撰寫)。
    - 若要擷取您的版本，請輸入 **Get-Module Azure -ListAvailable**。

## 設定您的訂用帳戶

開啟 Windows PowerShell 並執行下列 Cmdlet 來建立您的 Azure 帳戶存取權限。您會看到要輸入認證的登入畫面。請使用與登入 Azure 入口網站相同的電子郵件和密碼。

	Add-AzureRmAccount

成功登入後，您將會在畫面上看到一些資訊，包括您用於登入的 SubscriptionId。這是本教學課程中將於其中建立資源的訂用帳戶，除非您將它變更為不同的訂用帳戶。如果您有多個 SubscriptionId，請執行下列 Cmdlet 以傳回所有 SubscriptionId 的清單：

	Get-AzureRmSubscription

若要變更為另一個 SubscriptionID，請使用您所需的 SubscriptionId 執行下列 Cmdlet。

	Select-AzureRmSubscription -SubscriptionId xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

## 定義映像變數

為了簡化可用性和了解本教學課程中完整的指令碼，我們首先會定義數個變數。視需要變更參數值，但在修改所提供的值時，請注意與名稱長度和特殊字元相關的命名限制。

### 位置和資源群組
使用兩個變數來定義資料區域，以及您將在其中為虛擬機器建立其他資源的資源群組。

視需要修改並執行下列 Cmdlet 來初始化這些變數。

	$Location = "SouthCentralUS"
    $ResourceGroupName = "sqlvm1"

### 儲存體屬性

使用下列變數來定義儲存體帳戶和虛擬機器所要使用的儲存體類型。

視需要修改並執行下列 Cmdlet 來初始化這些變數。請注意，在此範例中，我們使用[進階儲存體](../storage/storage-premium-storage.md)，這是針對生產環境工作負載建議使用的儲存體。如需有關本指南及其他建議的詳細資料，請參閱 [Azure 虛擬機器中的 SQL Server 效能最佳做法](virtual-machines-windows-sql-performance.md)。

    $StorageName = $ResourceGroupName + "storage"
    $StorageSku = "Premium_LRS"

### 網路屬性

使用下列變數來定義網路介面、TCP/IP 配置方法、虛擬網路名稱、虛擬子網路名稱、虛擬網路的 IP 位址範圍、子網路的 IP 位址範圍，以及虛擬機器中的網路所要使用的公用網域名稱標籤。

視需要修改並執行下列 Cmdlet 來初始化這些變數。

    $InterfaceName = $ResourceGroupName + "ServerInterface"
    $TCPIPAllocationMethod = "Dynamic"
    $VNetName = $ResourceGroupName + "VNet"
    $SubnetName = "Default"
    $VNetAddressPrefix = "10.0.0.0/16"
    $VNetSubnetAddressPrefix = "10.0.0.0/24"
    $DomainName = "sqlvm1"   

### 虛擬機器屬性

使用下列變數來定義虛擬機器名稱、電腦名稱、虛擬機器大小和虛擬機器的作業系統磁碟名稱。

視需要修改並執行下列 Cmdlet 來初始化這些變數。

    $VMName = $ResourceGroupName + "VM"
    $ComputerName = $ResourceGroupName + "Server"
    $VMSize = "Standard_DS13"
    $OSDiskName = $VMName + "OSDisk"

### 映像屬性

使用下列變數來定義要用來虛擬機器的映像。在此範例中，會使用 SQL Server 2016 RC3 評估映像。

視需要修改並執行下列 Cmdlet 來初始化這些變數。

    $PublisherName = "MicrosoftSQLServer"
    $OfferName = "SQL2016RC3-WS2012R2"
    $Sku = "Evaluation"
    $Version = "latest"

請注意，您可以使用 Get-AzureRmVMImageOffer 命令來取得 SQL Server 映像提供項目的完整清單︰

    Get-AzureRmVMImageOffer -Location 'East US' -Publisher 'MicrosoftSQLServer'

而您可以使用 Get-AzureRmVMImageSku 來查看提供項目可用的 Sku。下列命令會顯示 **SQL2014SP1-WS2012R2** 提供項目可用的所有 Sku。

    Get-AzureRmVMImageSku -Location 'East US' -Publisher 'MicrosoftSQLServer' -Offer 'SQL2014SP1-WS2012R2' | Select Skus

## 建立資源群組

在 Resource Manager 部署模型中，您所建立的第一個物件是資源群組。我們將使用 [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt678985.aspx) Cmdlet，以您先前初始化的變數所定義的資源群組名稱和位置，來建立 Azure 資源群組及其資源。

執行下列 Cmdlet 來建立新的資源群組。

    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

## 建立儲存體帳戶

虛擬機器需要作業系統磁碟和 SQL Server 資料和記錄檔的儲存體資源。為了簡單起見，我們將針對兩者建立單一磁碟。您可以在之後使用 [Add-Azure Disk](https://msdn.microsoft.com/library/azure/dn495252.aspx) Cmdlet 來連結額外的磁碟，以便將您的 SQL Server 資料和記錄檔放在專用的磁碟上。我們將使用 [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) Cmdlet 在新的資源群組中建立標準儲存體帳戶，此帳戶會使用以您先前初始化的變數定義的儲存體帳戶名稱、儲存體 Sku 名稱及位置。

執行下列 Cmdlet 來建立新的儲存體帳戶。

    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

## 建立網路資源

虛擬機器需要數個網路資源才能連接網路。

- 每部虛擬機器都需要虛擬網路。
- 虛擬網路必須定義至少一個子網路。
- 網路介面必須以公用或私人 IP 位址定義。

### 建立虛擬網路子網路組態

我們首先會建立虛擬網路的子網路組態。在本教學課程中，我們將使用 [New-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) Cmdlet 來建立預設子網路。我們將使用您先前初始化的變數所定義的子網路名稱和位址首碼，建立虛擬網路子網路組態。

>[AZURE.NOTE] 您可以使用這個 Cmdlet 來定義虛擬網路子網路組態的其他屬性，但這已超出本教學課程的範圍。

執行下列 Cmdlet 來建立虛擬子網路組態。

    $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix

### 建立虛擬網路

接著，我們將使用 [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) Cmdlet 來建立虛擬網路。我們將使用您先前初始化的變數所定義的名稱、位置和位址首碼，以及使用您在前一個步驟中定義的子網路組態，在新的資源群組中建立虛擬網路。

執行下列 Cmdlet 來建立虛擬網路。

    $VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig

### 建立公用 IP 位址

我們現已定義虛擬網路，我們還必須設定 IP 位址才能連接至虛擬機器。在本教學課程中，我們將使用動態 IP 位址來建立公用 IP 位址，以支援網際網路連線。我們將使用 [New-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx) Cmdlet 在先前建立的資源群組中建立公用 IP 位址，此位址會使用以您先前初始化的變數定義的名稱、位置、配置方法及 DNS 網域名稱標籤。

>[AZURE.NOTE] 您可以使用這個 Cmdlet 來定義公用 IP 位址的其他屬性，但這已超出本初期教學課程的範圍。您也可以建立私人位址或具有靜態位址的位址，但這也已超出本教學課程的範圍。

執行下列 Cmdlet 來公用 IP 位址。

    $PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName

### 建立網路介面

我們現在已準備好建立虛擬機器將要使用的網路介面。我們將使用 [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) Cmdlet 在稍早建立的資源群組中建立網路介面，此網路介面會使用先前定義的名稱、位置、子網路及公用 IP 位址。

執行下列 Cmdlet 來建立網路介面。

    $Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

## 設定 VM 物件

我們現已定義儲存體和網路資源，我們便準備好定義虛擬機器的計算資源。在本教學課程中，我們將指定虛擬機器大小和各種作業系統屬性、指定我們先前建立的網路介面、定義 Blob 儲存體，然後指定作業系統磁碟。

### 建立 VM 物件

我們首先會指定虛擬機器大小。在本教學課程諸ㄥ，我們會指定 DS13。我們將使用 [New-AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx) Cmdlet 來建立可設定的虛擬機器物件，此物件會使用以您先前初始化的變數定義的名稱與大小。

執行下列 Cmdlet 來建立虛擬機器物件。

    $VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize

### 建立認證物件，以保留本機系統管理員認證的名稱和密碼

我們必須先提供本機系統管理員帳戶的認證做為安全字串，才可以設定虛擬機器的作業系統屬性。為了達成此目的，我們將使用 [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) Cmdlet。

執行下列 Cmdlet，並在 Windows PowerShell 認證要求視窗中，輸入要使用於 Windows 虛擬機器中本機系統管理員帳戶的名稱和密碼。

    $Credential = Get-Credential -Message "Type the name and password of the local administrator account."

### 設定虛擬機器的作業系統屬性

我們現在已準備好設定虛擬機器的作業系統屬性。我們將使用 [Set-AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx) Cmdlet 將作業系統的類型設定為 Windows、要求安裝[虛擬機器代理程式](virtual-machines-windows-classic-agents-and-extensions.md)、指定此 Cmdlet 啟用自動更新，以及使用您先前初始化的變數來設定虛擬機器名稱、電腦名稱和認證。

執行下列 Cmdlet 來設定虛擬機器的作業系統屬性。

    $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate

### 將網路介面新增至虛擬機器

接下來，我們會將先前建立的網路介面新增至虛擬機器。我們將使用 [Add-AzureRmVMNetworkInterface](https://msdn.microsoft.com/library/mt619351.aspx) Cmdlet 以您稍早定義的網路介面變數來新增網路介面。

執行下列 Cmdlet 來設定虛擬機器的網路介面。

    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id

### 設定虛擬機器所要使用之磁碟的 Blob 儲存體位置

接下來，我們將使用您稍早定義的變數，設定虛擬機器所要使用之磁碟的 Blob 儲存體位置。

執行下列 Cmdlet 來設定 Blob 儲存體位置。

    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"

### 設定虛擬機器的作業系統磁碟屬性

接下來，我們將設定虛擬機器的作業系統磁碟屬性。我們將使用 [Set-AzureRmVMOSDisk](https://msdn.microsoft.com/library/mt603746.aspx) Cmdlet 來指定虛擬機器的作業系統會來自映像、將快取設定成唯讀 (因為 SQL Server 要安裝在相同的磁碟上)，以及定義使用我們稍早定義之變數來定義的虛擬機器名稱和作業系統磁碟。

執行下列 Cmdlet 來設定虛擬機器的作業系統磁碟屬性。

    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

### 指定虛擬機器的平台映像

最後一個設定步驟是指定虛擬機器的平台映像。在本教學課程中，我們會使用最新的 SQL Server 2016 CTP 映像。我們將使用 [Set-AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx) Cmdlet 以使用如您稍早定義的變數所定義的這個映像。

執行下列 Cmdlet 來設定虛擬機器的平台映像。

    $VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

## 建立 SQL VM

您現在已完成設定步驟，即可開始建立虛擬機器。我們將使用 [New-AzureRmVM](https://msdn.microsoft.com/library/mt603754.aspx) Cmdlet 以我們已定義的變數來建立虛擬機器。

執行下列 Cmdlet 來建立虛擬機器。

    New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

虛擬機器已建立。請注意，系統會建立標準儲存體帳戶以供開機診斷，因為虛擬機器磁碟的指定儲存體帳戶是進階儲存體帳戶。

您現在可以在「Azure 入口網站」中檢視此機器，以查看[其公用 IP 位址及其完整的網域名稱](virtual-machines-windows-portal-sql-server-provision.md#Connect)。

## 範例指令碼

下列指令碼包含本教學課程的完整 PowerShell 指令碼。此指令碼假設您已設定要與 **Add-AzureRmAccount** 和 **Select-AzureRmSubscription** 命令搭配使用的 Azure 訂用帳戶。


    # Variables
    ## Global
    $Location = "SouthCentralUS"
    $ResourceGroupName = "sqlvm1"
    ## Storage
    $StorageName = $ResourceGroupName + "storage"
    $StorageSku = "Premium_LRS"

    ## Network
    $InterfaceName = $ResourceGroupName + "ServerInterface"
    $VNetName = $ResourceGroupName + "VNet"
    $SubnetName = "Default"
    $VNetAddressPrefix = "10.0.0.0/16"
    $VNetSubnetAddressPrefix = "10.0.0.0/24"
    $TCPIPAllocationMethod = "Dynamic"
    $DomainName = "sqlvm1"

    ##Compute
    $VMName = $ResourceGroupName + "VM"
    $ComputerName = $ResourceGroupName + "Server"
    $VMSize = "Standard_DS13"
    $OSDiskName = $VMName + "OSDisk"

    ##Image
    $PublisherName = "MicrosoftSQLServer"
    $OfferName = "SQL2016RC3-WS2012R2"
    $Sku = "Evaluation"
    $Version = "latest"

    # Resource Group
    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

    # Storage
    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

    # Network
    $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
    $VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
    $PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
    $Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

    # Compute
    $VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    $Credential = Get-Credential -Message "Type the name and password of the local administrator account."
    $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

    # Image
    $VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

    ## Create the VM in Azure
    New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

## 後續步驟
建立虛擬機器之後，您即可準備使用 RDP 和設定連線能力來連接到虛擬機器。如需詳細資訊，請參閱[連線到 Azure 上的 SQL Server 虛擬機器 (資源管理員)](virtual-machines-windows-sql-connect.md)。

<!---HONumber=AcomDC_0622_2016-->