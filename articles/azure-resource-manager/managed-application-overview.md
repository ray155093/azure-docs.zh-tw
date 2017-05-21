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
ms.date: 05/11/2017
ms.author: gauravbh; tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: a4348a4d2348d744c03ad3a89d0548526fa2f9f8
ms.contentlocale: zh-tw
ms.lasthandoff: 05/11/2017


---
# <a name="azure-managed-applications-overview"></a>Azure 受管理的應用程式概觀

目前，Azure 提供健全的 Marketplace，讓 ISV 和新創公司能夠將他們的解決方案提供給世界各地的客戶。 Azure Marketplace 是一個資源庫，包含第一方和第三方廠商的數百個複雜、多資源範本。 客戶可以在幾分鐘內部署並開始使用 PaaS 和 SaaS 應用程式。 雖然它提供能快速部署產品的絕佳方式，客戶仍必須負責維護和更新解決方案。 對廠商而言，當客戶使用應用程式超出虛擬機器映像計費時，並無法向客戶收費。 此外，廠商無法防止客戶修改重大的應用程式資源，且無法封鎖對組成應用程式的智慧財產之存取。 Azure 受管理的應用程式針對這些問題提供了解決方案。 

## <a name="advantages-of-managed-applications"></a>受管理應用程式的優點

Azure 受管理應用程式所提供的生態系統，可讓廠商提供 PaaS 或 SaaS 服務作為獨立的應用程式。 客戶會在其訂用帳戶中部署受管理的應用程式，而廠商可以進行管理。 它可讓廠商使用 Azure 的計費系統向客戶寄送帳單，使用範本來管理已部署應用程式的生命週期。 在另一端，它讓客戶能夠自動取得更新，並支付支援和維護的費用。 它們不需要自行維護或更新應用程式，也不必在應用程式失敗時診斷及修正問題。

不只有 PaaS 和 SaaS 廠商可受益於 Azure 中的這類生態系統，公司中央平台小組和想要封裝並轉售解決方案的系統整合者也都能有所受益。

## <a name="how-managed-applications-work"></a>受管理應用程式的運作方式
使用受管理的應用程式時，有兩種體驗︰

1. 廠商或獨立軟體廠商 (ISV) 會建立受管理的應用程式，並提供更廣泛的用途。 
2. 客戶或取用者想要建立及使用發佈的應用程式。 

本文將探討這兩種體驗。 首先，我們來了解受管理應用程式的運作方式。 

受管理的應用程式與 Marketplace 解決方案範本類似，只有一個主要差異。 在受管理的應用程式中，資源會佈建到 ISV/廠商所管理的資源群組。 資源群組存在於客戶的訂用帳戶，但使用者、使用者群組或 ISV 租用戶中的應用程式可以存取資源群組。 為管理和服務應用程式，會將廠商的身分識別新增至 Active Directory 擁有者、參與者、讀取者或任何其他內建的角色。 

## <a name="key-concepts"></a>重要概念

### <a name="managed-resource-group"></a>受管理的資源群組
會建立資源群組，當中包含所有要佈建在範本中的 Azure 資源。 例如，如果設備要建立儲存體帳戶，此資源群組就會包含儲存體帳戶資源。 它不包含設備資源。

### <a name="appliance-package"></a>設備套件
發行者建立的套件中，會包含範本檔案和 createUIDefinition 檔案。 具體來說，它包含下列檔案：

- **applianceMainTemplate.json** - 定義由設備佈建之所有資源的範本檔案。 這個檔案是用來建立資源的一般範本檔案。

- **MainTemplate.json** - 定義設備資源 (Microsoft.Solutions/appliances) 的範本檔案。 這項資源中所定義的一個金鑰屬性是 ManagedResourceGroupId。 這個屬性會指出要使用哪一個資源群組來裝載 applianceMainTemplate.json 中所定義的實際資源。

- **createUIDefinition.json** - 這個檔案會描述範本中定義的參數所需之 UI 的呈現方式。

### <a name="authorization"></a>Authorization
發行者必須指定廠商所需的權限，才能代表客戶管理資源。 此權限會套用至受管理的資源群組。 您要設定下列值：

- **PrincipalID** - 使用者、群組或應用程式的 Azure AD 識別碼，可用來授與受管理資源群組的存取權。 這個識別碼是屬於發行者的租用戶。

- **RoleDefinitionID** - 指派給先前主體識別碼的角色之 Azure AD 識別碼。 可能是發行者租用戶中的任何內建 RBAC 角色。

## <a name="next-steps"></a>後續步驟

* 若要了解廠商體驗，請參閱[建立及發佈 Azure 受管理應用程式](managed-application-publishing.md)。
* 若要了解取用者體驗，請參閱[使用 Azure 受管理的應用程式](managed-application-consumption.md)。
* 若要建立 UI 定義檔，請參閱[開始使用 CreateUiDefinition](managed-application-createuidefinition-overview.md)。
