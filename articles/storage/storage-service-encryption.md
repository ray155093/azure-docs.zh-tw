<properties
	pageTitle="待用資料的 Azure 儲存體服務加密 (預覽)| Microsoft Azure"
	description="使用 Azure 儲存體服務加密功能，在儲存資料時於服務端加密您的 Azure Blob 儲存體，在擷取資料時將其解密。"
	services="storage"
	documentationCenter=".net"
	authors="robinsh"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/01/2016"
	ms.author="robinsh"/>

# 待用資料的 Azure 儲存體服務加密 (預覽)

待用資料的 Azure 儲存體服務加密 (SSE) 會協助您保護資料安全，以符合組織安全性和法規遵循承諾。利用此功能，Azure 儲存體會自動加密資料，再保存到儲存體，以及在擷取之前解密。以完全無感的方式處理所有加密、解密和金鑰管理。

下列章節提供有關如何使用儲存體服務加密功能的詳細指引，以及支援的案例和使用者體驗。

## 概觀


Azure 儲存體提供一組完整的安全性功能，讓開發人員能夠共同建置安全應用程式。您可以使用[用戶端加密](storage-client-side-encryption.md)、HTTP 或 SMB 3.0，在應用程式和 Azure 之間進行傳輸時保護資料的安全。儲存體服務加密是 Azure 儲存體的新功能，在資料寫入至 Azure 儲存體 (支援區塊 Blob、分頁 Blob 和附加 Blob) 時加密資料。您可以針對使用 Azure Resource Manager 部署模型的新儲存體帳戶啟用此功能，並且適用於所有備援層級 (LRS、ZRS、GRS、RA-GRS)。儲存體服務加密適用於標準和進階儲存體，以完全透明的方式處理加密、解密和金鑰管理。所有資料都使用 256 位元 [AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) (可用的最強區塊加密方式之一) 進行加密。下方的 [預覽] 區段有關於如何連線儲存體服務加密的預覽程式的詳細資訊。

這個螢幕擷取畫面顯示如何使用 [Azure 入口網站](https://azure.portal.com)尋找儲存體服務加密設定。在此畫面上，按一下 [加密] 以繼續。

![入口網站螢幕擷取畫面顯示 [加密] 選項](./media/storage-service-encryption/image1.png)

按一下 [加密] 設定之後，您可以啟用或停用「儲存體服務加密」。

![入口網站螢幕擷取畫面顯示 [加密] 屬性](./media/storage-service-encryption/image2.png)

##Availability

對於標準儲存體，此功能目前只能在美國中部、東亞、美國東部 2、西歐及美國西部使用。

對於進階儲存體，此功能目前只能在美國中部、美國東部 2、日本東部及美國西部使用。

當我們在其他區域推出這項功能時，我們將會更新此文件。

##加密案例

可以在儲存體帳戶層級啟用儲存體服務加密。它支援下列客戶案例：

-   區塊 Blob、附加 Blob 和分頁 Blob 的加密。

-   從內部部署移至 Azure 的封存 VHD 和範本的加密。

-   使用您的 VHD 建立的 IaaS VM 基礎 OS 和資料磁碟的加密。

公開預覽版本包括下列限制：

-   不支援傳統儲存體帳戶的加密。

-   不支援移轉至 Resource Manager 儲存體帳戶的傳統儲存體帳戶的加密。

-   現有資料 - SSE 只會加密啟用加密之後新建立的資料。例如，如果您建立新的 Resource Manager 儲存體帳戶但是未開啟加密，然後您將 blob 或封存 VHD 上傳至該儲存體帳戶，然後開啟 SSE，那些 blob 將不會加密，除非它們重新寫入或複製。

-   Marketplace 支援 - 對於從 Marketplace 使用 (Azure 入口網站)[https://portal.azure.com、PowerShell 和 Azure CLI 建立的 VM 啟用加密。VHD 基底影像將保持未加密狀態；不過，在 VM 啟動之後完成的任何寫入將會加密。

-   資料表、佇列和檔案資料將不會加密。

##預覽

只有新建立的 Resource Manager 儲存體帳戶支援此功能，不支援傳統儲存體帳戶。若要使用這項新功能，您必須使用 PowerShell Cmdlet 註冊您的訂用帳戶。一旦核准您的訂用帳戶，您可以對已核准訂用帳戶底下的儲存體帳戶啟用 SSE。如同大部分的預覽，此功能在正式推出之前，不應用於生產工作負載。您可以加入我們在 Yammer 上的儲存體服務加密預覽群組，提供有關您的體驗的任何意見。

### 註冊預覽

-   [安裝 Azure PowerShell Cmdlet](../powershell-install-configure.md)。

-   在 Windows 10 中以系統管理員的身分開啟 PowerShell。

-   註冊儲存體資源提供者命名空間。只有尚未註冊 SRP 的訂用帳戶才需要這麼做。

    `PS E:> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Storage" `

-   若要註冊功能，您可以使用 Register-AzureRmProviderFeature PowerShell Cmdlet。

    `Register-AzureRmProviderFeature -FeatureName "EncryptionAtRest" -ProviderNamespace "Microsoft.Storage"`

-   若要查詢您的註冊的狀態，以查看訂用帳戶是否已核准，您可以使用 Get-AzureRmProviderFeature PowerShell Cmdlet。

    `Get-AzureRmProviderFeature -FeatureName "EncryptionAtRest" -ProviderNamespace "Microsoft.Storage"`

當註冊狀態傳回「已註冊」時，您的訂用帳戶已核准。另請造訪我們在 Yammer 上的 Azure 儲存體服務加密預覽群組。

##開始使用

###步驟 1：[註冊預覽](#registering-for-preview)。

###步驟 2：[建立新的儲存體帳戶](storage-create-storage-account.md)。

###步驟 3︰啟用加密。

您可以使用 [Azure 入口網站](https://portal.azure.com)啟用加密。

> [AZURE.NOTE] 如果您想要以程式設計方式啟用或停用儲存體帳戶上的儲存體服務加密，您可以使用 [Azure 儲存體資源提供者 REST API](https://msdn.microsoft.com/library/azure/mt163683.aspx)。我們即將會將此功能新增至[適用於 .NET 的儲存體資源提供者用戶端程式庫](https://msdn.microsoft.com/library/azure/mt131037.aspx)、Azure PowerShell 和 Azure CLI。

###步驟 4︰將資料複製到儲存體帳戶

#### 使用 AzCopy

AzCopy 是個 Windows 命令列公用程式，專為使用簡單命令高效率地將資料複製到和複製出 Microsoft Azure Blob、檔案和表格儲存體所設計。您可以使用此公用程式，將資料從現有的 Blob 儲存體帳戶複製到新的儲存體帳戶，後者已啟用加密功能。

若要深入了解，請參閱[使用 AzCopy 命令列公用程式傳輸資料](storage-use-azcopy.md)。

#### 使用儲存體用戶端程式庫

您可以使用我們豐富的儲存體用戶端程式庫集合，包括 .NET、C++、Java、Android、Node.js、PHP、Python 和 Ruby，將資料上傳至 Blob 儲存體，或從其中下載。

若要深入了解，請參閱[以 .NET 開始使用 Azure Blob 儲存體](storage-dotnet-how-to-use-blobs.md)。

#### 使用儲存體總管

儲存體總管可用來建立儲存體帳戶、上傳和下載資料、檢視 blob 的內容，以及瀏覽目錄。許多都同時支援傳統和 Resource Manager 儲存體帳戶。

您可以使用其中一種將 blob 上傳至儲存體帳戶，並且啟用加密。使用某些儲存體總管，您也可以從現有的儲存體帳戶將資料複製到已啟用 SSE 的新儲存體帳戶。

若要深入了解，請參閱 [Azure 儲存體總管](storage-explorers.md)。

###步驟 5︰查詢加密資料的狀態

廣泛使用 SSE 之後，會部署儲存體用戶端程式庫的更新版本，可讓您查詢物件的狀態，以判斷它是否加密。

在此同時，您可以呼叫[取得帳戶屬性](https://msdn.microsoft.com/library/azure/mt163553.aspx)來驗證儲存體帳戶已啟用加密，或在 Azure 入口網站中檢視儲存體帳戶屬性。

##加密和解密工作流程

以下是加密/解密工作流程的簡短描述：

-   客戶會在儲存體帳戶上啟用加密。

-   當客戶將新資料 (放置 Blob、放置區塊、放置頁面等等) 寫入至 Blob 儲存體，每個寫入是使用 256 位元 [AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) (可用的最強區塊加密方式之一) 進行加密。

-   當客戶需要存取資料 (取得 Blob 等) 時，資料會在傳回給使用者之前自動解密。

-   如果已停用加密，就不會再加密新的寫入，現有加密資料在使用者重新寫入之前會保持加密。啟用加密時，寫入至 Blob 儲存體將會加密。資料的狀態在使用者於啟用/停用儲存體帳戶的加密之間切換時不會變更。

-   所有加密金鑰會由 Microsoft 儲存、加密及管理。

##待用資料的儲存體服務加密的常見問題集

**問：我有現有的傳統儲存體帳戶。可以在其上啟用 SSE 嗎？**

答︰否，在預覽中僅在新建立的 Resource Manager 儲存體帳戶支援 SSE。

**問：我要如何在我的傳統儲存體帳戶中加密資料？**

答︰您可以建立新的 Resource Manager 儲存體帳戶，並且使用 [AzCopy](storage-use-azcopy.md)，從現有的傳統儲存體帳戶將資料複製到新建立的 Resource Manager 儲存體帳戶。

**問：我有現有的 Resource Manager 儲存體帳戶。可以在其上啟用 SSE 嗎？**

答︰在 SSE 預覽期間，您必須建立新的帳戶以存取新的 SSE 功能。

**問︰我想要加密現有 Resource Manager 儲存體帳戶中目前的資料？**

答︰如果您現有的 Resource Manager 儲存體帳戶是在此預覽公告之前建立，您可以建立新的 Resource Manager 儲存體帳戶，並啟用加密。然後您可以從先前的儲存體帳戶複製資料，資料就會自動加密。不過，如果您的 Resource Manager 儲存體帳戶是在預覽公告之後建立，且您稍後決定啟用加密，您可以使用 Azure 入口網站對此儲存體帳戶啟用加密，然後將未加密的資料重新寫入回儲存體帳戶。

**問︰我使用進階儲存體，可以使用 SSE 嗎？**

答︰是，SSE 支援標準儲存體和進階儲存體。

**問︰如果我建立新的儲存體帳戶，並啟用 SSE，然後使用該儲存體帳戶建立新的 VM，是否表示我的 VM 會加密？**

答： 會。使用新的儲存體帳戶建立的任何磁碟將會加密，只要它們是在啟用 SSE 之後建立。如果 VM 是使用 Azure Market Place 建立，VHD 基底影像將保持未加密狀態；不過，在 VM 啟動之後完成的任何寫入將會加密。

**問︰是否可以使用 Azure PowerShell 和 Azure CLI 建立新的儲存體帳戶並且啟用 SSE？**

答︰我們將在即將推出的 Azure PowerShell 和 Azure CLI 發行此功能，目前的目標是 4 月底。

**問︰如果已啟用 SSE，Azure 儲存體的成本會多出多少？**

答：沒有任何額外成本。

**問：如何註冊預覽？**

答︰您可以使用 PowerShell 註冊預覽的存取權。一旦您的訂用帳戶已核准可以使用該功能，您就可以使用 PowerShell 來啟用靜態加密。

**問︰何時使用 PowerShell 註冊預覽，我需要註冊的功能名稱是什麼？**

答︰EncryptionAtRest。

**問︰誰負責管理加密金鑰？**

答︰金鑰是由 Microsoft 管理。

**問︰我可以使用自己的加密金鑰嗎？**

答︰我們正在努力提供功能讓客戶使用他們自己的加密金鑰。

**問︰是否可以撤銷加密金鑰的存取權？**

答︰目前不行。金鑰完全是由 Microsoft 管理。

**問︰我建立新的儲存體帳戶時是否預設啟用 SSE？**

答：預設未啟用 SSE；您可以使用 Azure 入口網站來啟用它。您也可以以程式設計方式，使用儲存體資源提供者 REST API 來啟用這項功能。

**問︰這項功能與 Azure 磁碟機加密有什麼不同？**

答︰這項功能是用來加密 Azure Blob 儲存體中的資料。Azure 磁碟加密可用來加密作業系統和 IaaS VM 中的資料磁碟。如需詳細資訊，請參閱我們的[儲存體安全性指南](storage-security-guide.md)。

**問︰如果我啟用 SSE，然後在磁碟上進入並啟用 Azure 磁碟加密？**

答︰這會順暢地運作。這兩種方法都會加密您的資料。

**問︰我的儲存體帳戶設定為異地備援複寫。如果啟用 SSE，我的備援複本是否也會加密？**

答︰是，會加密儲存體帳戶的所有複本，並且支援所有備援選項 – 本地備援儲存體 (LRS)、區域備援儲存體 (ZRS)、異地備援儲存體 (GRS) 和讀取權限異地備援儲存體 (RA-GRS)。

**問︰我無法在我的儲存體帳戶上啟用加密。**

答︰您何時建立儲存體帳戶？ 在預覽中，您必須註冊您的訂用帳戶，並且建立新的儲存體帳戶，才能使用 SSE；您無法在預覽之前建立的儲存體帳戶上啟用 SSE。

**問︰是否只有在特定區域中允許 SSE 預覽？**

答：SSE 預覽在東亞和西歐適用於標準儲存體，在日本東部適用於進階儲存體。我們將會在數個月內在其他區域中推出這項功能，屆時我們將會更新此文件。

**問︰如果我有任何問題或想要提供意見反應，要如何連絡相關人員？**

答︰如果有儲存體服務加密的相關問題，請連絡 [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com)。

##後續步驟

Azure 儲存體提供一組完整的安全性功能，讓開發人員能夠共同建置安全應用程式。如需詳細資訊，請參閱[儲存體安全性指南](storage-security-guide.md)。

<!---HONumber=AcomDC_0706_2016-->