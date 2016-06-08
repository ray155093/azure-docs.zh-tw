<properties
   pageTitle="Azure 儲存體安全性概觀 | Microsoft Azure"
   description="Azure 儲存體是現代應用程式的雲端儲存體解決方案，這些應用程式仰賴持續性、可用性和可調整性來滿足其客戶的需求。本文提供可用於 Azure 儲存體的核心 Azure 安全性功能概觀。"
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/19/2016"
   ms.author="terrylan"/>

# Azure 儲存體安全性概觀

Azure 儲存體是現代應用程式的雲端儲存體解決方案，這些應用程式仰賴持續性、可用性和可調整性來滿足其客戶的需求。Azure 儲存體提供一組完整的安全性功能：

- 您可以使用角色型存取控制與 Azure Active Directory 來保護儲存體帳戶。
- 您可以使用用戶端加密、HTTP 或 SMB 3.0，在應用程式和 Azure 之間進行傳輸時保護資料的安全。
- 使用儲存體服務加密寫入 Azure 儲存體時，可將資料設定為自動加密。
- 您可以使用 Azure 磁碟加密，將虛擬機器所使用的作業系統和資料磁碟設定為加密。
- Azure 儲存體中資料物件的委派存取權可以使用「共用存取簽章」來授與。

本文將提供這其中每個可搭配 Azure 儲存體使用的核心安全性功能概觀。所提供的文章連結將提供每項功能的詳細資料，以讓您深入了解。

若要深入了解 Azure 儲存體中的安全性，請參閱 [Azure 儲存體安全性指南](https://azure.microsoft.com/documentation/articles../storage/storage-security-guide.md)。

以下是本文所涵蓋的核心功能：

- 角色型存取控制
- 管理儲存體帳戶金鑰
- 儲存體物件的委派存取權
- 傳輸中加密
- 待用加密/儲存體服務加密
- 儲存體分析

## 角色型存取控制 (RBAC)

您可以使用角色型存取控制 (RBAC) 來保護儲存體帳戶。每個 Azure 訂用帳戶都會與一個 Azure Active Directory (AD) 租用戶相關聯。該目錄中的使用者、群組和應用程式可以管理 Azure 訂用帳戶中的資源。在特定範圍將適當的 RBAC 角色指派給使用者、群組和應用程式，即可授與這些存取權限。

您可以使用 RBAC 來授與 Azure 儲存體帳戶的管理作業存取權。使用 RBAC，您可控制誰可以︰

- 管理該儲存體帳戶。
- 讀取儲存體帳戶金鑰，並使用這些金鑰來存取 Blob、佇列、資料表和檔案。
- 重新產生儲存體金鑰。

深入了解：

- [Azure Active Directory 角色型存取控制](../active-directory/role-based-access-control-configure.md)

## 管理儲存體帳戶金鑰

當您建立儲存體帳戶時，Azure 會產生兩個 512 位元的儲存體存取金鑰，作為存取儲存體帳戶時的驗證憑藉。透過提供這兩個儲存體存取金鑰，Azure 讓您可重新產生金鑰，同時又不需中斷儲存體服務或對該服務的存取。您可以在重新產生一個存取金鑰的同時，使用另一個存取金鑰維持儲存體帳戶連線。

儲存體帳戶金鑰搭配儲存體帳戶名稱，即可用來存取儲存於儲存體帳戶中的資料物件，例如，Blob、表格內的實體、佇列訊息，以及 Azure 檔案共用中的檔案。使用 RBAC，可以控制儲存體帳戶金鑰的存取，進而控制資料物件本身的存取。

深入了解：

- [關於 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)

## 儲存體物件的委派存取權

共用存取簽章 (SAS) 是一個字串，包含可附加至 URI 的安全性權杖，可讓您委派儲存體物件的存取權，以及指定存取的權限和日期/時間範圍之類的限制。

散發您的儲存體帳戶金鑰，就像是在您的儲存體王國內共用金鑰。它會授與完整存取權限。使用 SAS，您可以只為用戶端提供有限時間內所需的權限。您可以：

- 授與對 Blob、容器、佇列、檔案和表格的存取權。使用表格時，您可以實際授與權限來存取表格中某個範圍的實體，方法是指定您想要讓使用者有權存取的分割區和資料列索引鍵範圍。
- 指定將使用 SAS 所提出的要求限制為特定的 IP 位址或 Azure 外部的 IP 位址範圍。
- 要求使用特定通訊協定 (HTTPS 或 HTTP/HTTPS) 來提出要求。這表示，如果您只想要允許 HTTPS 流量，您可以將必要的通訊協定設定為僅限 HTTPS，而且將會封鎖 HTTP 流量。

深入了解：

- [了解 SAS 模型](../storage/storage-dotnet-shared-access-signature-part-1.md)

## 傳輸中加密
傳輸中加密是透過網路傳輸資料時用來保護資料的機制。使用 Azure 儲存體，您可以使用下列各向來保護資料︰

- 傳輸層級加密，例如從 Azure 儲存體傳入或傳出資料時的 HTTPS。
- 連線加密，例如 Azure 檔案共用的 SMB 3.0 加密。
- 用戶端加密，以在將資料傳輸至儲存體之前加密資料，以及自儲存體傳出後解密資料。

### 傳輸層級加密

若要確保 Azure 儲存體資料的安全性，您可以在用戶端與 Azure 儲存體之間加密資料。您可以在呼叫 REST API 或存取儲存體中的物件時[啟用 HTTPS](../app-service-web/web-sites-configure-ssl-certificate.md)。此外，[共用存取簽章](../storage/storage-dotnet-shared-access-signature-part-1.md) (SAS)，可用來委派 Azure 儲存體物件的存取權。這些包括選擇性參數 “protocol”，其可用來指定使用 SAS 時唯一可用的 HTTPS 通訊協定。這可確保任何送出含 SAS 權杖之連結的人員，將會使用適當的通訊協定。

### 適用於檔案共用存取的連線加密

SMB 3.0 支援加密，而且可以搭配 Windows Server 2012 R2、Windows 8、Windows 8.1 和 Windows 10 使用，允許跨區域存取，甚至是桌面上的存取。

Azure 檔案共用可以與 Linux 虛擬機器搭配使用。Linux SMB 用戶端尚未支援加密，因此只允許在 Azure 區域內存取。Linux 的加密支援目前正在規劃中。當他們新增加密功能時，您必須具備存取 Linux 上 Azure 檔案共用的相同網路層級加密，就像您對於 Windows 所做的一樣。

深入了解：

- [如何搭配使用 Azure 檔案儲存體與 Linux](../storage/storage-how-to-use-files-linux.md)
- [在 Windows 上開始使用 Azure 檔案儲存體](../storage/storage-dotnet-how-to-use-files.md)
- [Azure 檔案儲存體內部](https://azure.microsoft.com/blog/inside-azure-file-storage/)

### 用戶端加密

用戶端加密可協助您確保在用戶端應用程式與 Azure 儲存體之間傳輸時，您的資料安全無虞。資料會先加密，然後才傳輸到 Azure 儲存體。從 Azure 儲存體擷取資料時，在用戶端上收到資料之後要將之解密。即使資料在通過連線時已加密，您也可以使用 HTTPS 協助降低影響資料完整性的網路錯誤。

深入了解：

- [Microsoft Azure 儲存體的用戶端加密](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
- [雲端安全性控制項系列︰加密傳輸中的資料](http://blogs.microsoft.com/cybertrust/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## 待用加密

有三個 Azure 功能可提供「待用」資料的加密。

- 儲存體服務加密
- 用戶端加密
- Azure 磁碟加密

### 儲存體服務加密

儲存體服務加密可讓您要求儲存體服務在將資料寫入 Azure 儲存體時自動加密資料。當您從 Azure 儲存體讀取資料時，儲存體服務會在傳回資料之前將之解密。這讓能夠您保護資料，而不需修改程式碼或將程式碼加入任何應用程式。

[Azure 儲存體服務加密](https://azure.microsoft.com/services/storage/)適用於 [Azure Blob 儲存體](https://azure.microsoft.com/services/storage/blobs/)。如需其他 Azure 儲存體類型的詳細資訊，請參閱[檔案](https://azure.microsoft.com/services/storage/files/)、[磁碟 (進階儲存體)](https://azure.microsoft.com/services/storage/premium-storage/)、[資料表](https://azure.microsoft.com/services/storage/tables/)和[佇列](https://azure.microsoft.com/services/storage/queues/)。

深入了解：

- [待用資料的 Azure 儲存體服務加密](../storage/storage-service-encryption.md)

### 用戶端加密

在討論傳輸中加密時，我們曾提及用戶端加密。此功能可讓您以程式設計方式加密用戶端應用程式中的資料，然後透過連線傳送資料以寫入 Azure 儲存體，並在從 Azure 儲存體擷取資料之後以程式設計方式解密資料。

這會提供在傳輸中加密，但也會提供待用加密的功能。請注意，雖然會在傳輸過程中加密資料，您仍可使用 HTTPS 來充分利用內建的資料完整性檢查，協助降低影響資料完整性的網路錯誤。

深入了解：

- [Microsoft Azure 儲存體的用戶端加密和 Azure Key Vault 金鑰保存庫](../storage/storage-client-side-encryption.md)

### Azure 磁碟加密

適用於虛擬機器 (VM) 的 Azure 磁碟加密會使用您在 [Azure 金鑰保存庫](https://azure.microsoft.com/services/key-vault/)所控制的金鑰與原則將 VM 磁碟 (包括開機和資料磁碟) 加密，協助您達成組織安全性與相容性需求。

適用於 VM 的磁碟加密可用於 Linux 與 Windows 作業系統。也會使用金鑰保存庫，協助您防護、管理和稽核您的磁碟加密金鑰的使用情形。VM 磁碟中的所有資料皆在待用時，透過 Azure 儲存體帳戶中符合業界標準的加密技術進行加密。Windows 的磁碟加密解決方案是建基於 [Microsoft BitLocker 磁碟機加密](https://technet.microsoft.com/library/cc732774.aspx)，而 Linux 解決方案是建基於 [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt)。

深入了解：

- [適用於 Windows 和 Linux IaaS 虛擬機器的 Azure 磁碟加密](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## 儲存體分析

對於每個儲存體帳戶，您可以啟用 Azure 儲存體分析，來執行記錄和儲存計量資料。當您想要檢查儲存體帳戶的效能計量，或排解儲存體帳戶的效能問題時，這是一個絕佳的工具。

您可以在儲存體分析記錄中看見的另一部分資料是其他人存取儲存體時所使用的驗證方法。例如，使用 Blob 儲存體，您可以看見他們使用的是否為共用存取簽章或儲存體帳戶金鑰，或者存取的 Blob 是否為公用的。

深入了解：

- [Storage Analytics](../storage/storage-analytics.md)
- [儲存體分析記錄檔格式](https://msdn.microsoft.com/library/azure/hh343259.aspx)
- [在 Azure 入口網站中監視儲存體帳戶](../storage/storage-monitor-storage-account.md)
- [使用 Azure 儲存體度量和記錄、AzCopy 和 Message Analyzer 進行端對端疑難排解](../storage/storage-e2e-troubleshooting.md)

<!---HONumber=AcomDC_0525_2016-->