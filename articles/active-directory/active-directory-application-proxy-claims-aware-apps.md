---
title: "宣告感知應用程式 - Azure AD 應用程式 Proxy | Microsoft Docs"
description: "如何發佈接受 ADFS 宣告的內部部署 ASP.NET 應用程式，讓您的使用者安全地進行遠端存取。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: harshja
ms.assetid: 91e6211b-fe6a-42c6-bdb3-1fff0312db15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: 081e45e0256134d692a2da7333ddbaafc7366eaa
ms.openlocfilehash: ff07a52f6a503f07f5919b63f345878571742cac
ms.contentlocale: zh-tw
ms.lasthandoff: 02/06/2017


---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>在應用程式 Proxy 中使用宣告感知應用程式
宣告感知應用程式會執行前往 Security Token Service (STS) 的重新導向，而 STS 會接著向使用者要求認證以交換權杖，之後才會將使用者重新導向至應用程式。 若要啟用應用程式 Proxy 來使用這些重新導向，必須採取下列步驟。

## <a name="prerequisites"></a>必要條件
執行此程序之前，請確定宣告感知應用程式重新導向的 STS 可從內部部署網路外部使用。

## <a name="azure-classic-portal-configuration"></a>Azure 傳統入口網站設定
1. 根據 [使用應用程式 Proxy 發佈應用程式](active-directory-application-proxy-publish.md)中的所述指示來發佈您的應用程式。
2. 在應用程式清單中選取宣告感知應用程式，並按一下 [設定] 。
3. 如果您選擇 [通道] 作為您的 [預先驗證方法]，請務必選取 [HTTPS] 作為您的 [外部 URL] 配置。
4. 如果您選擇 [Azure Active Directory] 作為您的 [預先驗證方法]，請選取 [無] 作為您的 [內部驗證方法]。

## <a name="adfs-configuration"></a>ADFS 組態
1. 開啟 [ADFS 管理]。
2. 移至 [信賴憑證者信任]，並在您要使用「應用程式 Proxy 」來發佈的應用程式上按一下滑鼠右鍵，然後選擇 [屬性]。  

   ![信賴憑證者信任 - 以滑鼠右鍵按一下應用程式名稱 - 螢幕擷取畫面](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)  

3. 在 [端點] 索引標籤的 [端點類型] 底下，選取 [WS-同盟]。
4. 在 [信任的 URL] 底下，輸入您在「應用程式 Proxy」的 [外部 URL] 底下輸入的 URL，然後按一下 [確定]。  

   ![新增端點 - 設定 [信任的 URL] 值 - 螢幕擷取畫面](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>後續步驟
* [啟用單一登入](active-directory-application-proxy-sso-using-kcd.md)
* [使用應用程式 Proxy 疑難排解您遇到的問題](active-directory-application-proxy-troubleshoot.md)
* [啟用原生用戶端應用程式以與 Proxy 應用程式互動](active-directory-application-proxy-native-client.md)



