<properties
	pageTitle="利用 PowerShell 指令碼複製傳統虛擬機器至 Azure Resource Manager"
	description="本文說明如何利用 PowerShell 指令碼複製單一傳統虛擬機器至 Azure Resource Manager"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="singhkays"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/03/2016"
	ms.author="singhkay"/>

# 利用 PowerShell 指令碼複製傳統虛擬機器至 Azure Resource Manager

本文將說明如何使用位於 [Azure/classic-iaas-resourcemanager-migration](https://github.com/Azure/classic-iaas-resourcemanager-migration) 的指令碼將**單一**傳統虛擬機器複製到 Azure Resource Manager 部署模型。

>[AZURE.IMPORTANT]使用這些指令碼複製可能會造成傳統虛擬機器停機。如果您想要尋求支援移轉的平台，請造訪
- [支援將 IaaS 資源從傳統移轉至 Azure Resource Manager 堆疊的平台](./virtual-machines-windows-migration-classic-resource-manager.md)
- [深入技術探討支援從傳統移轉至 Azure Resource Manager 的平台](./virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [使用 Azure PowerShell 將 IaaS 資源從傳統移轉至 Azure Resource Manager](./virtual-machines-windows-ps-migration-classic-resource-manager.md)

## 取得指令碼

>[AZURE.NOTE]Microsoft 支援服務並未正式支援 PowerShell 指令碼。因此可在 Github 上取得其開放原始碼，我們很樂意收到修正或其他案例的 PR。

若要取得指令碼，可以從上述儲存機制下載 zip 檔案，或使用下列命令**之一**複製儲存機制

```
git clone https://github.com/Azure/classic-iaas-resourcemanager-migration.git
```

**OR**

```
git clone git@github.com:Azure/classic-iaas-resourcemanager-migration.git
```

## 匯入移轉模組

下一個步驟是將模組匯入您的 PowerShell 工作階段。這可透過以下命令來進行

```
Import-Module .\asm2arm.psd1
```

## 使用傳統部署模型和 Azure Resource Manager 訂用帳戶進行驗證

複製傳統虛擬機器之前，您必須向傳統部署模型和 Azure Resource Manager 堆疊驗證您的 PowerShell 工作階段。您可以使用下列 Cmdlet 完成此動作

```
Add-AzureAccount
Login-AzureRmAccount
```

>[AZURE.IMPORTANT]確定已選取預設訂用帳戶，傳統部署模型請使用 `Select-AzureSubscription`，Azure Resource Manager 請使用 AzureRmContext

## 使用指令碼

### 取得 Cmdlet

匯入模組會將下面兩個 Cmdlet 加入您的工作階段。

```
Add-AzureSMVmToRM
New-AzureSmToRMDeployment
```

這些 Cmdlet 可完成以下指定功能。

#### Add-AzureSMVmToRM
- 假定有虛擬機器，會產生一組 Azure Resource Manager 範本和命令式 PowerShell 指令碼 (用來複製磁碟 blob，且如果 VM 有 VM 代理程式擴充功能)

>[AZURE.IMPORTANT]若有指定 `-Deploy` 參數，此 Cmdlet 會呼叫 `New-AzureSmToRMDeployment` Cmdlet 來部署上述產生 Azure Resource Manager 範本和命令式 PowerShell 指令碼。

#### New-AzureSmToRMDeployment
- 部署 `Add-AzureSMVmToRM` Cmdlet 產生的範本和命令式 PowerShell 指令碼以開始移轉。

### 找出要複製的傳統虛擬機器

做法是將傳統虛擬機器狀態儲存在變數，並將變數傳遞到 `Add-AzureSMVmToRM` Cmdlet，或直接使用 `ServiceName` 和 VM `Name` 參數。一旦您找出傳統虛擬機器，可以使用 `Add-AzureSMVmToRM` Cmdlet 複製它。

```
$vm = Get-AzureVm -ServiceName acloudservice -Name atestvm
Add-AzureSMVmToRM -VM $vm -ResourceGroupName aresourcegroupname -DiskAction CopyDisks -OutputFileFolder D:\myarmtemplates -AppendTimeStampForFiles -Deploy
```

**OR**

```
Add-AzureSMVmToRM -ServiceName acloudservice -Name atestvm -ResourceGroupName aresourcegroupname -DiskAction CopyDisks -OutputFileFolder D:\myarmtemplates -AppendTimeStampForFiles -Deploy
```

>[AZURE.IMPORTANT]在上述範例中，因為我們使用 `-Deploy` 參數，便不需要使用 `New-AzureSmToRMDeployment` Cmdlet。

## 移轉如何使用這些指令碼？

Cmdlet 會用於複製傳統 VM 的整個程序驟，並產生資源做為自訂 PowerShell 雜湊表，用於儲存體、網路和計算資源提供者。這些代表資源的雜湊表會附加至陣列，之後再透過「序列化為 JSON」變成範本並寫入檔案。

範本會根據傳統虛擬機器代理程式擴充功能的存在和 DiskAction 選項值建立檔案。這些全都會放置在 OutputFileFolder 參數指定的目錄中。檔案如下︰

1. `<ServiceName>-<VMName>-setup<optional timestamp>.json`︰這個檔案代表複製傳統虛擬機器之前需要備妥的資源，可能和任何後續的 VM 所需的資源相同 (我們不會維護後續執行的狀態，但由於進行 blob 複製作業之前必須以命令方式佈建儲存體帳戶，將資源群組群組為一個頗合邏輯)

2.  `<ServiceName>-<VMName>-deploy<optional timestamp>.json`：包含 Resource Manager 虛擬機器的範本
3.  `<ServiceName>-<VMName>-parameters<optional timestamp>.json`：包含傳遞至範本的實際參數
4.  `<ServiceName>-<VMName>-setextensions<optional timestamp>.json`︰要執行的一組 PowerShell Cmdlet，用來設定虛擬機器代理程式擴充功能
4.  `<ServiceName>-<VMName>-copydisks<optional timestamp>.json`︰要執行的一組 PowerShell Cmdlet，用來複製磁碟 blob (若有指定 CopyDisks 選項)

如果設定了 -Deploy 旗標，則 Cmdlet 會在產生檔案之後部署 <ServiceName>-<VMName>-setup.json 範本、複製來源虛擬機器磁碟 Blob (如果將 DiskAction 參數設定為 CopyDisks)，然後針對參數使用 <ServiceName>-<VMName>-parameters.json 檔案來部署 <ServiceName>-<VMName>-deploy.json 範本。虛擬機器部署完成之後，如果有命令式指令碼 (用於虛擬機器代理程式擴充功能) 或複製磁碟的指令碼，它們會執行。

### 網路詳細資料
Cmdlet 的用意不是要將傳統網路設定複製至 Resource Manager。它利用網路設備的方式用來複製虛擬機器本身最方便。以下是不同情況下發生的事︰

1.  目標資源群組上沒有虛擬網路
    - 來源虛擬機器不在子網路上︰建立位址為 10.0.0.0/16 的預設虛擬網路以及位址空間為 10.0.0.0/22 的子網路。
    - 來源虛擬機器位於子網路︰會探索到虛擬機器所在之虛擬網路、虛擬網路的規格、複製過去的子網路
2.  目標資源群組具有名為 `<VM virtual network>arm` 的虛擬網路 (會附加 'arm' 字串)
    - 如果虛擬網路的子網路具有相同名稱和位址空間，請使用它。
    - 如果找不到適當的子網路，在現有子網路外找到具有 22 位元遮罩的位址區塊，則使用它。

## 複製與平台移轉

目前的複製方式與平台支援的移轉之間有一些差異。

### 複製


| 優點 | 缺點 |
|--------------------------------------------------------|:----------------------------------------------------------------:|
| 在雲端服務中複製任何虛擬機器 | 需要為 VM 而停機，因為 VM 需要關機 |
| 在 VNET 或 VNET 以外複製任何虛擬機器 | 在需要複製磁碟 blob 的案例中會長時間延遲 |
| | VM 的網路組態變更 - 內部 IP、VIP。 |


### 平台移轉


| 優點 | 缺點 |
|----------------------------------|:-------------------------------------------:|
| 大多數的虛擬網路內的 VM 組態不需要停機。 | 雲端服務中的所有 VM 或 VNET 中部署的所有資源必須一併移轉。 |
 
 
## 不支援的案例

**下列不在複製指令碼支援的範圍中**

 1. 停止執行中的虛擬機器
 2. 變更您的資料/磁碟
 3. 複製執行中的 VM
 4. 在複雜的案例中自動複製多個 VM
 5. 複製整個 ASM 網路組態
 6. 建立負載平衡的 VM。我們假設這是 Azure 專家必須明確地處理的組態
 
 
## 測試的組態

_Add-AzureSMVmToRM_ Cmdlet 使用下列測試案例進行驗證：

| # | 說明 |
|:---|:---|
| 1 | Windows 虛擬機器以及含現有作業系統的磁碟 |
| 2 | Linux 虛擬機器以及含現有作業系統的磁碟 |
| 3 | Windows 虛擬機器以及含現有作業系統和資料的磁碟 |
| 4 | Linux 虛擬機器以及含現有作業系統和資料的磁碟 |
| 5 | Windows 虛擬機器以及從映像庫比對而來的新作業系統磁碟 |
| 6 | Linux 虛擬機器以及從映像庫比對而來的新作業系統磁碟 |
| 7 | Windows 虛擬機器以及含新作業系統和空資料的磁碟 |
| 8 | Linux 虛擬機器以及含新作業系統和空資料的磁碟 |
| 9 | Windows 虛擬機器以及公用端點 |
| 10 | Linux 虛擬機器以及公用端點 |
| 11 | Windows 虛擬機器以及 WinRM 憑證 |
| 12 | 在 VNET 和子網路中的 Windows 虛擬機器 |
| 13 | 在 VNET 和子網路中的 Linux 虛擬機器 |
| 14 | Windows 虛擬機器以及自訂擴充功能 |
| 15 | 在可用性設定組中的 Windows 虛擬機器 |
| 16 | 在可用性設定組中的 Windows 虛擬機器，以及在 VNET 和子網路中的多個資料磁碟、公用端點，以及自訂擴充功能 |
| 17 | 在可用性設定組中的 Linux 虛擬機器，以及在 VNET 和子網路中的多個資料磁碟、公用端點，以及自訂擴充功能 |

## 注意事項
1. 如果逐一複製多個 VM，且每個複製之間有短時間間隔，因為 DNS 快取重新整理時間的緣故，公用 IP 位址可能會有 DNS 名稱衝突。

<!---HONumber=AcomDC_0817_2016-->