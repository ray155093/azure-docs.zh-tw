---
title: "Azure MFA Server Mobile App Web 服務 | Microsoft Docs"
description: "Microsoft Authenticator 應用程式提供額外的頻外驗證選項。  它可以讓 MFA Server 將通知推播給使用者。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 6c8d6fcc-70f4-4da4-9610-c76d66635b8b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/15/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: H1Hack27Feb2017,it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 20afeb3ba290ddf728d2b52c076c7a57fadc77c6
ms.openlocfilehash: 4014bf0217e25ea9bc8473ef2383279e5eb79b87
ms.contentlocale: zh-tw
ms.lasthandoff: 02/28/2017

---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>使用 Azure Multi-Factor Authentication Server 來啟用行動應用程式驗證

Microsoft Authenticator 應用程式提供額外的頻外驗證選項。 Azure Multi-Factor Authentication 不會在使用者登入時，撥打自動電話或傳送 SMS 給使用者，而是會將通知推送到使用者智慧型手機或平板電腦上的 Microsoft Authenticator 應用程式。 使用者只需要在應用程式中點選 驗證 \(或輸入 PIN 再點選 [驗證]) 即可完成登入。

當手機收訊不可靠時，建議使用行動應用程式進行兩步驟驗證。 如果您使用此應用程式作為 OATH 權杖產生器，它並不需要任何網路或網際網路連線。

若要將使用者入口網站安裝在 Azure Multi-Factor Authentication Server 以外的伺服器上，必須進行下列步驟：

1. 安裝 Web 服務 SDK
2. 安裝 Mobile App Web 服務
3. 在 Azure Multi-Factor Authentication Server 中配置行動應用程式設定
4. 為使用者啟用 Microsoft Authenticator 應用程式

## <a name="requirements"></a>需求

若要使用 Microsoft Authenticator 應用程式，必須符合以下條件，應用程式才能與 Mobile App Web 服務成功通訊：

* Azure Multi-Factor Authentication Server 6.0 或更新版本
* 將 Mobile App Web 服務安裝在執行 Microsoft® [Internet Information Services (IIS) IIS 7.x 或更新版本](http://www.iis.net/)的網際網路對向 Web 伺服器上
* 已安裝、註冊 ASP.NET v4.0.30319 並已設定為 [已允許]
* 所需的角色服務包括 ASP.NET 和 IIS 6 Metabase 相容性
* 可透過公用 URL 存取 Mobile App Web 服務
* 已使用 SSL 憑證來保護 Mobile App Web 服務。
* 將 Azure Multi-Factor Authentication Web 服務 SDK 安裝在與 Azure Multi-Factor Authentication Server 相同伺服器上的 IIS 7.x 或更新版本中
* 已使用 SSL 憑證來保護 Azure Multi-Factor Authentication Web 服務 SDK。
* Mobile App Web 服務可透過 SSL 連接到 Azure Multi-Factor Authentication Web 服務 SDK
* Mobile App Web 服務可使用隸屬於 "PhoneFactor Admins" 安全性群組之服務帳戶的認證向 Azure MFA Web 服務 SDK 進行驗證。 如果 Azure Multi-Factor Authentication Server 是位於已加入網域的伺服器上，Active Directory 中就會有此服務帳戶和群組。 如果伺服器未加入網域，此服務帳戶和群組會存在於 Azure Multi-Factor Authentication Server 本機。


## <a name="install-the-web-service-sdk"></a>安裝 Web 服務 SDK
如果 Azure Multi-Factor Authentication (MFA) Server 上尚未安裝 Azure Multi-Factor Authentication Web 服務 SDK，請移至該伺服器，然後開啟 Azure MFA Server。

1. 按一下 [Web 服務 SDK] 圖示。
2. 按一下 [安裝 Web 服務 SDK]，然後依照呈現的指示操作。

Web 服務 SDK 必須受到 SSL 憑證保護。 自我簽署憑證適用於這項用途。 請將此憑證匯入到「使用者入口網站」Web 伺服器上 [本機電腦] 帳戶的 [受信任的根憑證授權單位] 存放區中，這樣它才會在起始 SSL 連接時信任該憑證。

![設定](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)

## <a name="install-the-mobile-app-web-service"></a>安裝 Mobile App Web 服務
在安裝 Mobile App Web 服務之前，請注意下列細節：

* 如果您已經在網際網路對向伺服器上安裝「Azure MFA 使用者入口網站」，就可以從「使用者入口網站」的 web.config 檔案將使用者名稱、密碼及 URL 複製到 Web 服務 SDK。
* 在網際網路對向 Web 伺服器上開啟網頁瀏覽器，並瀏覽至輸入 web.config 檔案中的 Web 服務 SDK URL，如此將有所幫助。 如果瀏覽器可以順利連接 Web 服務，它應該會提示您輸入認證。 輸入使用者名稱和密碼 (與輸入 web.config 檔案中的使用者名稱和密碼完全相同)。 確定未出現任何憑證警告或錯誤。
* 如果反向 Proxy 或防火牆是在 Mobile App Web 服務 Web 伺服器之前，並且目前執行 SSL 卸載，則您可以編輯 Mobile App Web 服務的 web.config 檔案，以便讓 Mobile App Web 服務能夠使用 http 而不是 https。 從 Mobile App 到防火牆/反向 Proxy 仍然需要 SSL。 將下列索引鍵新增到 \<appSettings\> 區段：

        <add key="SSL_REQUIRED" value="false"/>

### <a name="install-the-service"></a>安裝服務

1. 在 Azure Multi-Factor Authentication Server 上開啟「Windows 檔案總管」，然後瀏覽至安裝 Azure MFA Server 的資料夾 (通常是 C:\Program Files\Azure Multi-Factor Authentication)。 選擇 32 位元或 64 位元版本的 Azure Multi-Factor AuthenticationPhoneAppWebServiceSetup 安裝檔案。 將安裝檔案複製到網際網路對向伺服器。

2. 在網際網路對向 Web 伺服器上，以系統管理員權限執行安裝程式檔案。 以系統管理員身分開啟命令提示字元，然後瀏覽至安裝檔案的複製位置。

3. 執行 Multi-Factor AuthenticationMobileAppWebServiceSetup 安裝檔案，視需要變更網站，然後將虛擬目錄變更為簡短的名稱 (例如 “PA”)。

  在啟用期間，由於使用者必須將 Mobile App Web 服務 URL 輸入行動裝置，因此我們建議使用簡短的虛擬目錄名稱。

4. Azure Multi-Factor AuthenticationMobileAppWebServiceSetup 安裝完成之後，瀏覽至 C:\inetpub\wwwroot\PA (或根據虛擬目錄名稱瀏覽至適當目錄) 並編輯 web.config 檔案。

5. 找出 WEB_SERVICE_SDK_AUTHENTICATION_USERNAME 和 WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD 機碼。 將值設定為隸屬於 PhoneFactor Admins 安全性群組之服務帳戶的使用者名稱和密碼。 如果您之前已安裝 Azure Multi-Factor Authentication 使用者入口網站，這可能是當做該使用者入口網站之身分識別的帳戶。 請務必將使用者名稱和密碼輸入行尾的引號之間 (value=””/>)。 使用合格的使用者名稱 (例如 domain\username 或 machine\username)。  

6. 找出 pfMobile App Web Service_pfwssdk_PfWsSdk 設定。 將值從 *http://localhost:4898/PfWsSdk.asmx* 變更為在 Azure Multi-Factor Authentication Server 上執行之 Web 服務 SDK 的 URL (例如 https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx)。

  由於此連線使用 SSL，因此您必須依伺服器名稱參考 Web 服務 SDK，而不是依 IP 位址。 SSL 憑證會是針對該伺服器名稱發行的，因此所使用的 URL 必須與憑證上的名稱相符。 該伺服器名稱可能未解析為來自網際網路對向伺服器的 IP 位址。 如果發生這種情況，請在該伺服器上的 hosts 檔案中新增項目，以將 Azure Multi-Factor Authentication Server 的名稱與其 IP 位址對應。 完成變更後，儲存 web.config 檔案。

7. 如果安裝 Mobile App Web 服務的網站尚未與公開簽署的憑證繫結，請在伺服器上安裝憑證、開啟「IIS 管理員」，然後將憑證繫結至該網站。

8. 從任何電腦開啟網頁瀏覽器，然後瀏覽至安裝 Mobile App Web 服務的 URL (例如 https://www.publicwebsite.com/PA)。 確定未出現任何憑證警告或錯誤。

### <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>在 Azure Multi-Factor Authentication Server 中配置行動應用程式設定
既然 Mobile App Web 服務已安裝完成，您需要設定 Azure Multi-Factor Authentication Server，使其與入口網站搭配運作。

1. 在 Azure MFA Server 中，按一下 [使用者入口網站] 圖示。 如果您允許使用者控制其驗證方法，請在 [設定] 索引標籤之 [允許使用者選取方法] 下方選取 [行動應用程式]。 若未啟用這個功能，使用者就必須連絡技術支援人員來完成行動應用程式啟用。
2. 選取 [允許使用者啟用行動裝置應用程式] 方塊。
3. 選取 [允許使用者註冊] 方塊。
4. 按一下 [行動應用程式] 圖示。
5. 輸入與安裝 Azure Multi-Factor AuthenticationMobileAppWebServiceSetup 時所建立之虛擬目錄搭配使用的 URL。 您可以在提供的空間內輸入帳戶名稱。 此處的公司名稱會顯示在行動應用程式中。 如果保留空白，則會顯示在 Azure 傳統入口網站中建立之「多因素驗證提供者」的名稱。

<center>![設定](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)</center>

