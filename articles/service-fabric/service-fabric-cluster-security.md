<properties
   pageTitle="保護 Service Fabric 叢集 | Microsoft Azure"
   description="說明 Service Fabric 叢集的安全性案例，以及用來實作這些案例的各種不同技術。"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/01/2016"
   ms.author="chackdan"/>

# Service Fabric 叢集安全性案例

Service Fabric 叢集是您所擁有的資源。為了防止未經授權的資源存取，您必須保護其安全性，尤其是當其中有執行中的生產工作負載時。本文針對 Azure 和 Windows 伺服器上執行之叢集的安全性案例，以及用來實作這些案例的各種技術，提供概觀。叢集安全性案例包括：

- 節點對節點安全性
- 用戶端對節點安全性
- 角色型存取控制 (RBAC)

## 節點對節點安全性
保護叢集中 VM 與電腦之間的通訊。這可確保只有獲得授權加入叢集的電腦可以參與裝載應用程式和叢集中的服務。

![節點對節點通訊的圖表][Node-to-Node]

在 Azure 上執行的叢集或在 Windows 上執行的獨立叢集可以使用[憑證安全性](https://msdn.microsoft.com/library/ff649801.aspx)或 [Windows 安全性](https://msdn.microsoft.com/library/ff649396.aspx)。憑證安全性是在建立叢集 (透過「Azure 入口網站」或 ARM 範本) 時，藉由指定主要憑證及視需要指定次要憑證來設定的。您指定的主要和次要憑證，應該不同於您為[用戶端對節點安全性](#client-to-node-security)指定的系統管理用戶端憑證和唯讀用戶端憑證。若要了解如何在於 Azure 上執行的叢集中設定憑證安全性，請參閱[使用憑證保護 Azure 上的 Service Fabric 叢集](service-fabric-secure-azure-cluster-with-certs.md)或[使用 ARM 範本來設定叢集](service-fabric-cluster-creation-via-arm.md)。

## 用戶端對節點安全性
驗證用戶端並保護用戶端與叢集中個別節點之間的通訊。這個類型的安全性會驗證並保護用戶端通訊，確保只有獲得授權的使用者可以存取叢集與叢集上部署的應用程式。用戶端是透過其 Windows 安全性認證或其憑證安全性認證唯一識別。

![用戶端對節點通訊的圖表][Client-to-Node]

在 Azure 上執行的叢集或在 Windows 上執行的獨立叢集可以使用[憑證安全性](https://msdn.microsoft.com/library/ff649801.aspx)或 [Windows 安全性](https://msdn.microsoft.com/library/ff649396.aspx)。用戶端對節點憑證安全性是在建立叢集 (透過「Azure 入口網站」或 ARM 範本) 時，藉由指定系統管理用戶端憑證和 (或) 唯讀用戶端憑證來設定的。您指定的系統管理用戶端憑證和唯讀用戶端憑證，應該不同於您為[節點對節點安全性](#node-to-node-security)指定的主要和次要憑證。用戶端如果是使用系統管理憑證或主要憑證來連接到叢集，就會擁有管理功能的完整存取權。用戶端如果是使用唯讀用戶端憑證來連接到叢集，則只會擁有管理功能的唯讀存取權。若要了解如何在於 Azure 上執行的叢集中設定憑證安全性，請參閱[使用憑證保護 Azure 上的 Service Fabric 叢集](service-fabric-secure-azure-cluster-with-certs.md)或[使用 ARM 範本來設定叢集](service-fabric-cluster-creation-via-arm.md)。

Service Fabric 會使用您建立叢集時在節點類型組態中指定的 X.509 伺服器憑證。本文結尾處會提供這些憑證是什麼，以及要如何取得或建立的快速概觀。

在 Azure 上執行的叢集也可以使用 Azure Active Directory (AAD) 來保護對管理端點的存取。若要了解如何建立必要的 AAD 構件、如何在建立叢集時填入這些構件，以及之後如何連接到這些叢集，請參閱[建立使用 Azure Active Directory 進行用戶端驗證的 Service Fabric 叢集](service-fabric-cluster-security-client-auth-with-aad.md)。

## 角色型存取控制 (RBAC)
存取控制可讓叢集系統管理員針對不同的使用者群組限制特定叢集作業的存取權，讓叢集更加安全。針對連接到叢集的用戶端，支援兩種不同的存取控制類型：系統管理員和使用者。系統管理員可以完整存取管理功能 (包括讀取/寫入功能)。使用者預設只具有管理功能的讀取存取權 (例如查詢功能)，以及解析應用程式和服務的能力。您可以在建立叢集時，分別為系統管理員和使用者用戶端角色提供個別的憑證，來指定這兩個角色。如需有關預設存取控制設定及如何變更預設設定的詳細資訊，請參閱[適用於用戶端的角色型存取控制](service-fabric-cluster-security-roles.md)。


## X.509 憑證和 Service Fabric
X509 數位憑證通常用來驗證用戶端與伺服器，以及加密及數位簽署訊息。如需有關這些憑證的詳細資料，請移至[使用憑證](http://msdn.microsoft.com/library/ms731899.aspx)。

一些需要考量的重要事項︰

- 執行生產環境工作負載之叢集中所使用的憑證應該是使用已正確設定的 Windows Server 憑證服務來建立，或是從已核准的[憑證授權單位 (CA)](https://en.wikipedia.org/wiki/Certificate_authority) 取得。
- 絕對不要在生產環境中使用以 MakeCert.exe 這類工具建立的暫時或測試憑證。
- 您可以使用自我簽署憑證，但應該只用於測試叢集，不應該在生產環境中使用。

### 伺服器的 X.509 憑證

伺服器憑證的主要工作是向用戶端驗證伺服器 (節點) 或是向伺服器 (節點) 驗證伺服器 (節點)。用戶端或節點驗證節點時的其中一項初始檢查是檢查 [主體] 欄位中的一般名稱值。此一般名稱或其中一個憑證主體別名必須存在於允許的一般名稱清單中。

下文說明如何產生具有主體別名 (SAN) 的憑證：[如何為安全的 LDAP 憑證新增主體別名](http://support.microsoft.com/kb/931351)。

[主體] 欄位可以包含數個值，而每個值的前面會加上代表該值類型的起首字母。最常見的起首字母是 "CN" 代表一般名稱，例如 "CN = www.contoso.com"。[主體] 欄位也可能空白。如果選擇性 [主體別名] 欄位已填入資料，此欄位必須包含憑證的一般名稱，以及每個主體別名的一個項目。這些會被輸入為 DNS 名稱值。

憑證的 [預定目的] 欄位值應包含適當的值，例如「伺服器驗證」或「用戶端驗證」。

### 用戶端 X.509 憑證

用戶端憑證通常不是由第三方憑證授權單位所發行。然而，目前使用者位置的個人存放區通常包含由根授權單位所放置的用戶端憑證，其預定目的為「用戶端驗證」。用戶端可以在需要相互驗證時使用這類憑證。

>[AZURE.NOTE] Service Fabric 叢集上的所有管理作業都需要伺服器憑證。用戶端憑證無法用於管理作業。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->


## 後續步驟
在您設定好叢集之後，可以了解叢集升級：

- [Service Fabric 叢集升級程序與期望](service-fabric-cluster-upgrade.md)

深入了解應用程式安全性：

- [應用程式安全性及 RunAs](service-fabric-application-runas-security.md)

- [安全服務通訊](service-fabric-reliable-services-secure-communication.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

<!---HONumber=AcomDC_0608_2016-->