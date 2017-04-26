---
title: "了解 Azure AD 應用程式 Proxy 連接器 | Microsoft Docs"
description: "涵蓋 Azure AD 應用程式 Proxy 連接器的基本概念。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 5780c56a05ce1c40500927dec9df7906b02a1d13
ms.lasthandoff: 04/07/2017


---

# <a name="understand-azure-ad-application-proxy-connectors"></a>了解 Azure AD 應用程式 Proxy 連接器

連接器是讓 Azure AD 應用程式 Proxy 運作的關鍵。 它們很簡單、容易部署及維護且超級強大。 本文討論什麼是連接器，其運作方式，以及充分運用部署的一些最佳做法。 

## <a name="connector-deployment"></a>連接器部署

須在您的網路上安裝稱為連接器的 Windows Server 服務之後，應用程式 Proxy 才會運作。 您可以根據您的高可用性和延展性需求安裝多個連接器。 以其中一種開始，並視需要新增更多。 每次安裝連接器時，它會新增至做為您租用戶的連接器集區。

我們建議不要在相同伺服器上安裝連線器和裝載您的應用程式。

您不必手動刪除未使用的連接器。 當連接器執行時，它在連接到服務時會保持作用中。 未使用的連接器會標記為_非作用中_，且將在未作用 10 天之後移除。 

## <a name="connector-maintenance"></a>連接器維護
連接器和服務會負責所有高可用性的工作。 它們可以動態新增或移除。 每當新要求抵達時，它會路由傳送至其中一個目前可用的連接器。 如果連接器暫時無法使用，則不會回應此流量。

除了服務設定和驗證此連接器之憑證的連線之外，連接器是無狀態且在電腦上沒有組態資料。 當它們連線至服務時，會提取所有必要的組態資料，且每隔幾分鐘會重新整理。
它們也會輪詢伺服器以尋找是否有較新版的連接器。 如果找到，連接器會自行更新。

您可以從執行連接器的電腦 (使用事件記錄和效能計數器)，或從 Azure 入口網站的 [應用程式 Proxy] 頁面，監視您的連接器。

 ![Azure AD 應用程式 Proxy 連接器](./media/application-proxy-understand-connectors/app-proxy-connectors.png)

## <a name="all-networking-is-outbound"></a>所有的網路為輸出
連接器只會傳送輸出要求，因此連接一律由連接器啟動。 不需要開啟輸入連接埠，因為建立工作階段後，流量就會雙向流動。

輸出流量會傳送到應用程式 Proxy 服務和已發佈應用程式。 服務的流量會傳送至 Azure 資料中心的多個不同通訊埠編號。 如需使用哪些連接埠的詳細資訊，請參閱[在 Azure 入口網站中啟用應用程式 Proxy](active-directory-application-proxy-enable.md)。

由於僅有輸出流量，不需要設定連接器之間的負載平衡，或透過您的防火牆設定內部存取。

如需設定輸出防火牆規則的相關資訊，請參閱[使用現有的內部部署 Proxy 伺服器](application-proxy-working-with-proxy-servers.md)。

請使用 [Azure AD 應用程式 Proxy 連接器連接埠測試工具](https://aadap-portcheck.connectorporttest.msappproxy.net/)，來確認您的連接器是否能夠連線到「應用程式 Proxy」服務。 至少，請確定「美國中部」區域及離您最近的區域都具有綠色勾選記號。 除此之外，綠色勾選記號越多代表恢復能力越佳。 

## <a name="network-security"></a>網路安全性

連接器可以安裝在允許它們將要求傳送至服務和後端應用程式的網路上任何地方。 將它們安裝在公司內、非軍事區 (DMZ) 內，甚至在虛擬機器上，都可以正常運作。 重點是執行連接器的電腦也可存取您的應用程式。

DMZ 部署較為複雜。 您可能想要將連接器部署在 DMZ，其中一個理由是為了使用其他基礎結構，例如後端應用程式負載平衡器或入侵偵測系統。

## <a name="domain-joining"></a>加入網域

連接器可以在未加入網域的電腦上執行。 不過，如果您想要對使用整合式 Windows 驗證 (IWA) 的應用程式執行單一登入 (SSO)，則需要已加入網域的電腦。 在此情況下，連接器電腦必須代表已發佈應用程式的相關使用者加入可以執行 [Kerberos](https://web.mit.edu/kerberos) 限制委派的網域。

連接器也可以加入具有部分信任的網域或樹系，或加入唯讀網域控制站。

## <a name="connectors-on-hardened-environments"></a>強化環境上的連接器

連接器部署通常都直截了當，不需要特殊組態。 不過，應該考量一些獨特的情況︰

* 限制傳出流量的組織必須[開啟必要的連接埠](active-directory-application-proxy-enable.md#open-your-ports)。
* 符合 FIPS 規範的電腦可能需要變更其設定，以允許連接器服務、連接器更新程式服務及其安裝程式產生憑證並儲存該電腦上。
* 根據發出網路要求的處理程序鎖定其環境的組織，必須確定已啟用兩個連接器服務才可存取所有必要的連接埠和 IP。
* 在某些情況下，輸出的正向 Proxy 可能會中斷雙向憑證驗證，並造成失敗的通訊。

## <a name="connector-authentication"></a>連接器驗證

為了提供安全服務，連接器必須向服務驗證，且服務必須向連接器驗證。 可在連接器初始化連線時，使用用戶端和伺服器憑證來完成此動作。 如此一來，系統管理員的使用者名稱和密碼不會儲存在連接器電腦上。

使用的憑證是特定於應用程式 Proxy 服務。 它們會在初始註冊期間建立，且每隔幾個月會由連接器自動更新。 

如果連接器有數個月未連線至服務，它的憑證可能會過期。 在此情況下，請解除安裝並重新安裝連接器以觸發註冊。 您可以執行下列 PowerShell 命令：

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

## <a name="performance-and-scalability"></a>效能和延展性

即使線上服務的規模很明顯，但規模是連接器的一項因素。 您需要有足夠的連接器，以處理尖峰流量。 因為連接器是無狀態，所以不受使用者或工作階段的數目所影響。 相反地，它們會依要求數目和其承載大小而作出反應。 以標準 Web 流量而言，一台普通電腦每秒可以處理幾千個要求。 具體產能取決於確切的電腦特性。

連接器效能受限於 CPU 和網路。 CPU 效能需要 SSL 加密和解密，而網路對於取得應用程式和 Azure 線上服務的快速連線很重要。 

相反地，連接器發行需要較少的記憶體。 線上服務可以處理大部分的處理程序，及所有未經驗證的流量。 所有可在雲端中完成的內容都是在雲端中完成。

影響效能的另一個因素是連接器之間的網路品質，包括︰

* **線上服務︰**慢速或延遲嚴重的連線會影響連接器服務。 您的組織最好是透過 Express Route 連線到 Azure。 否則，請網路小組確定與 Azure 的連線是以有效的方式處理。  
* **後端應用程式︰**在某些情況下，連接器和後端應用程式之間有其他 Proxy。 從連接器電腦開啟瀏覽器，並存取這些應用程式，以針對這個問題進行疑難排解。 如果您在 Azure 中執行連接器，且應用程式在內部部署，則體驗可能無法如您的使用者所預期。
* **網域控制器︰**如果連接器使用 Kerberos 限制委派 (KCD) 來執行 SSO，它們會先連絡網域控制器後，才傳送要求至後端。 連接器有 Kerberos 票證的快取 (但是在忙碌環境中)，網域控制器的回應速度可能會影響體驗。 當網域控制器為內部部署時，在 Azure 中執行的連接器會更常發生這個問題。

## <a name="automatic-updates-to-the-connector"></a>自動更新至連接器

利用連接器更新程式服務，我們提供自動化的方式來維持最新狀態。 如此一來，您可持續擁有所有新功能的優點，以及安全性和效能增強功能。

Azure AD 支援您部署之所有連接器的自動更新。 只要應用程式 Proxy 連接器更新程式服務正在執行，您的連接器便會自動更新。 如果您在伺服器上沒有看到連接器更新程式服務，則需要[重新安裝您的連接器](active-directory-application-proxy-enable.md)以取得任何更新。

如果是下列情況，連接器更新時可能會遭遇停機︰

- 您只有一個連接器。 因為沒有任何其他連接器可重新路由傳送流量，所以服務在更新期間無法使用。 若要避免這種停機情形並改善高可用性，建議您安裝第二個連接器，然後[建立連接器群組](active-directory-application-proxy-connectors-azure-portal.md)。

- 更新開始時，連接器正處於交易中途。 您的瀏覽器應該會自動重試作業，或者，您可以重新整理頁面。 當重新傳送要求時，流量會路由傳送至備份連接器。

## <a name="under-the-hood"></a>幕後

連接器是以 Windows Server Web 應用程式 Proxy 為基礎，因此有大部分相同的管理工具，包括 Windows 事件記錄

 ![使用事件檢視器](./media/application-proxy-understand-connectors/event-view-window.png)

和 Windows 效能計數器管理事件記錄。 

 ![使用效能監視器將計數器新增至連接器](./media/application-proxy-understand-connectors/performance-monitor.png)

連接器有系統管理員和工作階段記錄檔。 系統管理記錄檔包含索引鍵事件和其錯誤。 這些工作階段記錄檔包含所有交易，以及它們的處理詳細資料。 

若要查看記錄，請移至事件檢視器，開啟 [檢視] 功能表，並啟用 [顯示分析與偵錯記錄]。 接著，啟用它們以開始收集事件。 這些記錄不會出現在 Windows Server 2012 R2 中的 Web 應用程式 Proxy，因為連接器會根據較新的版本。

您可以檢查 [服務] 視窗中的服務狀態。 連接器包含兩個 Windows 服務︰實際連接器和更新程式。 必須具有這兩個才能隨時執行。

 ![AzureAD 服務本機](./media/application-proxy-understand-connectors/aad-connector-services.png)

## <a name="next-steps"></a>後續步驟
[針對應用程式 Proxy 和連接器錯誤進行疑難排解](active-directory-application-proxy-troubleshoot.md)

[使用現有的內部部署 Proxy 伺服器](application-proxy-working-with-proxy-servers.md)

[如何以無訊息方式安裝 Azure AD 應用程式 Proxy 連接器](active-directory-application-proxy-silent-installation.md)


