---
title: "Azure AD Connect：更新 Active Directory Federation Services (AD FS) 伺服器陣列的 SSL 憑證 | Microsoft Docs"
description: "本文件詳述使用 Azure AD Connect 更新 AD FS 伺服器陣列 SSL 憑證的步驟。"
services: active-directory
keywords: "azure ad connect, adfs ssl 更新, adfs 憑證更新, 變更 adfs 憑證, 新增 adfs 憑證, adfs 憑證, 更新 adfs ssl 憑證, 更新 ssl 憑證 adfs, 設定 adfs ssl 憑證, adfs, ssl, 憑證, adfs 服務通訊憑證, 更新同盟, 設定同盟, aad connect"
authors: anandyadavmsft
manager: femila
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2016
ms.author: anandy
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: d8f4181d7b51109cc5a2e205a40225a7763d0be4
ms.lasthandoff: 03/07/2017


---    

# <a name="update-the-ssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>更新 Active Directory Federation Services (AD FS) 伺服器陣列的 SSL 憑證

## <a name="overview"></a>概觀
本文說明 Azure AD Connect 如何能用來更新 Active Directory Federation Services (AD FS) 伺服器陣列的 SSL 憑證。  如果 Azure AD Connect 中的登入方法設定為 AD FS，則可使用 Azure AD Connect 工具來輕鬆地更新 AD FS 伺服器陣列的 SSL 憑證 - 跨所有同盟和 WAP 伺服器，只要 3 個簡單步驟︰

![三個步驟](./media/active-directory-aadconnectfed-ssl-update/threesteps.png)


>[!NOTE]
>若要深入了解 AD FS 所使用的憑證，請參閱文章[了解 AD FS 所使用的憑證](https://technet.microsoft.com/library/cc730660.aspx)

##<a name="pre-requisites"></a>必要條件

* **AD FS 伺服器陣列**：AD FS 伺服器陣列應該是 2012R2 或更高版本
* **Azure AD Connect**︰請確定 Azure AD Connect 版本為 1.1.443.0 或更新版本。 您會找到工作「更新 AD FS SSL 憑證」

![更新 SSL 工作](./media/active-directory-aadconnectfed-ssl-update/updatessltask.png)

##<a name="step-1-provide-ad-fs-farm-information"></a>步驟 1︰提供 AD FS 伺服器陣列資訊

AAD Connect 會透過下列方式，嘗試自動取得 AD FS 伺服器陣列的相關資訊︰
1. 自 AD FS (2016 或更新版本) 查詢伺服器陣列資訊
2. 參照來自前次執行的資訊 (使用 Azure AD Connect 儲存在本機)。 

顯示的伺服器清單可透過新增或移除伺服器，以反映 AD FS 伺服器陣列目前的組態來修改。 只要提供了伺服器資訊，Azure AD Connect 會顯示連線和目前的 SSL 憑證狀態。

![AD FS 伺服器資訊](./media/active-directory-aadconnectfed-ssl-update/adfsserverinfo.png)

如果清單包含不再屬於 AD FS 伺服器陣列的伺服器，則按一下 [移除] 可從 AD FS 伺服器陣列中的伺服器清單刪除伺服器。 

![清單中的離線伺服器](./media/active-directory-aadconnectfed-ssl-update/offlineserverlist.png)
 
>[!NOTE] 
> 在 Azure AD Connect 中從 AD FS 伺服器陣列的伺服器清單中移除是本機作業，並且會更新 Azure AD Connect 在本機維護之 AD FS 伺服器陣列的資訊。 Azure AD Connect 將不會在 AD FS 上做任何組態修改來反映變更設定。    

##<a name="step-2-provide-new-ssl-certificate"></a>步驟 2︰提供新的 SSL 憑證

一旦 AD FS 伺服器陣列伺服器的相關資訊已確認，Azure AD Connect 會要求新的 SSL 憑證。 提供使用密碼保護的 PFX 憑證以繼續安裝。 

![SSL 憑證](./media/active-directory-aadconnectfed-ssl-update/certificate.png)
 
一旦提供憑證，則 Azure AD Connect 會對憑證執行一系列的必要條件檢查，以確保此憑證對該 AD FS 伺服器陣列是正確的︰
1.    憑證的主體名稱 (SN)/主體替代名稱 (SAN) 會與同盟相同服務名稱相同，或它是萬用字元憑證。
2.    憑證有效期限為 30 天以上
3.    憑證信任鏈結有效 
4.    憑證使用密碼保護

##<a name="step-3-select-servers-for-update"></a>步驟 3︰選取用於更新的伺服器

在下一個步驟中，選取 SSL 憑證需要更新的伺服器。 無法選取離線的伺服器進行更新。 

![選取要更新的伺服器](./media/active-directory-aadconnectfed-ssl-update/selectservers.png)

組態完成之後，Azure AD Connect 將會顯示訊息，指出更新的狀態，並且會提供一個選項來驗證 AD FS 登入。

![組態完成](./media/active-directory-aadconnectfed-ssl-update/configurecomplete.png)   

##<a name="faqs"></a>常見問題集

* **新 AD FS SSL 憑證之憑證的主體名稱應該是什麼？**

    Azure AD Connect 會檢查憑證的主體名稱/替代主體名稱是否包含同盟服務名稱。 例如，如果同盟服務名稱為 fs.contoso.com，則主體名稱/替代主體名稱必須是 fs.contoso.com。  也接受萬用字元憑證。 

* **為什麼會要求我於 WAP 伺服器頁面上再次輸入認證？**

    如果提供用於連線至 AD FS 伺服器的認證也不具備管理 WAP 伺服器的權限，Azure AD Connect 會要求提供在 WAP 伺服器上具有系統管理權限的認證。

* **伺服器顯示為離線，我該怎麼辦？**

    如果伺服器已離線，Azure AD Connect 即無法執行任何作業。 如果伺服器是 AD FS 伺服器陣列的一部分，請檢查對伺服器的連線，並且在您解決問題之後，是按一下重新整理圖示，以更新精靈中的狀態。 如果伺服器稍早是伺服器陣列的一部分，但現在已不存在，請按一下移除，以將它從 Azure AD Connect 維護的伺服器清單中刪除。  從 Azure AD Connect 的清單中移除伺服器不會改變 AD FS 組態本身，並且如果您使用 AD FS 2016 或更新版本，伺服器會保留在組態設定中，而且在下一次工作執行時再次顯示。

* **可以使用新的 SSL 憑證更新我的伺服器陣列伺服器子集嗎？**

    是。 您永遠可以再次執行工作「更新 SSL 憑證」來更新其餘的伺服器。 在 [選取要進行 SSL 憑證更新的伺服器] 頁面上，您可以依「SSL 到期日」來排序伺服器清單，以便輕鬆地存取尚未更新的伺服器。 

* **我在上一次執行中移除了伺服器，但它仍然顯示為離線，並且列在 [AD FS 伺服器] 頁面。為何即使在移除後，離線的伺服器還在？**

    從 Azure AD Connect 的清單中移除伺服器並不會在 AD FS 組態中將它移除。 Azure AD Connect 也會參照 AD FS (2016 或更新版本)，以取得伺服器陣列的任何資訊，並且，若伺服器仍存在於 AD FS 組態中，將會列在清單中。  

## <a name="next-steps"></a>後續步驟

[Azure AD Connect 和同盟](active-directory-aadconnectfed-whatis.md)
[使用 Azure AD Connect 管理和自訂 Active Directory Federation Services](active-directory-aadconnect-federation-management.md)

