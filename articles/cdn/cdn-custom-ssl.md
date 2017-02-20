---
title: "在 Azure CDN 自訂網域上啟用 HTTPS | Microsoft Docs"
description: "了解如何在具有自訂網域的 Azure CDN 端點上啟用 HTTPS。"
services: cdn
documentationcenter: 
author: camsoper
manager: erikre
editor: 
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: casoper
translationtype: Human Translation
ms.sourcegitcommit: aea6f0fedb447e953b9db40342e5091bd35486af
ms.openlocfilehash: 9b80ae8c247480e69025b86fac322166a4d5cb4c


---
# <a name="enable-https-on-an-azure-cdn-custom-domain"></a>在 Azure CDN 自訂網域上啟用 HTTPS

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Azure CDN 自訂網域的 HTTPS 支援，可讓您使用自訂的網域名稱透過 SSL 傳遞安全內容，以改善資料傳送的安全性。 啟用自訂網域 HTTPS 的端對端工作流程已簡化為一步啟用和完整憑證管理，而且不須額外費用。

確保您所有 Web 應用程式在傳輸資料時的隱私和資料完整性非常重要。 使用 HTTPS 通訊協定可確保您的機密資料在網際網路上傳遞時已經過加密。 它提供信任與認證，並保護您的 Web 免於攻擊。 目前，Azure CDN 支援 CDN 端點上的 HTTPS。 舉例來說，當您從 Azure CDN (https://contoso.azureedge.net) 建立端點時，預設會啟用 HTTPS。 現在透過自訂網域 HTTPS，您也可以針對自訂網域 (如 https://www.contoso.com) 啟用安全傳遞。 

HTTPS 功能的一些重要特色如下：

- 不須額外付費：取得或續約憑證不須付費，且 HTTPS 流量不另外收費。 您只需支付 CDN 的 GB 輸出。

- 容易啟用：從 [Azure 入口網站](https://portal.azure.com)按一下就能佈建。 您也可以使用 REST API 或其他開發工具來啟用此功能。

- 完整憑證管理：為您處理所有憑證採購及管理。 系統會自動佈建憑證並在到期前自動更新。 這樣可以完全避免因為憑證到期而導致的服務中斷風險。

>[!NOTE] 
>啟用 HTTPS 支援之前，您必須先建立 [Azure CDN 自訂網域](./cdn-map-content-to-custom-domain.md)。

## <a name="step-1-enabling-the-feature"></a>步驟 1︰啟用功能 

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽到您的 Verizon 標準或進階 CDN 設定檔。

2. 在端點清單中，按一下包含自訂網域的端點。

3. 按一下您要啟用 HTTPS 的自訂網域。

    ![端點刀鋒視窗](./media/cdn-custom-ssl/cdn-custom-domain.png)

4. 按一下 [開啟]來啟用 HTTPS 並儲存變更。

    ![自訂 HTTPS 對話方塊](./media/cdn-custom-ssl/cdn-enable-custom-ssl.png)


## <a name="step-2-domain-validation"></a>步驟 2︰網域驗證

>[!IMPORTANT] 
>必須先完成網域驗證才能在您的自訂網域上啟用 HTTPS。 您有 6 個工作天可以核准網域。 6 個工作天內沒有核准將會取消要求。  

在您的自訂網域上啟用 HTTPS 之後，我們的憑證提供者 DigiCert 會根據 WHOIS 註冊資訊，透過電子郵件 (預設) 或電話連絡您網域的註冊人，以驗證網域的所有權。 如果 WHOIS 註冊資訊是不公開的，DigiCert 會傳送驗證電子郵件到 **admin@*<您的網域名稱.com>***。

收到電子郵件之後，您有兩種驗證方式選項：

1. 您可以核准未來所有經相同帳戶針對同一個根網域 (如 consoto.com) 的所有訂購。 如果您計畫未來要針對同一個跟網域新增其他自訂網域，則建議您選擇此方法。
 
2. 您可以只核准這次要求使用的特定主機名稱。 後續的要求將需要另外核准。

    範例電子郵件：
    
    ![自訂 HTTPS 對話方塊](./media/cdn-custom-ssl/domain-validation-email-example.png)

核准之後，DigiCert 會將您的自訂網域名稱加入 SAN 憑證。 憑證有效期限為一年，並且會在到期之前自動更新。

## <a name="step-3-wait-for-the-propagation-then-start-using-your-feature"></a>步驟 3︰ 等待傳播，然後開始使用您的功能

自訂網域名稱的 HTTPS功能要在網域名稱通過驗證之後 6-8 小時才會啟用。 程序完成之後，Azure 入口網站中的 [自訂 HTTPS] 狀態將會設為 [啟用]。 您現在即可使用訂網域的 HTTPS 功能。

## <a name="frequently-asked-questions"></a>常見問題集

1. *憑證提供者是誰？使用的是哪一種憑證？*

    我們使用 DigiCert 提供的主題別名 (SAN) 憑證。 SAN 憑證可以使用一個憑證來保護多個完整網域名稱。

2. *是否可以使用自己的憑證？*
    
    目前不行，但此功能已列在藍圖中。

3. *如果沒有收到 DigiCert 的驗證電子郵件該怎麼辦？*

    如果您沒有在 24 小時內收到驗證電子郵件，請連絡 Microsoft。

4. *SAN 憑證的安全性是否比專用憑證來得低？*
    
    SAN 憑證遵循和專用憑證相同的加密與安全性標準。 所有核發的 SSL 憑證都使用 SHA-256 來加強伺服器安全性。

5. *是否可以搭配 Akamai 的 Azure CDN 使用自訂網域 HTTPS？*

    此功能目前只提供給 Verizon 的 Azure CDN。 我們正在進行相關工作，讓此功能在未來的幾個月內支援 Akamai 的 Azure CDN。


## <a name="next-steps"></a>後續步驟

- 了解如何[在 Azure CDN 端點上設定自訂網域](./cdn-map-content-to-custom-domain.md)





<!--HONumber=Feb17_HO1-->


