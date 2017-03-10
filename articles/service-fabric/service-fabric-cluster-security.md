---
title: "保護 Service Fabric 叢集 | Microsoft Docs"
description: "說明 Service Fabric 叢集的安全性案例，以及用來實作這些案例的各種不同技術。"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 26b58724-6a43-4f20-b965-2da3f086cf8a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/08/2017
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: 628df1df8f5de99a5c18d0df5b7ee41e2fb747df
ms.openlocfilehash: c3ff370b105a1f9bdacd1bdb4b32d6209e150be2
ms.lasthandoff: 12/08/2016


---
# <a name="service-fabric-cluster-security-scenarios"></a>Service Fabric 叢集安全性案例
Service Fabric 叢集是您所擁有的資源。 叢集必須受到安全保護，以防止未經授權使用者連線您的叢集，特別是當叢集上有生產工作負載正在執行時。 雖然可以建立不安全的叢集，但這樣做會在叢集向公用網際網路公開管理端點時，允許匿名使用者連線叢集。 

本文針對 Azure 或獨立伺服器上執行之叢集的安全性案例，以及用來實作這些案例的各種技術，提供概觀。 叢集安全性案例包括：

* 節點對節點安全性
* 用戶端對節點安全性
* 角色型存取控制 (RBAC)

## <a name="node-to-node-security"></a>節點對節點安全性
保護叢集中 VM 與機器之間的通訊。 這可確保只有獲得授權加入叢集的電腦可以參與裝載應用程式和叢集中的服務。

![節點對節點通訊的圖表][Node-to-Node]

在 Azure 上執行的叢集或在 Windows 上執行的獨立叢集可以使用[憑證安全性](https://msdn.microsoft.com/library/ff649801.aspx)或針對 Windows Server 機器使用 [Windows 安全性](https://msdn.microsoft.com/library/ff649396.aspx)。

### <a name="node-to-node-certificate-security"></a>節點對節點憑證安全性
Service Fabric 會使用您建立叢集時在節點類型組態中指定的 X.509 伺服器憑證。 本文結尾處會提供這些憑證是什麼，以及要如何取得或建立的快速概觀。

憑證安全性是在建立叢集時設定，透過 Azure 入口網站、Azure Resource Manager 範本或獨立 JSON 範本。 您可以指定用於憑證變換的主要憑證和選用次要憑證。 您指定的主要和次要憑證，應該不同於您為 [用戶端對節點安全性](#client-to-node-security)指定的系統管理用戶端憑證和唯讀用戶端憑證。

對於 Azure，若要了解如何在叢集中設定憑證安全性，請參閱 [使用 Azure Resource Manager 範本來設定叢集](service-fabric-cluster-creation-via-arm.md) 。

對於獨立 Windows Server，請參閱 [使用 X.509 憑證保護 Windows 上的獨立叢集 ](service-fabric-windows-cluster-x509-security.md)

### <a name="node-to-node-windows-security"></a>節點對節點 Windows 安全性
對於獨立 Windows Server，請參閱 [使用 Windows 安全性保護 Windows 上的獨立叢集](service-fabric-windows-cluster-windows-security.md)

## <a name="client-to-node-security"></a>用戶端對節點安全性
驗證用戶端並保護用戶端與叢集中個別節點之間的通訊。 這個類型的安全性會驗證並保護用戶端通訊，確保只有獲得授權的使用者可以存取叢集與叢集上部署的應用程式。 用戶端是透過其 Windows 安全性認證或其憑證安全性認證唯一識別。

![用戶端對節點通訊的圖表][Client-to-Node]

在 Azure 上執行的叢集或在 Windows 上執行的獨立叢集可以使用[憑證安全性](https://msdn.microsoft.com/library/ff649801.aspx)或 [Windows 安全性](https://msdn.microsoft.com/library/ff649396.aspx)。

### <a name="client-to-node-certificate-security"></a>用戶端對節點憑證安全性
 用戶端對節點憑證安全性是在建立叢集 (透過 Azure 入口網站、Resource Manager 範本或 JSON 範本) 時，藉由指定系統管理用戶端憑證和 (或) 使用者用戶端憑證來設定的。  您指定的系統管理用戶端憑證和使用者用戶端憑證，應該不同於您為 [節點對節點安全性](#node-to-node-security)指定的主要和次要憑證。

用戶端如果是使用系統管理憑證來連接到叢集，就會擁有管理功能的完整存取權。  用戶端如果是使用唯讀使用者用戶端憑證來連接到叢集，則只會擁有管理功能的唯讀存取權。 換句話說，這些憑證是用於下文所述的角色型存取控制 (RBAC)。

對於 Azure，若要了解如何在叢集中設定憑證安全性，請參閱 [使用 Azure Resource Manager 範本來設定叢集](service-fabric-cluster-creation-via-arm.md) 。

對於獨立 Windows Server，請參閱 [使用 X.509 憑證保護 Windows 上的獨立叢集 ](service-fabric-windows-cluster-x509-security.md)

### <a name="client-to-node-azure-active-directory-aad-security-on-azure"></a>Azure 上的用戶端對節點 Azure Active Directory (AAD)
在 Azure 上執行的叢集也可以使用 Azure Active Directory (AAD) 來保護對管理端點的存取。 若要了解如何建立必要的 AAD 構件、如何在建立叢集時填入這些構件，以及之後如何連接到這些叢集，請參閱 [使用 Azure Resource Manager 範本來設定叢集](service-fabric-cluster-creation-via-arm.md) 。

## <a name="security-recommendations"></a>安全性建議
對於 Azure 叢集，建議您針對節點對節點安全性使用 AAD 安全性來驗證用戶端和憑證。

對於獨立 Windows Server 叢集，如果您有 Windows Server 2012 R2 和 Active Directory，建議您使用 Windows 安全性與群組管理帳戶 (GMA)。 否則仍然使用 Windows 安全性與 Windows 帳戶。

## <a name="role-based-access-control-rbac"></a>角色型存取控制 (RBAC)
存取控制可讓叢集系統管理員針對不同的使用者群組限制特定叢集作業的存取權，讓叢集更加安全。 針對連接到叢集的用戶端，支援兩種不同的存取控制類型：系統管理員角色和使用者角色。

系統管理員可以完整存取管理功能 (包括讀取/寫入功能)。 使用者預設只具有管理功能的讀取存取權 (例如查詢功能)，以及解析應用程式和服務的能力。

您可以在建立叢集時，分別為系統管理員和使用者用戶端角色提供個別的身分識別 (憑證、AAD 等等)，來指定這兩個角色。 如需有關預設存取控制設定及如何變更預設設定的詳細資訊，請參閱[適用於 Service Fabric 用戶端的角色型存取控制](service-fabric-cluster-security-roles.md)。

## <a name="x509-certificates-and-service-fabric"></a>X.509 憑證和 Service Fabric
X509 數位憑證通常用來驗證用戶端與伺服器，以及加密及數位簽署訊息。 如需有關這些憑證的詳細資料，請移至 [使用憑證](http://msdn.microsoft.com/library/ms731899.aspx)。

一些需要考量的重要事項︰

* 執行生產環境工作負載之叢集中所使用的憑證應該是使用已正確設定的 Windows Server 憑證服務來建立，或是從已核准的 [憑證授權單位 (CA)](https://en.wikipedia.org/wiki/Certificate_authority)取得。
* 絕對不要在生產環境中使用以 MakeCert.exe 這類工具建立的暫時或測試憑證。
* 您可以使用自我簽署憑證，但應該只用於測試叢集，不應該在生產環境中使用。

### <a name="server-x509-certificates"></a>伺服器的 X.509 憑證
伺服器憑證的主要工作是向用戶端驗證伺服器 (節點) 或是向伺服器 (節點) 驗證伺服器 (節點)。 用戶端或節點驗證節點時的其中一項初始檢查是檢查 [主體] 欄位中的一般名稱值。 此一般名稱或其中一個憑證主體別名必須存在於允許的一般名稱清單中。

下文說明如何產生具有主體別名 (SAN) 的憑證： [如何為安全的 LDAP 憑證新增主體別名](http://support.microsoft.com/kb/931351)。

[主體] 欄位可以包含數個值，而每個值的前面會加上代表該值類型的起首字母。 最常見的起首字母是 "CN" 代表一般名稱，例如 "CN = www.contoso.com"。 [主體] 欄位也可能空白。 如果選擇性 [主體別名] 欄位已填入資料，此欄位必須包含憑證的一般名稱，以及每個主體別名的一個項目。 這些會被輸入為 DNS 名稱值。

憑證的 [預定目的] 欄位值應包含適當的值，例如「伺服器驗證」或「用戶端驗證」。

### <a name="client-x509-certificates"></a>用戶端 X.509 憑證
用戶端憑證通常不是由第三方憑證授權單位所發行。 然而，目前使用者位置的個人存放區通常包含由根授權單位所放置的用戶端憑證，其預定目的為「用戶端驗證」。 用戶端可以在需要相互驗證時使用這類憑證。

> [!NOTE]
> Service Fabric 叢集上的所有管理作業都需要伺服器憑證。 用戶端憑證無法用於管理作業。
> 
> 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->


## <a name="next-steps"></a>後續步驟
本文提供與叢集安全性有關的概念資訊。 接下來，


1.  [使用 Resource Manager 範本在 Azure 中建立叢集](service-fabric-cluster-creation-via-arm.md) 
2.  [Azure 入口網站](service-fabric-cluster-creation-via-portal.md)。

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

