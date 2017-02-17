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
ms.date: 02/06/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 85458f4477dadb83a6a2627ef490471ca38ac634
ms.openlocfilehash: a8ef669bdb28bfe66dacb42798a1ae65bcffdb4e


---
# <a name="azure-ad-connect-health-frequently-asked-questions-faq"></a>Azure AD Connect Health 常見問題集 (FAQ)
此常見問題集會回答 Azure AD Connect Health 的相關問題。 這個常見問題集涵蓋使用服務的相關問題，包括計費模型、功能、限制及支援。

## <a name="general-questions"></a>一般問題
**問：我管理多個 Azure AD 目錄。我如何切換到包含 Azure Active Directory Premium 的租用戶？**

您可以切換不同的 Azure AD 租用戶，方法為在右上角選取目前登入的使用者名稱，然後選擇適當的帳戶。 如果此處未列出帳戶，請選取 [登出]，然後使用已啟用 Azure Active Directory Premium 之目錄的全域系統管理員認證登入。

## <a name="installation-questions"></a>安裝問題
**問：在個別的伺服器上安裝 Azure AD Connect Health 代理程式有什麼影響？**

安裝 Microsoft Azure AD Connect Health Agents ADFS、Web 應用程式 Proxy 伺服器、Azure AD Connect (同步處理) 伺服器、網域控制站，對於 CPU、記憶體耗用量、網路頻寬和儲存體的影響非常小。

以下的數字是近似值。

* CPU 耗用量：增加約&1;%
* 記憶體耗用量：最多 10% 的系統總記憶體

> [!NOTE]
> 如果代理程式無法與 Azure 通訊，則代理程式會在本機儲存資料，最多可達定義的上限。 代理程式會根據「最近最少服務」的準則來覆寫「快取」資料。
> 
> 

* Azure AD Connect Health 代理程式的本機緩衝區儲存體：~20 MB
* 對於 AD FS 伺服器，建議您為 AD FS 稽核通道佈建 1024 MB (1 GB) 的磁碟空間，Azure AD Connect Health 代理程式才能在所有稽核資料遭到覆寫前加以處理。

**問：在安裝 Azure AD Connect Health 代理程式期間，我是否需要重新啟動我的伺服器？**

否。 安裝代理程式不需要您重新啟動伺服器。 不過，安裝某些先決條件的步驟可能需要重新啟動伺服器。

例如，在 Windows Server 2008 R2 上安裝 .Net 4.5 Framework 需要重新啟動伺服器。

**問：Azure AD Connect Health 服務是否透過通過 http proxy 運作？**

是。  若是進行中的作業，您可以設定 Health 代理程式，以使用 HTTP Proxy 來轉送輸出 http 要求。

如果需要在代理程式註冊期間設定 Proxy，您可能需要預先修改 Internet Explorer Proxy 設定。

1. 開啟 Internet Explorer -> [設定] -> [網際網路選項] -> [連線]-> [LAN 設定]。
2. 選取 [在您的區域網路使用 Proxy 伺服器]。
3. 如果您有不同的 Proxy 連接埠供 HTTP 和 HTTPS/安全使用，請選取 [進階]。

**問：連線到 Http Proxy 時，Azure AD Connect Health 服務是否支援基本驗證？**

否。 目前不支援為基本驗證指定任意使用者名稱/密碼的機制。

**問︰適用於 AD DS 的 Azure AD Connect Health 支援哪個版本的 AD DS？**

安裝於下列作業系統版本上時支援 AD DS 的監視︰

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

## <a name="operations-questions"></a>操作問題
**問：我是否需要在 AD FS 應用程式 Proxy 伺服器或 Web 應用程式 Proxy 伺服器上啟用稽核？**

否，不需要在 Web 應用程式 Proxy (WAP) 伺服器上啟用稽核。 在 AD FS 伺服器上加以啟用。

**問：Azure AD Connect Health 警示如何獲得解決？**

Azure AD Connect Health 警示會在成功情況下獲得解決。 Azure AD Connect Health 代理程式會定期偵測成功情況，並向服務回報。 對於少數幾個警示，隱藏是以時間為基礎。 也就是說，如果在警示產生的 72 小時內未觀察到相同的錯誤狀況，就會自動解決警示。

**問：我需要開放哪些防火牆連接埠，Azure AD Connect Health 代理程式才能運作？**

您必須開放 TCP/UDP 連接埠 443 和 5671，Azure AD Connect Health 代理程式才能夠與 Azure AD Health 服務端點進行通訊。

**問︰為什麼我會在 Azure AD Connect Health 入口網站中看到兩部名稱相同的伺服器？**

當您從伺服器移除代理程式時，系統不會自動從 Azure AD Connect 入口網站移除伺服器。  如果您以手動方式從伺服器移除代理程式，或移除伺服器本身，就需要以手動方式從 Azure AD Connect Health 入口網站中刪除伺服器項目。 

此外，如果您重新製作了伺服器的映像或使用相同的詳細資訊 (例如電腦名稱) 建立新的伺服器，但並未從 Azure AD Connect Health 入口網站移除已經註冊的伺服器，就在新伺服器上安裝代理程式，您可能會看到兩個同名的項目。  
在此情況下，您應該手動刪除屬於較舊伺服器的項目。 此伺服器的資料應該已過時。

## <a name="migration-questions"></a>移轉問題

本節僅適用於獲通知其 Azure AD Connect Health 服務資料即將移轉的客戶。

**問︰移轉之後，我是否必須重新註冊我的代理程式，或重新設定我的通知設定？**

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




<!--HONumber=Jan17_HO4-->


