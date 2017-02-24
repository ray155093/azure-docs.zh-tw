---
title: "Azure AD 應用程式 Proxy 中的自訂網域 | Microsoft Docs"
description: "管理 Azure AD 應用程式 Proxy 中的自訂網域，讓不論使用者從何處存取都能看到相同的應用程式 URL。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: harshja
ms.assetid: 2fe9f895-f641-4362-8b27-7a5d08f8600f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 081e45e0256134d692a2da7333ddbaafc7366eaa
ms.openlocfilehash: b8edebd4e7dfdbf85a9beb6d126acaf7ec66dd01


---
# <a name="working-with-custom-domains-in-azure-ad-application-proxy"></a>使用 Azure AD 應用程式 Proxy 中的自訂網域
使用預設網域，可讓您將相同的 URL 設為可供存取應用程式的內部或外部 URL，而您的使用者在存取應用程式時只需記住一個 URL (不論他們是從哪裡進行存取)。 這也讓您可以在應用程式的 [存取面板] 中建立單一捷徑。 如果您使用 Azure AD 應用程式 Proxy 所提供的預設網域，則無需進行其他設定來啟用您的網域。 若使用自訂網域，您需要執行一些動作，以確保應用程式 Proxy 能辨識您的網域並驗證其憑證。

## <a name="selecting-your-custom-domain"></a>選取自訂網域
1. 根據 [使用應用程式 Proxy 發佈應用程式](active-directory-application-proxy-publish.md)中的指示，發佈您的應用程式。
2. 在應用程式出現在應用程式清單後，加以選取並按一下 [設定] 。
3. 在 [外部 URL] 之下，輸入您的自訂網域。
4. 如果您的外部 URL 是 https，系統將提示您上傳憑證，以便 Azure 驗證應用程式的 URL。 您也可以上傳符合應用程式外部 URL 的萬用字元憑證。 這個網域必須在您的 [Azure 已驗證網域](https://msdn.microsoft.com/library/azure/jj151788.aspx)清單內。 Azure 必須有應用程式之網域 URL 的憑證或符合應用程式之 [外部 URL] 的萬用字元憑證。
5. 新增 DNS 記錄，以將內部 URL 路由傳送到應用程式。 此記錄可讓您為內部與外部應用程式存取提供相同的 URL，並在使用者的應用程式清單中提供單一捷徑。

## <a name="frequently-asked-questions-about-working-with-custom-domains"></a>有關使用自訂網域的常見問題
問：是否可以選取已上傳的憑證，而不再重新上載？  
答︰先前已上傳的憑證會自動繫結至應用程式，而只會有一個符合應用程式主機名稱的憑證。  

問：如何加入憑證，而已匯出的憑證應以何種格式上傳？  
答：憑證應從應用程式組態頁面進行上傳。 此憑證應該是 PFX 檔案。  

問：是否可以使用 ECC 憑證？  
答：簽章方法沒有明確的限制。  

問：是否可以使用 SAN 憑證？  
答： 會。  

問：是否可以使用萬用字元憑證？  
答： 會。  

問：是否可以在每個應用程式上使用不同的憑證？  
答：可以，除非兩個應用程式共用相同的外部主機。  

問：如果我註冊新的網域，是否可以使用該網域？  
答：可以，網域清單是來自租用戶的已驗證網域清單。  

問：憑證過期會發生什麼狀況？  
答：您會在應用程式組態頁面的 [憑證] 區段中收到警告。 當使用者嘗試存取應用程式時，將會顯示安全性警告。  

問：我該如何取代指定應用程式的憑證？  
答：從應用程式組態頁面上傳新的憑證。  

問：我是否可以刪除憑證並取代它？  
答：當您上傳新的憑證時，如果舊的憑證並未由另一個應用程式所使用，將會自動予以刪除。  

問：憑證被撤銷時會發生什麼狀況？  
答：不會對憑證執行撤銷檢查。 當使用者嘗試存取應用程式時，視瀏覽器而定，可能會出現安全性警告。  

問：是否可以使用自我簽署憑證？  
答：可以，可以使用自我簽署憑證。 請注意，如果您使用私人憑證授權單位，則憑證的 CDP (憑證撤銷點發佈點) 應是公開的。  

問：是否有地方可以查看我的租用戶的所有憑證？  
答︰目前的版本不支援此功能。  

## <a name="next-steps"></a>後續步驟
* [啟用單一登入](active-directory-application-proxy-sso-using-kcd.md)以登入您使用 Azure AD 驗證發佈的應用程式。
* [啟用條件式存取](active-directory-application-proxy-conditional-access.md)以存取您發佈的應用程式。
* [將自訂網域名稱新增至 Azure AD](active-directory-add-domain.md)





<!--HONumber=Feb17_HO1-->


