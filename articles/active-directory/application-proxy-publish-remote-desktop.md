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
ms.sourcegitcommit: 40ddf7d6648561eab855b41e76a5ca509c6ca4ef
ms.openlocfilehash: 05130d728c579e0b778bdc8ec49e01f2223c2bba


---

# <a name="publish-remote-desktop-with-azure-active-directory-application-proxy"></a>使用 Azure Active Directory 應用程式 Proxy 發佈遠端桌面

此文章討論如何讓遠端桌面部署可供遠端使用者存取。 此類遠端桌面部署可位於內部部署環境或位於私人網路 (例如 IaaS 部署)。 

> [!NOTE]
> 應用程式 Proxy 是您升級至 Premium 或 Basic 版本的 Azure Active Directory 時才能使用的功能。 如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。
> 
 

您可以透過使用應用程式 Proxy 做為穿通 Proxy 應用程式，來發佈遠端桌面通訊協定流量。 此解決方案可解決連線問題並提供基本安全性保護 (例如網路緩衝處理、強化的網際網路前端與 DDoS 保護)。 

##<a name="remote-desktop-deployment"></a>遠端桌面部署

在遠端桌面部署中，會發佈遠端桌面閘道，以便它可以將 RPC over HTTPS 流量轉換為 RDP over UDP 流量。

您可以將您的用戶端設定為使用遠端桌面用戶端 (例如 MSTSC.exe) 來存取 Azure AD 應用程式 Proxy。 這可讓您使用其連接器來建立連到遠端桌面閘道的新 HTTPS 連線。 透過這樣做，遠端桌面閘道將不會直接暴露給網際網路，而且所有 HTTPS 要求都會先於雲端終止。 

下圖顯示該拓撲。

 ![AzureAD 服務本機](./media/application-proxy-publish-remote-desktop/remote-desktop-topology.png)

## <a name="configure-the-remote-desktop-gateway-url"></a>設定遠端桌面閘道 URL

只要您的使用者設定遠端桌面閘道 URL，當他們如往常一樣觸發 RDP 流量時，他們將能存取檔案與其他方法。

您可以使用應用程式 Proxy (msappproxy.net) 所提供的網域名稱或使用在 Azure AD 上設定的自訂網域名稱 (例如 rdg.contoso.com) 來完成發佈。 

若已使用遠端桌面閘道 URL 設定您的用戶端裝置與 RDP 檔案，您可以選擇使用相同的網域名稱，因此可避免變更。 在此案例中，您應該將涵蓋此網域的憑證提供給應用程式 Proxy，而且必須可從網際網路存取其 CRL。

若未設定遠端桌面閘道 URL，使用者或系統管理員可以在遠端桌面用戶端 (MSTSC) 中使用 [遠端桌面連線] 方塊來指定它，如下所示。

 ![AzureAD 服務本機](./media/application-proxy-publish-remote-desktop/remote-desktop-connection-advanced.png)

當您按一下 [進階] 索引標籤中的 [設定] 時，[連線設定] 方塊會出現。

 ![AzureAD 服務本機](./media/application-proxy-publish-remote-desktop/remote-desktop-connection-settings.png)

## <a name="remote-desktop-web-access"></a>遠端桌面 Web 存取

若您的組織使用遠端桌面 Web 存取 (RDWA) 入口網站，您也可以使用 Azure AD 應用程式 Proxy 來發佈。 您可以使用預先驗證與單一登入 (SSO) 來發佈到此入口網站。

下圖顯示此案例的拓撲。

 ![AzureAD 服務本機](./media/application-proxy-publish-remote-desktop/remote-desktop-web-access-portal1.png)

在上面的案例中，在使用者存取 RDWA 之前，將在 Azure AD 上驗證您的使用者。 若已在 Azur AD 上驗證使用者 (例如，他們使用 Office 365)，則他們不需要再次針對 RDWA 進行驗證。

當您的使用者開始 RDP 工作階段時，他們必須透過 RDP 通道再次驗證。 發生此情況的原因是從 RDWA 到遠端桌面閘道的 SSO 是以使用 ActiveX 在用戶端上存放使用者認證為基礎。 此程序是從 RDWA 表單架構驗證所觸發。 當 RDWA 驗證使用 Kerbros 時，不會呈現表單架構驗證，因此 RDWA 到 RDP 的 SSO 將無法運作。

若 RDWA 需要以 SSO 方式連線到 RDP 流量，或您已大幅自訂 RDWA 表單架構驗證，則可以在沒有預先驗證的情況下發佈 RDWA。

下圖顯示此案例的拓撲。

 ![AzureAD 服務本機](./media/application-proxy-publish-remote-desktop/remote-desktop-web-access-portal2.png)

在上面的案例中，您的使用者將必須使用表單架構驗證向 RDWA 驗證，但他們不需要透過 RDP 驗證。 

請務必注意，在兩個案例中，RDP 流量上都不需要預先驗證。 因此，使用者不需要先執行 RDWA 就能存取它。

##<a name="next-steps"></a>後續步驟

[使用 Azure AD 應用程式 Proxy 啟用 SharePoint 的遠端存取](application-proxy-enable-remote-access-sharepoint.md)<br>
[在 Azure 入口網站中啟用應用程式 Proxy](https://github.com/Microsoft/azure-docs-pr/blob/master/articles/active-directory/active-directory-application-proxy-enable.md)



<!--HONumber=Feb17_HO1-->


