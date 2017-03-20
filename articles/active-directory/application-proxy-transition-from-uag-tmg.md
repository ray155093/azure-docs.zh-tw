---
title: "從 Microsoft Forefront 轉換到 Azure AD 應用程式 Proxy | Microsoft Docs"
description: "探討如何從 Microsoft Forefront TMG 和 UAG 解決方案轉換到 Azure Active Directory 應用程式 Proxy 的相關基本概念。"
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
ms.date: 01/27/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: c887adaae811972efd7fcea86eaa67a899f3f4c2
ms.openlocfilehash: 542dd7df7e0b887298522f29cb597f1df73709cb
ms.lasthandoff: 02/27/2017


---
# <a name="transition-to-azure-ad-application-proxies-from-microsoft-forefront"></a>從 Microsoft Forefront 轉換到 Azure AD 應用程式 Proxy

本文說明如何從 Microsoft Forefront Threat Management Gateway (TMG) 與 United Access Gateway (UAG) 解決方案轉換到這些 Azure Active Directory (Azure AD) 應用程式 Proxy：Web 應用程式 Proxy 與 Azure AD 應用程式 Proxy。

> [!NOTE]
> Azure AD 應用程式 Proxy 是您升級至 Premium 或 Basic 版本的 Azure AD 時才能使用的功能。 如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。


如需從 Forefront TMG 和 UAG 轉換到新的應用程式 Proxy 的詳細資訊，您可以[從 Microsoft 下載相關的技術白皮書](http://download.microsoft.com/download/3/E/3/3E335D93-6DB8-4834-90A8-B86105419F05/Microsoft%20TMG%20and%20UAG%20EOL%20and%20transitioning%20to%20WAP%20and%20AADAP.docx)。

## <a name="functionality-details-for-the-conversion"></a>功能轉換詳細資料

|**TMG/UAG 功能**|**Web 應用程式 Proxy/Azure AD 應用程式 Proxy**|
|:-----|:-----|
|適用於瀏覽器應用程式的選擇性 HTTP 發佈|可在 Windows Server 2012 R2 的 Web 應用程式 Proxy 取得。 可立即在 Azure AD 應用程式 Proxy 取得。|
|Active Directory Federation Services (AD FS) 整合|可在 Windows Server 2012 R2 的 Web 應用程式 Proxy 取得。 可立即在 Azure AD 應用程式 Proxy 取得。|
|豐富的通訊協定發佈 (例如 Citrix、Lync、RDG)|可在 Windows Server 2012 R2 的 Web 應用程式 Proxy 取得。 可立即在 Azure AD 應用程式 Proxy 取得。|
|ActiveSync 的預先驗證 (HTTP 基本) 與 RDG|目前無法在 Web 應用程式 Proxy 或 Azure AD 應用程式 Proxy 取得。|
|入口網站|若為 Web 應用程式 Proxy，使用 Intune 或 System Center。 若為 Azure AD 應用程式 Proxy，則使用 Azure AD 存取面板或 Office 365 應用程式啟動程式。|
|端點健全狀況偵測|使用 Intune 或 System Center。|
|SSL 通道處理|使用 Windows SSL 或 VPN 功能。|
|第 2 層/第 3 層防火牆|使用 Windows Server 功能。|
|Web 應用程式防火牆|Microsoft 目前未提供解決方案。|
|安全 Web 閘道 (轉送 Proxy)|Microsoft 目前未提供解決方案。|


## <a name="next-steps"></a>後續步驟

[部署 Web 應用程式 Proxy](https://blogs.technet.microsoft.com/applicationproxyblog/tag/web-application-proxy)<br>
[部落格︰Azure AD 應用程式 Proxy](https://blogs.technet.microsoft.com/applicationproxyblog/tag/aad-ap) (英文)

