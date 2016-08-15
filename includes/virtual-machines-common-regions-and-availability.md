# 區域和可用性概觀
Azure 在世界各地多個資料中心運作。這些資料中心會依據地理區域分組，提供您彈性來選擇要建置應用程式的位置。您也可以利用 Azure 平台內的備援和可用性功能 (例如儲存體複寫和可用性設定組)，建置高可用性應用程式。

## 什麼是 Azure 區域？
Azure 可讓您在定義的地理區域 (例如「美國西部」、「北歐」或「東南亞」) 中建立資源 (例如 VM)。目前全世界有 26 個 Azure 區域。您可以檢閱[地區及其位置的清單](https://azure.microsoft.com/regions/)。為了提供備援和可用性，每個區域內都有多個資料中心。這可為您在建置應用程式時提供彈性，以建立距離使用者最近的虛擬機器 (VM)，並滿足任何法務、法規遵循或稅務方面的目的。

## 特殊 Azure 區域
有一些適用於法規遵循或法務目的的特殊 Azure 區域，您在建置應用程式時可能會想要使用這些區域。

現有的特殊區域包括：

- **美國維吉尼亞州政府**和**美國愛荷華州政府**
    - 由經篩選的美國人士所操作、適用於美國政府機構及其合作夥伴的實體與邏輯網路隔離 Azure 執行個體。包含其他法規遵循認證，例如 [FedRAMP](https://www.microsoft.com/zh-TW/TrustCenter/Compliance/FedRAMP) 和 [DISA](https://www.microsoft.com/zh-TW/TrustCenter/Compliance/DISA)。深入了解 [Azure Government](https://azure.microsoft.com/features/gov/)。
- **澳大利亞東部**和**澳大利亞東南部**
    - 這些區域可供在澳洲或紐西蘭有營業據點的客戶使用。
- **印度中部**、**印度南部**及**印度西部**
    - 這些區域目前可供在印度當地註冊的大量授權客戶和合作夥伴使用，並且將於 2016 全年開放給直接線上訂用帳戶存取。
- **中國東部**和**中國北部**
    - 這些區域是透過 Microsoft 與 21Vianet 之間的唯一合作關係提供，其中 Microsoft 不會直接維護資料中心。深入了解[位於中國的 Microsoft Azure](http://www.windowsazure.cn/)。

公告的特殊區域包括：

- **德國中部**和**德國東北部**
    - Azure 將透過新的資料信任者模型提供，其中客戶資料會留在德國受到 T-Systems 控管，這是 Deutsche Telekom 旗下的公司，扮演德國資料信任者的角色。

## 區域配對
每個 Azure 區域都會與相同地理位置 (例如美國、歐洲或亞洲) 內的另一個區域配對。這樣可允許複寫整個地理位置的資源 (例如 VM 儲存體)，而應能降低發生同時影響兩個區域之自然災害、社會動亂、電力中斷或實體網路中斷的可能性。區域配對的其他優點包括：

- 如果發生更大範圍的 Azure 中斷狀況，會優先復原所有配對中的一個區域，以協助縮短應用程式的還原時間。
- 計劃性 Azure 更新會以逐個區域的方式發行至配對的區域，以將停機時間縮到最短並將應用程式中斷風險降到最低。
- 資料會繼續駐留在與其配對相同的地理位置內 (巴西南部除外)，以符合稅務和執法管轄區的要求。

區域配對的範例包括：

| 主要 | 次要 |
|:---------------|:------------|
| 美國西部 | 美國東部 |
| 北歐 | 西歐 |
| 東南亞 | 東亞 |

您可以[在這裡看到完整的區域配對清單](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions)。

## 功能可用性
有些服務或 VM 功能只有在特定區域提供，例如特定的 VM 大小或儲存體類型。也有一些不會要求您選取特定區域的通用 Azure 服務，例如 [Azure Active Directory](../articles/active-directory/active-directory-whatis.md)、[流量管理員](../articles/traffic-manager/traffic-manager-overview.md)或 [Azure DNS](../articles/dns/dns-overview.md)。為了協助您設計應用程式環境，您可以查看[每個區域的 Azure 服務可用性](https://azure.microsoft.com/regions/#services)。


## 儲存體可用性
在您考慮可用的「Azure 儲存體」複寫選項時，了解 Azure 區域和地理位置會變得相當重要。建立儲存體帳戶時，您必須選取下列其中一個複寫選項：

- 本機備援儲存體 (LRS)
    - 此選項會在您建立儲存體帳戶的區域內複寫資料三次。
- 區域備援儲存體 (ZRS)
    - 此選項會在單一區域或兩個區域內的兩個到三個設備複寫資料三次。
- 異地備援儲存體 (GRS)
    - 此選項會將資料複寫到與主要區域距離數百英哩的次要區域。
- 讀取權限異地備援儲存體 (RA-GRS)
    - 此選項會與 GRS 一樣將資料複寫到次要區域，但此外還提供對次要位置中資料的唯讀存取權。

下表提供儲存體複寫類型間差異的快速概觀︰

| 複寫策略 | LRS | ZRS | GRS | RA-GRS |
|:-----------------------------------------------------------------------------------|:----|:----|:----|:-------|
| 可跨多個設備複寫資料。 | 否 | 是 | 是 | 是 |
| 可從次要位置及主要位置讀取資料。 | 否 | 否 | 否 | 是 |
| 可在不同的節點上維護的資料副本數量。 | 3 | 3 | 6 | 6 |

您可以[在這裡深入了解 Azure 儲存體複寫選項](../articles/storage/storage-redundancy.md)。

### 儲存成本
價格會依據您選取的儲存體類型和可用性而有所不同。

- 標準儲存體是由一般轉動式磁碟支援，收費方式是依據使用的容量和所需的儲存體可用性。
    - 針對 RA-GRS，會有一個額外的「異地複寫資料傳輸」費用，此費用是將該資料複寫到另一個 Azure 區域中時的頻寬費用。
- 進階儲存體是由「固態硬碟」(SSD) 支援，收費方式是依據磁碟的容量。

如需有關不同儲存體類型和可用性選項的定價資訊，請參閱 [Azure 儲存體定價](https://azure.microsoft.com/pricing/details/storage/)。


## Azure 映像
在 Azure 中，會從映像建立 VM。通常，這會是從 [Azure Marketplace](https://azure.microsoft.com/marketplace/) 建立的映象，合作夥伴可以在 Azure Marketplace 提供預先設定的完整作業系統或應用程式映像。

當您從 Azure Marketplace 中的映像建立 VM 時，您實際上是在使用範本。Azure Resource Manager 範本是宣告式「JavaScript 物件標記法」(JSON) 檔案，可用來建立包含 VM、儲存體、虛擬網路功能等的複雜應用程式環境。您可以進一步了解如何使用 [Azure Resource Manager 範本](../articles/resource-group-overview.md)，包括如何[建置您自己的範本](../articles/resource-group-authoring-templates.md)。

您也可以使用 [Azure CLI](../articles/virtual-machines/virtual-machines-linux-upload-vhd.md) 或 [Azure PowerShell](../articles/virtual-machines/virtual-machines-windows-upload-image.md) 來建立自己的自訂映像並上傳它們，以快速建立符合您特定建置需求的自訂 VM。使用自訂映像時，VM 必須儲存在與映像本身相同的儲存體帳戶中。您不能將映像上傳至單一區域，然後從該映像跨其他 Azure 區域建立 VM。


## 可用性設定組
可用性設定組是 VM 的邏輯群組，可讓 Azure 了解您應用程式的建置方式，以便提供備援和可用性。建議您在可用性設定組內建立兩個或更多個 VM，以便提供具高可用性的應用程式，以及符合 [99\.95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)。可用性設定組是由可防止硬體故障及允許安全地套用更新的兩個額外群組所組成 - 容錯網域 (FD) 和更新網域 (UD)。

![更新網域和容錯網域組態的概念圖](./media/virtual-machines-common-regions-and-availability/ud-fd-configuration.png)

您可以深入了解如何管理 [Linux VM](../articles/virtual-machines/virtual-machines-linux-manage-availability.md) 或 [Windows VM](../articles/virtual-machines/virtual-machines-linux-manage-availability.md)。

### 容錯網域
容錯網域是共用通用電源和網路交換器的基礎硬體邏輯群組，類似於內部部署資料中心內的機架。當您在可用性設定組內建立 VM 時，Azure 平台會自動將 VM 分散到這些容錯網域，以限制可能的實體硬體故障、網路中斷或電源中斷的影響。

### 更新網域
更新網域是可以同時進行維護或重新啟動的基礎硬體邏輯群組。當您在可用性設定組內建立 VM 時，Azure 平台會自動將 VM 分散到這些更新網域，以確保在 Azure 平台進行定期維護時，一律至少會有一個應用程式執行個體保持執行。請注意，在計劃性維護期間，要重新啟動的更新網域順序可能不會循序進行，而只會一次重新啟動一個更新網域。


## 後續步驟
您可以閱讀更多有關 [Azure 可用性最佳做法](../articles/best-practices-availability-checklist.md)的具體詳細資料。

<!---HONumber=AcomDC_0803_2016-->