---
title: "使用 Azure AD 應用程式 Proxy 時的網路拓撲考量 | Microsoft Docs"
description: "涵蓋使用 Azure AD 應用程式 Proxy 時的網路拓撲考量。"
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
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 1fdc00865f31352026a64c2769d9d65047bb2fa6
ms.openlocfilehash: 26ec9167f1f883c3c71947a55142020fb48e206a


---

# <a name="network-topology-considerations-when-using-azure-ad-application-proxy"></a>使用 Azure AD 應用程式 Proxy 時的網路拓撲考量
> [!NOTE]
> 應用程式 Proxy 是您升級至 Premium 或 Basic 版本的 Azure Active Directory 時才能使用的功能。 如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。
> 

本文說明使用 Azure AD 應用程式 Proxy 遠端發佈及存取您的應用程式時的網路拓撲考量。 

## <a name="traffic-flow"></a>流量

當應用程式透過 Azure AD App Proxy 發佈時，所有來自使用者往目標後端應用程式的流量會流過下列躍點︰

* 躍點 1︰使用者至 Azure 上的 Azure AD App Proxy 服務的公用端點
* 躍點 2︰應用程式 Proxy 服務至連接器
* 躍點 3︰連接器至目標應用程式

 ![AzureAD Iaas 多雲端廠商](./media/application-proxy-network-topologies/application-proxy-three-hops.png)

## <a name="tenant-location-and-app-proxy-service"></a>租用戶位置與應用程式 Proxy 服務

當您註冊 Azure AD 租用戶時，您租用戶 (美國、EMEA、APAC 等) 的區域取決於您所指定的國家/地區。 當您啟用應用程式 Proxy 時，租用戶的應用程式 Proxy 服務執行個體會顯示在與 Azure AD 租用戶位於相同區域或最近的區域。 

例如，如果您 Azure AD 租用戶的區域是歐盟 (EU)，則所有的 Azure AD App Proxy 連接器將連線到歐盟 Azure 資料中心的應用程式 Proxy 服務執行個體。 這也表示，所有的使用者在嘗試存取已發佈應用程式時，會通過這個位置的應用程式 Proxy 服務執行個體。

## <a name="considerations-for-reducing-latency"></a>降低延遲的考量

所有 Proxy 解決方案都會在您的網路連線中造成延遲。 不論您選擇做為遠端存取解決方案的是哪一個 Proxy 或 VPN 解決方案，它一定會包含一組伺服器，啟用您公司網路內部的連接。 

公司通常會在他們的網路隔離區 (DMZ) 包含伺服器端點。 但使用 Azure AD App Proxy 時，則不需要 DMZ。  這是因為使用應用程式 Proxy，流量都會流過雲端中的 Proxy 服務，而連接器會位於公司的網路上。

### <a name="connector-placement"></a>連接器放置

應用程式 Proxy 服務會根據您的租用戶位置，為您選擇執行個體的位置。 因此，您可以決定在何處安裝連接器，讓您能夠定義網路流量的端對端延遲特性。

當設定應用程式 Proxy 服務時，以下是一些您會問到的問題︰

* 應用程式位於何處？
* 大多數存取應用程式的使用者位於哪裡？
* 應用程式 Proxy 執行個體所在的位置 (這是根據您的租用戶)？
* 您是否已有連線到 Azure 資料中心 (例如 Express Route 或類似的 VPN 設定) 的專用網路？

連接器的位置將會決定躍點 #2 和躍點 #3 的延遲。 評估連接器的位置時，您應該考慮下列因素：

* 當您要單一登入 (SSO) 至後端應用程式時，連接器需要直視資料中心才可執行 Kerberos 限制委派 (KCD) 作業。
* 通常會在較近的應用程式安裝連接器，以減少從連接器到應用程式的時間。

### <a name="general-approach-to-minimize-latency"></a>將延遲降至最低的常見方式

您可以最佳化每個網路躍點來嘗試並盡量減少端對端流量延遲，使流量可以流通。

可以最佳化每個躍點，方法為︰

* 減少躍點兩端之間的距離。
* 選擇正確的網路來周遊。 例如，周遊私人網路而非公用網際網路可能會因為專用的連結而更快。
 
如果您在 Azure 和您的公司網路之間有專用的 VPN/Express Route 連結，則您可加以運用。

## <a name="focus-your-optimizing-strategy"></a>專注於最佳化策略

因為您的使用者可能會透過網際網路從遠端存取應用程式，您應該一律專注於最佳化躍點 2 和 3。 以下是一些您可加入的常見模式。

### <a name="pattern-1-optimize-hop-3"></a>模式 #1︰最佳化躍點 #3︰

若要最佳化躍點 3，連接器會放在接近客戶網路中的目標應用程式。 這項操作的優點是連接器可能需要直視網域連接器，如先前所述。 這種方法對於大多數的客戶和案例通常已足夠。 大部分的客戶會遵循這個模式。

 ![AzureAD Iaas 多雲端廠商](./media/application-proxy-network-topologies/application-proxy-hop3.png)


> [!NOTE]
在某些情況下，您將需要最佳化躍點 #2 和躍點 #3，以取得您想要的延遲特性。 例如，如果您在網路與 Azure 資料中心之間有 VPN 或 ExpressRoute 的設定，這種情況可讓您在躍點 #3 之外最佳化躍點 #2。
>

### <a name="pattern-2-take-advantage-of-expressroute-with-public-peering"></a>模式 2︰利用 ExpressRoute 與公用對等互連

如果您有 ExpressRoute 設定與公用對等互連，我們將針對躍點 #2 利用更快速的 ExpressRoute 連線。 躍點 #3 已最佳化，方法為在客戶網路中將連接器放置接近應用程式。

![AzureAD Iaas 多雲端廠商](./media/application-proxy-network-topologies/application-proxy-expressroute-public.png)

### <a name="pattern-3-taking-advantage-expressroute-with-private-peering"></a>模式 #3︰利用 ExpressRoute 與私人對等互連

如果您有專用的 VPN 或 ExpressRoute 設定與 Azure 和已安裝應用程式的公司網路之間的私人對等互連，您會有另一個選項。 在此組態中，Azure 中的虛擬網路通常會被視為公司網路的擴充功能。 因此，您可以在 Azure 資料中心內安裝連接器，並仍能滿足躍點 #3 的連接器對應用程式連接之低延遲需求。 

延遲不會受到危害，因為流量會流經專用的連接。 不過，您會取得改善躍點 #2 延遲特性的額外好處。 這是因為連接器安裝在接近您 AAD 租用戶 (以及應用程式 Proxy) 的 Azure 資料中心位置時，Proxy 服務對連接器連接 (躍點 #2) 是較短的躍點。

![AzureAD Iaas 多雲端廠商](./media/application-proxy-network-topologies/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>其他方法

本文章到目前為止著重於連接器放置。 不過，如果移動應用程式是您的選項 (例如，到 Azure 或其他託管環境)，則可以變更應用程式的位置，以取得較佳的延遲特性。 

有愈來愈多的組織將其網路移至託管環境。 這可讓它們將自己的應用程式放在託管環境中，這也是其公司網路的一部分，並仍在網域內。 在此情況下，上述模式可以套用至新的應用程式位置。

請考慮使用連接器群組，以鎖定位於不同位置和網路的應用程式。 如果您正在考慮此選項，請參閱 [AD Domain Services](https://azure.microsoft.com/en-us/services/active-directory-ds)。 

## <a name="common-scenarios"></a>常見案例

在本節中，我們將逐步解說幾個使用案例。 在下列所有使用案例中，假設 Azure AD 租用戶 (以及 Proxy 服務端點) 位於美國。在全球各地的其他區域，通常會套用相同的考量。

### <a name="use-case-1"></a>使用案例 1

應用程式在美國客戶的網路，使用者在相同的區域中。 Azure DC 和公司網路之間沒有 ExpressRoute 或 VPN 存在。

**建議︰**遵循上述的使用案例 #1。 如需改良的延遲，請視需要考慮利用 ExpressRoute <請參閱案例 #3 和 #4>。

這是簡單的模式。 最常見的模式是最佳化躍點 #3，其中連接器放置在應用程式附近的位置。 這是也很自然的選擇，因為連接器通常安裝在直視應用程式及 DC 以執行 KCD 作業。
這個使用案例會遵循下列 #1 的模式。

![AzureAD Iaas 多雲端廠商](./media/application-proxy-network-topologies/application-proxy-pattern1.png)

### <a name="use-case-2"></a>使用案例 2

應用程式在美國客戶的網路，使用者則散佈在全球各地。 Azure DC 和公司網路之間沒有 ExpressRoute 或 VPN 存在。

**建議︰**遵循上述的使用案例 #2。 如需改良的延遲，請視需要考慮利用 ExpressRoute (請參閱案例 #3 和 #4)。

同樣地，常見的模式是最佳化躍點 #3，其中連接器基於上述原因會放置在應用程式附近的位置。 躍點 #3 通常不是高成本，如果全部都在相同區域內。 不過，躍點 #1 可能會根據使用者所在的位置而更高成本，因為所有的使用者會存取美國的應用程式 Proxy 執行個體。值得注意的是，這裡任何 Proxy 解決方案會有相似特性，相對於散佈於全球的使用者。

這個使用案例會遵循下列 #2 的模式。

![AzureAD Iaas 多雲端廠商](./media/application-proxy-network-topologies/application-proxy-pattern2.png)

### <a name="use-case-3"></a>使用案例 3

應用程式在美國的客戶網路。Azure 與公司網路之間存在 ExpressRoute 與公用對等互連。

**建議︰**盡可能將連接器放置接近應用程式。 系統會自動使用躍點 #2 的 ExpressRoute。 這會遵循上述的 #2 模式。

如果 ExpressRoute 連結是使用公用互連，則 Proxy 與連接器之間的流量會流過該連結，且躍點 #2 將最佳化延遲。

這個使用案例會遵循下列 #3 的模式。

![AzureAD Iaas 多雲端廠商](./media/application-proxy-network-topologies/application-proxy-pattern3.png)

### <a name="use-case-4"></a>使用案例 4

應用程式在美國的客戶網路。 Azure 與公司網路之間存在 ExpressRoute 與私人對等互連。

**建議︰**將連接器放置於透過 ExpressRoute 私人對等互連連線到公司網路的 Azure DC。 這會遵循上述的 #3 模式。

連接器可放置於 Azure DC。 因為它仍有透過私人網路的直視應用程式以及 DC，躍點 #3 會維持最佳化。 不過，這個設定會進一步最佳化躍點 #2。

這個使用案例會遵循下列 #4 的模式。

![AzureAD Iaas 多雲端廠商](./media/application-proxy-network-topologies/application-proxy-pattern4.png)

### <a name="use-case-5"></a>使用案例 5

應用程式在歐盟的客戶網路。 大部分的使用者在美國。

**建議︰**將連接器放置在應用程式附近。 基於上述理由，這是最佳選擇。 因為美國使用者會存取剛好在相同區域中的應用程式 Proxy 執行個體，躍點 #1 並不過於昂貴。 躍點 #3 已最佳化。 不過，在本使用案例中，躍點 #2 通常很昂貴。

這個使用案例會遵循下列 #5a 的模式。

![AzureAD Iaas 多雲端廠商](./media/application-proxy-network-topologies/application-proxy-pattern5a.png)

請在上述 #2 和 #3 模式中提出時考慮利用 ExpressRoute。 下面我會示範套用模式 #2。

這個使用案例會遵循下列 #5b 的模式。

![AzureAD Iaas 多雲端廠商](./media/application-proxy-network-topologies/application-proxy-pattern5b.png)

以下是您可以考慮採用這個使用案例的其他變化。

如果組織中的大部分使用者不在美國，則可能您的網路也會「延伸」至美國。 如果是這樣，則連接器可以放在美國，而且可以利用歐盟國家應用程式的專用內部企業網路。 如此一來，躍點 #2 和躍點 #3 會最佳化。

這個使用案例會遵循下列 #5c 的模式。

![AzureAD Iaas 多雲端廠商](./media/application-proxy-network-topologies/application-proxy-pattern5c.png)

## <a name="next-steps"></a>後續步驟
[啟用應用程式 Proxy](active-directory-application-proxy-enable.md)<br>
[啟用單一登入](active-directory-application-proxy-sso-using-kcd.md)<br>
[啟用條件式存取](active-directory-application-proxy-conditional-access.md)<br>
[使用應用程式 Proxy 疑難排解您遇到的問題](active-directory-application-proxy-troubleshoot.md)





<!--HONumber=Feb17_HO1-->


