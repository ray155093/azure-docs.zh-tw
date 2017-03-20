---
title: "使用 Azure Active Directory 應用程式 Proxy 發佈遠端桌面 | Microsoft Docs"
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
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 0d9f2f5c9ad5c47eb5e1a8a8ec3fee2952caef63
ms.openlocfilehash: 5c01f89c335a6c85b5b50a4c8491ae1b02740578
ms.lasthandoff: 02/27/2017


---

# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>使用 Azure AD 應用程式 Proxy 發佈遠端桌面

本文討論如何讓 Windows 遠端桌面部署可供遠端使用者存取。 遠端桌面部署可位於內部部署環境或位於私人網路 (例如 IaaS 部署)。

> [!NOTE]
> 在升級至 Premium 或 Basic 版本的 Azure Active Directory (Azure AD) 後，才能使用應用程式 Proxy 功能。 如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。

您可以透過使用 Azure AD 應用程式 Proxy 做為穿通 Proxy 應用程式，來發佈遠端桌面通訊協定 (RDP) 流量。 此解決方案可解決連線問題並提供基本安全性保護 (例如網路緩衝處理、強化的網際網路前端與分散式阻斷服務 (DDoS) 保護)。

## <a name="remote-desktop-deployment"></a>遠端桌面部署

在遠端桌面部署中，會發佈遠端桌面閘道，以便它可以將 RPC (遠端程序呼叫) over HTTPS 流量轉換為 RDP over UDP (使用者資料包通訊協定) 流量。

您可以將用戶端設定為使用遠端桌面用戶端 (例如 MSTSC.exe) 來存取 Azure AD 應用程式 Proxy。 如此一來，您就可以使用其連接器來建立連到遠端桌面閘道的新 HTTPS 連線。 因此，閘道將不會直接暴露給網際網路，而且所有 HTTPS 要求都會先於雲端終止。

拓撲如下圖所示：

 ![Azure AD 服務本機圖](./media/application-proxy-publish-remote-desktop/remote-desktop-topology.png)

## <a name="configure-the-remote-desktop-gateway-url"></a>設定遠端桌面閘道 URL

當使用者如往常一樣設定遠端桌面閘道 URL 並觸發 RDP 流量，他們便能存取檔案與其他方法。

您可以使用應用程式 Proxy (msappproxy.net) 所提供的網域名稱或使用在 Azure AD 上設定的自訂網域名稱 (例如 rdg.contoso.com) 來完成發佈。

若已使用遠端桌面閘道 URL 設定用戶端裝置與 RDP 檔案，您可以選擇使用相同的網域名稱，因此可避免變更。 在此案例中，您應該將涵蓋此網域的憑證提供給應用程式 Proxy，而且必須可從網際網路存取其憑證撤銷清單 (CRL)。

若未設定遠端桌面閘道 URL，使用者或系統管理員可以在遠端桌面用戶端 (MSTSC) 中使用 [遠端桌面連線] 對話方塊來指定它，如下所示。

 ![遠端桌面連線對話方塊](./media/application-proxy-publish-remote-desktop/remote-desktop-connection-advanced.png)

當您按一下 [進階] 索引標籤中的 [設定] 時，[連線設定] 對話方塊會出現。

 ![[遠端桌面連線] 對話方塊中的連線設定視窗](./media/application-proxy-publish-remote-desktop/remote-desktop-connection-settings.png)

## <a name="remote-desktop-web-access"></a>遠端桌面 Web 存取

若您的組織使用遠端桌面 Web 存取 (RDWA) 入口網站，您也可以使用 Azure AD 應用程式 Proxy 來發佈。 您可以使用預先驗證與單一登入 (SSO) 來發佈到此入口網站。

RDWA 案例的拓撲如下圖所示：

 ![RDWA 案例的圖表](./media/application-proxy-publish-remote-desktop/remote-desktop-web-access-portal1.png)

在上面的案例中，在使用者存取 RDWA 之前，將在 Azure AD 上驗證使用者。 若已在 Azure AD 上驗證使用者 (例如，他們使用 Office 365)，則他們不需要再次針對 RDWA 進行驗證。

當使用者開始 RDP 工作階段時，他們必須透過 RDP 通道再次驗證。 發生此情況的原因是從 RDWA 到遠端桌面閘道的 SSO 是以使用 ActiveX 在用戶端上存放使用者認證為基礎。 此程序是從 RDWA 表單架構驗證所觸發。 當 RDWA 驗證使用 Kerbros 時，不會呈現表單架構驗證，因此 RDWA 到 RDP 的 SSO 無法運作。

若 RDWA 需要以 SSO 方式連線到 RDP 流量，或您已大幅自訂 RDWA 表單架構驗證，則可以在沒有預先驗證的情況下發佈 RDWA。

此案例的拓撲如下圖所示：

 ![RDWA 案例的圖表](./media/application-proxy-publish-remote-desktop/remote-desktop-web-access-portal2.png)

在上面的案例中，使用者必須使用表單架構驗證向 RDWA 驗證，但他們不需要透過 RDP 驗證。

>[!NOTE]
>在上面的兩個案例中，RDP 流量不需要進行預先驗證。 因此，使用者不需要先執行 RDWA 就能存取它。

## <a name="next-steps"></a>後續步驟

[使用 Azure AD 應用程式 Proxy 啟用 SharePoint 的遠端存取](application-proxy-enable-remote-access-sharepoint.md)  
[在 Azure 入口網站中啟用應用程式 Proxy](https://github.com/Microsoft/azure-docs-pr/blob/master/articles/active-directory/active-directory-application-proxy-enable.md)

