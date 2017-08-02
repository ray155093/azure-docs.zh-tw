---
title: "Azure 受管理的應用程式概觀 | Microsoft Docs"
description: "說明 Azure 受管理應用程式的概念"
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/09/2017
ms.author: gauravbh; tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: 51732474e7983827988f950d344d36745564dfd2
ms.contentlocale: zh-tw
ms.lasthandoff: 07/13/2017

---

# <a name="azure-managed-applications-overview"></a>Azure 受管理的應用程式概觀

Azure 可讓廠商為世界各地的客戶提供解決方案。 Microsoft Azure Marketplace 是一個資源庫，包含第一方和第三方廠商之數百個複雜的多資源範本。 客戶可以在幾分鐘內部署並開始使用 PaaS 和 SaaS 應用程式。 雖然它提供能快速部署產品的絕佳方式，客戶仍必須負責維護和更新解決方案。 對廠商而言，當客戶使用應用程式超出虛擬機器映像計費時，並無法向客戶收費。 此外，廠商無法防止客戶修改重大的應用程式資源，且無法封鎖對組成應用程式的智慧財產之存取。 Azure 受管理的應用程式針對這些問題提供了解決方案。 

受管理的應用程式與 Marketplace 中的解決方案範本類似，只有一個主要差異。 在受管理的應用程式中，資源會佈建到廠商所管理的資源群組。 資源群組存在於客戶的訂用帳戶，但廠商租用戶中的身分識別可以存取資源群組。

## <a name="advantages-of-managed-applications"></a>受管理應用程式的優點

受管理的應用程式讓受管理的服務提供者 (MSP)、獨立軟體廠商 (ISV) 和公司中央 IT 小組能透過 Marketplace 或 Service Catalog 提供解決方案。 雖然客戶在其訂用帳戶中部署這些受管理的應用程式，但他們不需要維護、更新或服務它們。 廠商會管理並支援這些應用程式。 因此，客戶不需要開發特定應用程式特定領域的知識，來管理這些應用程式。 它讓客戶能自動獲得應用程式更新，而不必擔心疑難排解和診斷應用程式的問題。

對於廠商及提供者，受管理的應用程式不只透過 Marketplace 建立了對於銷售基礎結構和軟體的通道，也附加服務和作業支援給 Azure 客戶。 它讓廠商能使用 Azure 的計費系統向客戶寄送帳單，並使用範本來管理已部署應用程式的生命週期。 這些解決方案都各自獨立且客戶不會知曉，因此廠商可以提供高品質的服務。 此方法不只有 PaaS 和 SaaS 廠商可受益，公司中央平台小組和想要封裝並轉售解決方案的系統整合者也都能有所受益。

## <a name="managed-application-types"></a>受管理的應用程式類型
Azure 受管理的應用程式分為兩種 - Service Catalog 和 Marketplace。
 
### <a name="service-catalog"></a>Service Catalog  

Service Catalog 可讓組織建立已核准解決方案的目錄，讓該組織中的人員使用 Azure。 維護這類解決方案的目錄對於企業的中央 IT 小組很有幫助。 它讓他們能確保遵循特定組織標準，同時為他們的組織提供絕佳的解決方案。 他們可以控制、更新和維護這些應用程式。 組織中的員工能輕易探索由 IT 部門建議和核准的豐富應用程式集合。 客戶只會看到他們建立之 Service Catalog 受管理的應用程式，或是組織中其他人已經與他們共用的受管理應用程式。
 
如需發行 Service Catalog 受管理應用程式的資訊，請參閱[建立和發行 Service Catalog 受管理的應用程式](managed-application-publishing.md)。
 
如需使用 Service Catalog 受管理應用程式的詳細資訊，請參閱[使用 Service Catalog 受管理的應用程式](managed-application-consumption.md)。
 
### <a name="marketplace"></a>Marketplace

可透過 Azure 入口網站的 Marketplace 取得的受管理應用程式。 廠商發行之後，這些應用程式便可供您組織內外的所有人使用。 此方式讓 MSP、ISV 和系統整合業者 (SI) 能提供解決方案給所有 Azure 客戶。 客戶獲得的好處是能利用這類複雜的解決方案，而不需要投資理解和維護解決方案。 目前，發行者可以讓他們的供應項目提供為受管理的應用程式，或是不受管理的解決方案範本。 發行受管理的應用程式，主要元件包括範本檔案 (描述佈建的資源)，以及 UI 定義檔案 (描述如何將佈建這些資源所需的輸入顯示在入口網站)。 必要的檔案會封裝為 .zip 檔案，並透過發行入口網站上傳。
 
如需發行受管理的應用程式到 Marketplace 的資訊，請參閱 [Marketplace 中 Azure 受管理的應用程式](managed-application-author-marketplace.md)。

如需從 Marketplace 使用受管理應用程式的詳細資訊，請參閱[在 Marketplace 中使用 Azure 受管理的應用程式](managed-application-consume-marketplace.md)。

## <a name="key-concepts"></a>重要概念

### <a name="managed-resource-group"></a>受管理的資源群組
會建立資源群組，當中包含所有要佈建在範本中的 Azure 資源。 例如，如果設備要建立儲存體帳戶，此資源群組就會包含儲存體帳戶資源。 它不包含設備資源。

### <a name="appliance-package"></a>設備套件
發行者建立的套件中，會包含範本檔案和 createUIDefinition 檔案。 具體來說，它包含下列檔案：

- **applianceMainTemplate.json** - 定義由設備佈建之所有資源的範本檔案。 這個檔案是用來建立資源的一般範本檔案。

- **MainTemplate.json** - 定義設備資源 (Microsoft.Solutions/appliances) 的範本檔案。 這項資源中所定義的一個金鑰屬性是 ManagedResourceGroupId。 這個屬性會指出要使用哪一個資源群組來裝載 applianceMainTemplate.json 中所定義的實際資源。

- **applianceCreateUIDefinition.json** - 這個檔案會描述範本中定義的參數所需之 UI 的呈現方式。

### <a name="authorization"></a>Authorization
發行者必須指定廠商所需的權限，才能代表客戶管理資源。 此權限會套用至受管理的資源群組。 您要設定下列值：

- **PrincipalID** - 使用者、群組或應用程式的 Azure AD 識別碼，可用來授與受管理資源群組的存取權。 這個識別碼是屬於發行者的租用戶。

- **RoleDefinitionID** - 指派給先前主體識別碼的角色之 Azure AD 識別碼。 可能是發行者租用戶中的任何內建 RBAC 角色。 如需詳細資訊，請參閱 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md)。

## <a name="next-steps"></a>後續步驟

* 如需發行受管理的應用程式到 Marketplace 的資訊，請參閱 [Marketplace 中 Azure 受管理的應用程式](managed-application-author-marketplace.md)。
* 如需從 Marketplace 使用受管理應用程式的詳細資訊，請參閱[在 Marketplace 中使用 Azure 受管理的應用程式](managed-application-consume-marketplace.md)。
* 如需發行 Service Catalog 受管理應用程式的資訊，請參閱[建立和發行 Service Catalog 受管理的應用程式](managed-application-publishing.md)。
* 如需使用 Service Catalog 受管理應用程式的詳細資訊，請參閱[使用 Service Catalog 受管理的應用程式](managed-application-consumption.md)。
* 若要建立 UI 定義檔，請參閱[開始使用 CreateUiDefinition](managed-application-createuidefinition-overview.md)。
