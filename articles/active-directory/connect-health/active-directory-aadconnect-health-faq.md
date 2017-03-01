---
title: "Azure Active Directory Connect Health 常見問題集 - Azure | Microsoft Docs"
description: "此常見問題集會回答 Azure AD Connect Health 的相關問題。 這個常見問題集涵蓋使用服務的相關問題，包括計費模型、功能、限制及支援。"
services: active-directory
documentationcenter: 
author: billmath
manager: samueld
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 6ad2194a71e0d36ba4a0b9a46ca6dbcd58b619ff
ms.openlocfilehash: 06a8b79f0740e902bb7f9412b449a98b2f0167ea
ms.lasthandoff: 02/16/2017

---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Azure AD Connect Health 常見問題集
本文會回答有關 Azure Active Directory (Azure AD) Connect Health 的常見問題 (FAQ)。 這些常見問題涵蓋如何使用服務的相關問題，包括計費模型、功能、限制及支援。

## <a name="general-questions"></a>一般問題
**問：我管理多個 Azure AD 目錄。我如何切換到包含 Azure Active Directory Premium 的租用戶？**

若要在不同的 Azure AD 租用戶之間切換，請在右上角選取目前登入的 [使用者名稱]，然後選擇適當的帳戶。 如果此處未列出帳戶，請選取 [登出]，然後使用已啟用 Azure Active Directory Premium 之目錄的全域系統管理員認證登入。

**問︰Azure AD Connect Health 支援哪個版本的身分識別角色？**

下表列出各角色與支援的作業系統版本。

|角色| 作業系統/版本|
|--|--|
|Active Directory Federation Services (AD FS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|
|Azure AD Connect | 版本 1.0.9125 或更高版本|
|Active Directory Domain Services (AD DS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|

請注意，服務所提供的功能可能會因為角色和作業系統而有所不同。 換句話說，並非所有作業系統版本都能使用所有功能。 如需詳細資料，請參閱功能描述。

**問︰我需要多少授權才能監視基礎結構？**

* 第一個 Connect Health 代理程式至少需要一個 Azure AD Premium 授權。
* 每多一個註冊代理程式，就需要再 25 個 Azure AD Premium 授權。
* 代理程式計數等於所有受監視角色 (AD FS、Azure AD Connect 及/或 AD DS) 的註冊代理程式總數。

您也可以在 [Azure AD 定價頁面](https://aka.ms/aadpricing)找到授權資訊。

範例：

| 已註冊的代理程式 | 需要的授權 | 監視組態範例 |
| ------ | --------------- | --- |
| 1 | 1 | 1 個 Azure AD Connect 伺服器 |
| 2 | 26| 1 個 Azure AD Connect 伺服器、1 個網域控制站 |
| 3 | 51 | 1 個 Active Directory Federation Services (AD FS) 伺服器、1 個 AD FS Proxy、1 個網域控制站 |
| 4 | 76 | 1 個 AD FS 伺服器、1 個 AD FS Proxy、2 個網域控制站 |
| 5 | 101 | 1 個 Azure AD Connect 伺服器、1 個 AD FS 伺服器、1 個 AD FS Proxy、2 個網域控制站 |


## <a name="installation-questions"></a>安裝問題

**問：在個別的伺服器上安裝 Azure AD Connect Health 代理程式有什麼影響？**

安裝 Microsoft Azure AD Connect Health 代理程式、AD FS、Web 應用程式 Proxy 伺服器、Azure AD Connect (同步處理) 伺服器、網域控制站，對於 CPU、記憶體耗用量、網路頻寬和儲存體的影響非常小。

以下的數字是近似值：

* CPU 耗用量：增加約&1;-5%。
* 記憶體耗用量：最多 10% 的系統總記憶體。

> [!NOTE]
> 如果代理程式無法與 Azure 通訊，則代理程式會在本機儲存資料，最多可達定義的上限。 代理程式會根據「最近最少服務」的準則來覆寫「快取」資料。
>
>

* Azure AD Connect Health 代理程式的本機緩衝區儲存體：約&20; MB。
* 對於 AD FS 伺服器，建議您為 AD FS 稽核通道佈建 1024 MB (1 GB) 的磁碟空間，Azure AD Connect Health 代理程式才能在所有稽核資料遭到覆寫前加以處理。

**問：在安裝 Azure AD Connect Health 代理程式期間，我是否需要重新啟動我的伺服器？**

否。 安裝代理程式不需要您重新啟動伺服器。 不過，安裝某些先決條件的步驟可能需要重新啟動伺服器。

例如，在 Windows Server 2008 R2 上安裝 .NET 4.5 Framework 需要重新啟動伺服器。

**問：Azure AD Connect Health 是否透過傳遞 Http Proxy 運作？**

是。 若是進行中的作業，您可以將 Health 代理程式設定為使用 HTTP Proxy 來轉送輸出 HTTP 要求。
深入了解[設定 Health 代理程式的 HTTP Proxy](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)。

如果需要在代理程式註冊期間設定 Proxy，您可能需要預先修改 Internet Explorer 的 Proxy 設定。

1. 開啟 Internet Explorer > [設定]  >  [網際網路選項]  >  [連線]  >  [LAN 設定]。
2. 選取 [在您的區域網路使用 Proxy 伺服器]。
3. 如果您有不同的 Proxy 連接埠供 HTTP 和 HTTPS/安全使用，請選取 [進階]。

**問：連線到 HTTP Proxy 時，Azure AD Connect Health 是否支援基本驗證？**

否。 目前不支援為基本驗證指定任意使用者名稱和密碼的機制。

**問：我需要開放哪些防火牆連接埠，Azure AD Connect Health 代理程式才能運作？**

請參閱[需求](active-directory-aadconnect-health-agent-install.md#requirements)一節，以取得防火牆連接埠清單與其他的連線需求。

**問︰為什麼我會在 Azure AD Connect Health 入口網站中看到兩部名稱相同的伺服器？**

當您從伺服器移除代理程式時，系統不會自動從 Azure AD Connect Health 入口網站移除伺服器。 如果您以手動方式從伺服器移除代理程式，或移除伺服器本身，就需要以手動方式從 Azure AD Connect Health 入口網站中刪除伺服器項目。

您可能會重新安裝伺服器的映像，或以相同的細節 (例如電腦名稱) 建立新的伺服器。 如果您並未從 Azure AD Connect Health 入口網站移除已經註冊的伺服器，就在新伺服器上安裝代理程式，您可能會看到兩個同名的項目。

在此情況下，請手動刪除屬於舊伺服器的項目。 此伺服器的資料應該已過時。

## <a name="health-agent-registration-and-data-freshness"></a>Health 代理程式註冊和資料有效性

**問︰Health 代理程式註冊失敗的常見原因為何？該如何解決問題？**

Health 代理程式會因為下列可能原因而無法註冊：

* 因為防火牆封鎖流量，代理程式無法與所需端點通訊。 在 Web 應用程式 Proxy 伺服器上尤其常見。 請確定您已允許針對所需端點和連接埠的輸出通訊。 如需詳細資訊，請參閱[需求](active-directory-aadconnect-health-agent-install.md#requirements)一節。
* 輸出通訊會在網路層遇到 SSL 檢查。 這會導致代理程式所使用的憑證遭到檢查伺服器/實體所取代，完成代理程式註冊的步驟便會失敗。
* 使用者沒有執行代理程式註冊的存取權。 根據預設，全域系統管理員具有存取權。 您可以使用[角色型存取控制](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control)將存取權委派給其他使用者。

**問︰我收到有關「Health 服務資料不是最新狀態」的警示。我該如何進行解決這個問題？**

當 Azure AD Connect Health 在過去&2; 小時沒有從伺服器收到所有資料點，就會產生此警示。 有很多原因都可能導致引發此警示。

* 因為防火牆封鎖流量，代理程式無法與所需端點通訊。 在 Web 應用程式 Proxy 伺服器上尤其常見。 請確定您已允許針對所需端點和連接埠的輸出通訊。 如需詳細資訊，請參閱[需求](active-directory-aadconnect-health-agent-install.md#requirements)一節。
* 輸出通訊會在網路層遇到 SSL 檢查。 這會導致代理程式所使用的憑證遭到檢查伺服器/實體所取代，而無法將資料上傳至 Azure AD Connect Health 服務。
* 您可以使用代理程式內建的連線命令。 [閱讀更多資訊](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)。
* 代理程式也支援透過未經驗證之 HTTP Proxy 的輸出連線。 [閱讀更多資訊](active-directory-aadconnect-health-agent-install.md##configure-azure-ad-connect-health-agents-to-use-http-proxy)。

## <a name="operations-questions"></a>操作問題
**問︰是否需要在 Web 應用程式 Proxy 伺服器上啟用稽核？**

否，不需要在 Web 應用程式 Proxy 伺服器上啟用稽核。

**問：Azure AD Connect Health 警示如何獲得解決？**

Azure AD Connect Health 警示會在成功情況下獲得解決。 Azure AD Connect Health 代理程式會定期偵測成功情況，並向服務回報。 對於少數幾個警示，隱藏是以時間為基礎。 也就是說，如果在警示產生的 72 小時內未觀察到相同的錯誤狀況，就會自動解決警示。

## <a name="migration-questions"></a>移轉問題

本節僅適用於獲通知其 Azure AD Connect Health 資料即將移轉的客戶。

**問︰移轉之後，我是否必須再次註冊我的代理程式，或重新設定我的通知設定？**

否，在移轉過程中，代理程式註冊資訊和通知設定將會隨之移動。

**問︰移轉之後需要多久，我才會在入口網站上開始看到資料？**

資料將會開始在移轉之後的一小時內出現在入口網站中。

**問︰我的現存作用中警示會發生什麼情況？**

在移轉之後的一小時內，所有適用的警示都會重新啟動。

## <a name="related-links"></a>相關連結
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure AD Connect Health 代理程式安裝](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health 操作](active-directory-aadconnect-health-operations.md)
* [在 AD FS 使用 Azure AD Connect Health](active-directory-aadconnect-health-adfs.md)
* [使用 Azure AD Connect Health 進行同步處理](active-directory-aadconnect-health-sync.md)
* [在 AD DS 使用 Azure AD Connect Health](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health 版本歷程記錄](active-directory-aadconnect-health-version-history.md)

