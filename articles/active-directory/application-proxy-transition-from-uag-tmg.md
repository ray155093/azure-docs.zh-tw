---
title: "升級到 Azure AD 應用程式 Proxy | Microsoft Docs"
description: "如果您是從 Microsoft Forefront 或 Unified Access Gateway 升級，選擇哪一個 Proxy 解決方案最適合。"
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
ms.date: 04/27/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: e9d5caa4d11012744ce9f26648166371f3aa17ba
ms.contentlocale: zh-tw
ms.lasthandoff: 05/12/2017

---
# <a name="upgrade-to-azure-ad-proxies-from-microsoft-forefront-or-unified-access-gateway"></a>從 Microsoft Forefront 或 Unified Access Gateway 升級到 Azure AD Proxy

本文說明如何從 Microsoft Forefront Threat Management Gateway (TMG) 與 United Access Gateway (UAG) 解決方案轉換到 Azure AD 應用程式 Proxy。

如需從 Forefront TMG 和 UAG 轉換到應用程式 Proxy 的詳細資訊，您可以[從 Microsoft 閱讀相關的技術白皮書](https://blogs.technet.microsoft.com/isablog/2015/06/30/modernizing-microsoft-application-access-with-web-application-proxy-and-azure-active-directory-application-proxy/)。

## <a name="functionality-details-for-the-conversion"></a>功能轉換詳細資料

|**TMG/UAG 功能**|**現代解決方案**|
|:-----|:-----|
|適用於瀏覽器應用程式的選擇性 HTTP 發佈|Azure AD 應用程式 Proxy|
|Active Directory Federation Services (AD FS) 整合|Azure AD 應用程式 Proxy|
|豐富的通訊協定發佈 (例如 Citrix、Lync、RDG)|Azure AD 應用程式 Proxy|
|入口網站|若為 Azure AD 應用程式 Proxy，則使用 Azure AD 存取面板或 Office 365 應用程式啟動程式|
|端點健全狀況偵測|Intune 或 System Center|
|SSL 通道處理|Windows SSL 或 VPN|
|第 2 層/第 3 層防火牆|Windows Server|
|ActiveSync 的預先驗證 (HTTP 基本) 與 RDG|目前沒有 Microsoft 解決方案|
|Web 應用程式防火牆|Microsoft 目前未提供解決方案。|
|安全 Web 閘道 (轉送 Proxy)|Microsoft 目前未提供解決方案。|


## <a name="next-steps"></a>後續步驟

[部署 Web 應用程式 Proxy](https://blogs.technet.microsoft.com/applicationproxyblog/tag/web-application-proxy)<br>
[部落格︰Azure AD 應用程式 Proxy](https://blogs.technet.microsoft.com/applicationproxyblog/tag/aad-ap) (英文)

