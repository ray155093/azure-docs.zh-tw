---
title: "Azure AD 應用程式 Proxy - 開始安裝連接器 | Microsoft Docs"
description: "在 Azure 入口網站中開啟應用程式 Proxy，並安裝反向 Proxy 的連接器。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: c7186f98-dd80-4910-92a4-a7b8ff6272b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 8bfdc2f4b86e38d707c026365e3ffcce471d5367
ms.lasthandoff: 04/07/2017


---

# <a name="get-started-with-application-proxy-and-install-the-connector"></a>開始使用應用程式 Proxy 並安裝連接器
本文將逐步引導您完成為 Azure AD 中的雲端目錄啟用 Microsoft Azure AD 應用程式 Proxy 的步驟。

如果您不熟悉應用程式 Proxy 可協助您執行的工作，請深入了解 [如何為內部部署應用程式提供安全的遠端存取](active-directory-application-proxy-get-started.md)。

## <a name="application-proxy-prerequisites"></a>應用程式 Proxy 先決條件
您可以啟用並使用應用程式 Proxy 服務之前，必須具備：

* Microsoft Azure AD [基本或進階訂用帳戶](active-directory-editions.md) 以及您是全域管理員的 Azure AD 目錄。
* 執行 Windows Server 2012 R2 或 Windows 8.1 或更高版本的伺服器，您可以在該伺服器上安裝應用程式 Proxy 連接器。 伺服器會將要求傳送至雲端的應用程式 Proxy 服務，而且需要您所發佈之應用程式的 HTTP 或 HTTPS 連線。
  * 如需單一登入已發佈的應用程式，這部電腦應該會加入與您要發佈的應用程式相同的 AD 網域中。 如需詳細資訊，請參閱[使用應用程式 Proxy 進行單一登入](active-directory-application-proxy-sso-using-kcd.md)

如果您的組織使用 Proxy 伺服器來連線至網際網路，請參閱[使用現有的內部部署 Proxy 伺服器](application-proxy-working-with-proxy-servers.md)，以取得如何設定伺服器的詳細資訊。

## <a name="open-your-ports"></a>開啟您的連接埠

如果路徑中有防火牆，請確定防火牆已開啟，以便連接器可以對應用程式 Proxy 提出 HTTPS (TCP) 要求。 連接器會將這些連接埠與屬於高層級網域的子網域一起使用：msappproxy.net 和 servicebus.windows.net。 請務必開啟下列連接埠以 **輸出** 流量：

| 連接埠號碼 | 說明 |
| --- | --- |
| 80 |為安全性驗證啟用輸出 HTTP 流量。 |
| 443 |針對 Azure AD 啟用使用者驗證 (只有在連接器註冊程序才需要)<br>啟用連接器啟動順序和自動更新<br>啟用連接器和 Azure 服務之間的通訊以處理連入要求  |
| 10100–10120 |啟用傳送回 Proxy 的 LOB HTTP 回應 |
| 9350 |針對連入要求選擇性地啟用較佳的效能。 |
| 8080 |啟用連接器啟動順序和連接器自動更新 |
| 9090 |啟用連接器註冊 (只有在連接器註冊程序才需要) |
| 9091 |啟用連接器信任憑證自動更新 |

> [!IMPORTANT]
> 下表反映最新版連接器的連接埠需求。 如果您仍然有比 1.5 版更舊的連接器，您也需要啟用 9350、9352 和 5671。 這些連接埠可啟用舊版連接器和 Azure 服務之間的通訊以處理連入要求。 

如果您的防火牆根據原始使用者強制執行流量，請針對來自當做網路服務執行的 Windows 服務的流量，開放這些連接埠。 此外，請務必為 NT Authority\System 啟用連接埠 8080。

請使用 [Azure AD 應用程式 Proxy 連接器連接埠測試工具](https://aadap-portcheck.connectorporttest.msappproxy.net/)，來確認您的連接器是否能夠連線到「應用程式 Proxy」服務。 至少，請確定「美國中部」區域及離您最近的區域都具有綠色勾選記號。 除此之外，綠色勾選記號越多代表恢復能力越佳。 


## <a name="install-and-register-a-connector"></a>安裝並註冊連接器
1. 在 [Azure 入口網站](https://portal.azure.com/)中，以系統管理員身分登入。
2. 您目前的目錄會顯示在右上角的使用者名稱之下。 如果您需要變更目錄，請選取該圖示。
3. 移至 [Azure Active Directory] > [應用程式 Proxy]。

   ![瀏覽至 [應用程式 Proxy]](./media/active-directory-application-proxy-enable/app_proxy_navigate.png)

4. 選取 [下載連接器]。

   ![下載連接器](./media/active-directory-application-proxy-enable/download_connector.png)

4. 在您根據必要條件所準備好的伺服器上執行 **AADApplicationProxyConnectorInstaller.exe** 。
5. 依照精靈中的指示進行安裝。
6. 在安裝期間，系統將提示您向 Azure AD 租用戶的應用程式 Proxy 註冊連接器。
   
   * 提供您的 Azure AD 全域管理員認證。 您的全域管理員租用戶可能與您的 Microsoft Azure 認證不同。
   * 請確定註冊連接器的系統管理員與您啟用應用程式 Proxy 服務的位置在相同的目錄中。 例如，如果租用戶網域為 contoso.com，則系統管理員應該是 admin@contoso.com ，或該網域上的其他別名。
   * 如果 [IE 增強式安全性設定] 在您要安裝連接器的伺服器上設定為 [開啟]，可能會封鎖註冊畫面。 請依照錯誤訊息中的指示允許存取。 請確定已停用 [Internet Explorer 增強式安全性]。
   * 如果連接器註冊不成功，請參閱 [針對應用程式 Proxy 進行疑難排解](active-directory-application-proxy-troubleshoot.md)。  
7. 安裝完成後，會在您的伺服器中新增兩個新的服務：
   
   * **Microsoft AAD 應用程式 Proxy 連接器** 可啟用連線
     
   * **Microsoft AAD 應用程式 Proxy 連接器更新程式** 是自動更新服務，可定期檢查連接器新版本，並在需要時更新連接器。
     
   ![應用程式 Proxy 連接器服務 - 螢幕擷取畫面](./media/active-directory-application-proxy-enable/app_proxy_services.png)

如需連接器的相關資訊，請參閱[了解 Azure AD 應用程式 Proxy 連接器](application-proxy-understand-connectors.md)。 

為了實現高可用性，您應該至少部署兩個連接器。 若要部署更多連接器，請重複步驟 2 和 3。 每個連接器都必須分別進行註冊。

如果您想要解除安裝連接器，請解除安裝連接器服務和更新程式服務。 重新啟動電腦，才能完全移除此服務。

## <a name="next-steps"></a>後續步驟
您現在已經準備好 [使用應用程式 Proxy 發佈應用程式](application-proxy-publish-azure-portal.md)。

如果您有位於不同網路或不同位置上的應用程式，您可以使用連接器群組將不同的連接器組織成邏輯單元。 深入了解 [使用應用程式 Proxy 連接器](active-directory-application-proxy-connectors-azure-portal.md)。


