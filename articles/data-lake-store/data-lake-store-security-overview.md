<properties 
   pageTitle="Data Lake Store 安全性概觀 | Microsoft Azure" 
   description="了解 Azure Data Lake Store 如何成為安全的巨量資料存放區" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="07/18/2016"
   ms.author="nitinme"/>

# Azure Data Lake Store 安全性

許多企業都運用巨量資料分析來取得商務深入解析，進而做出明智的決策。這些組織可能具備複雜且有規範的環境，各種使用者的數目日益增加。因此，企業務必確定以授與給相關使用者的適當存取層級安全地儲存重要商務資料。Azure Data Lake Store 的設計是以這些安全性需求為考量。在本文中，您將了解 Azure Data Lake Store (ADLS) 的安全性功能，例如︰

* 驗證
* Authorization
* 網路隔離
* 資料保護
* 稽核

 
## 驗證和識別管理

驗證是使用者在與 Data Lake Store 或任何連線至 Data Lake Store 的服務互動時，用以證明其身分識別的程序。針對身分識別管理和驗證，Data Lake Store 會 [Azure Active Directory](../active-directory/active-directory-whatis.md) (AAD)，這是一個綜合身分識別和存取管理雲端解決方案，可以簡化使用者和群組的管理。

目前，每一個 Azure 訂用帳戶都可與 Azure Active Directory 建立關聯。只有此目錄中定義的使用者和服務身分識別可以透過 Azure 入口網站、命令列工具或使用 Data Lake Store SDK 建置的用戶端應用程式來存取您的 Data Lake Store。使用 Azure Active Directory 做為集中式存取控制機制的主要優點如下︰

* 簡化身分識別生命週期管理。只要刪除或停用目錄中的帳戶，即可快速建立及撤銷使用者或服務的身分識別 (服務主體身分識別)。

* 支援 [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)，其可為使用者登入和交易提供額外的安全性。

* 允許使用者從任何使用標準開放式通訊協定 (例如 OAuth 和 OpenID) 的用戶端進行驗證。

* 可讓企業目錄服務和雲端識別提供者聯盟。

## 授權和存取控制

使用者由 AAD 進行驗證以存取 Azure Data Lake Store 後，授權便會控制 Data Lake Store 的存取權限。Data Lake Store 會以下列方式分隔授權的帳戶以及資料相關的活動。

* Azure 針對帳戶管理提供的[角色型存取控制](../active-directory/role-based-access-control-what-is.md) (RBAC)
* 適用於存取存放區資料的 POSIX ACL。

### 使用 RBAC 進行帳戶管理

預設已定義四個基本角色。這些角色允許透過入口網站、PowerShell Cmdlet 和 REST API，在 Data Lake Store 帳戶上執行不同的作業。[擁有者] 和 [參與者] 角色會提供帳戶上各種不同的管理功能。對於僅與資料互動的使用者，您可以將它們新增至 [讀取器] 角色。

![RBAC 角色](./media/data-lake-store-security-overview/rbac-roles.png "RBAC 角色")

請注意，雖然指派這些角色的目的是為了進行帳戶管理 (視特定角色而定)，但也對資料存取權限有所影響。對於使用者可以在檔案系統上執行的作業，您需要使用存取控制清單 (ACL)。以下是這些角色的管理權限和資料存取權限摘要。

| 角色 | 管理權限 | 資料存取權限 | 說明 |
|--------------------------|---------------------------------|--------------------|-------------|
| 未指派角色 | None | 由 ACL 控管 | 在這種情況下，使用者無法使用 Azure 入口網站或 Azure PowerShell Cmdlet 來瀏覽 Data Lake Store。這類使用者必須完全依賴命令列工具。 |
| 擁有者 | 全部 | 全部 | 擁有者是超級使用者，因此擁有者角色可讓您管理所有一切並具有資料的完整存取權 | 
| 讀取者 | 唯讀 | 由 ACL 控管 | 讀取者可以檢視有關帳戶管理的所有一切，例如哪個使用者被指派給哪個角色，但無法進行任何變更 |
| 參與者 | 除了新增和移除角色以外的一切 | 由 ACL 控管 | 參與者可以管理帳戶的其他方面，例如建立/管理警示、部署等。參與者無法新增或移除角色 |
| 使用者存取系統管理員 | 新增和移除角色 | 由 ACL 控管 | 使用者存取管理員可讓您管理使用者對帳戶的存取權。 |

如需相關指示，請參閱[指派使用者或安全性群組給 Azure Data Lake Store 帳戶](data-lake-store-secure-data.md#assign-users-or-security-groups-to-azure-data-lake-store-accounts)。

### 在檔案系統上使用 ACL 執行作業

Azure Data Lake Store 是階層式檔案系統 (如 HDFS)，可支援 [POSIX ACL](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists) - 允許對授與擁有者、歸屬群組及其他使用者/群組的資源，提供讀取 (r)、寫入 (w) 及執行 (x) 存取權限。在 Data Lake Store 公開預覽版本 (目前的版本) 中，ACL 只會在根資料夾上啟用，這表示您套用至根資料夾的 ACL 也適用於所有子資料夾/檔案。在未來的版本中，您將能夠在任何檔案或資料夾上設定 ACL。

建議您使用[安全性群組](../active-directory/active-directory-accessmanagement-manage-groups.md)為許多使用者定義 ACL。將使用者分組到安全性群組，然後將檔案和資料夾的 ACL 指派給該安全性群組。提供自訂存取權時，這非常有用，因為您只能在自訂存取權中新增最多九個項目的情況有所限制。如需有關使用 AAD 安全性群組保護 Data Lake Store 中所存放資料的詳細資訊，請參閱[將使用者或安全性群組以 ACL 形式指派給 Azure Data Lake Store 檔案系統](data-lake-store-secure-data.md#filepermissions)。

![列出標準和自訂存取](./media/data-lake-store-security-overview/adl.acl.2.png "列出標準和自訂存取")

## 網路隔離

Azure Data Lake Store 可讓您進一步在網路層級鎖定資料存放區的存取。您可以啟用防火牆，並且為受信任的用戶端定義 IP 位址範圍。一旦啟用，只有具有定義範圍內 IP 位址的用戶端可以連線到存放區。

![防火牆設定和 IP 存取](./media/data-lake-store-security-overview/firewall-ip-access.png "防火牆設定和 IP 位址")

## 資料保護

組織想要確保其業務關鍵資料在整個生命週期中受到保護。對於傳輸中的資料，Data Lake Store 會使用業界標準的 TLS (傳輸層安全性通訊協定) 來保護用戶端與 Data Lake Security 之間的資料。

待用資料的資料保護也會在未來的版本中提供。

## 稽核和診斷記錄檔

您可以視您要尋找管理相關活動或資料相關活動的記錄檔而定，使用稽核或診斷記錄檔。

*  管理相關活動會使用 Azure Resource Manager API 並透過稽核記錄檔呈現在 Azure 入口網站中。
*  資料相關活動會使用 WebHDFS API 並透過診斷記錄檔呈現在 Azure 入口網站中。

### 稽核記錄檔

為了符合法規，如果組織需要深入探索事件，則可能需要適當的稽核線索。Data Lake Store 有內建的監視和稽核作業，以記錄所有的帳戶管理活動。

如需帳戶管理稽核線索，您可以檢視並選擇要記錄的感興趣資料行，而且將稽核記錄檔匯出至 Azure 儲存體。

![稽核記錄檔](./media/data-lake-store-security-overview/audit-logs.png "稽核記錄檔")

### 診斷記錄

您可以從 Azure 入口網站 (**診斷設定**) 啟用資料存取稽核線索，並提供將要存放記錄檔的 Azure Blob 儲存體帳戶。

![診斷記錄](./media/data-lake-store-security-overview/diagnostic-logs.png "診斷記錄")

一旦您啟用了診斷設定，即可在 [診斷記錄] 索引標籤中查看記錄。

## 摘要

企業客戶要求安全且容易使用的資料分析雲端平台。Azure Data Lake Store 的設計是透過 Azure Active Direction 整合、以 ALC 為基礎的授權、網路隔離、傳輸中和待用資料加密 (未來即將提供) 以及稽核，來滿足身分識別管理和驗證的需求。

如果您想要查看 Data Lake Store 中包含的新功能，請在 [Uservoice 論壇](https://feedback.azure.com/forums/327234-data-lake)將您的意見反應傳給我們。

## 另請參閱

- [Azure Data Lake Store 概觀](data-lake-store-overview.md)
- [開始使用 Data Lake Store](data-lake-store-get-started-portal.md)
- [保護 Data Lake Store 中的資料](data-lake-store-secure-data.md)

<!---HONumber=AcomDC_0720_2016-->