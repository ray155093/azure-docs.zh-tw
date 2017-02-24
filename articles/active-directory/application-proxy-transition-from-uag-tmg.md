---
title: "從 Microsoft Forefront 轉換到 Azure AD 應用程式 Proxy | Microsoft Docs"
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
ms.date: 01/27/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 9945512d18d66c321c0d24ee1050497d4716fd47
ms.openlocfilehash: 699112846cc1e4e9fc6b04b1b8509152d7aecdef


---
#<a name="transition-to-azure-ad-app-proxy-from-microsoft-forefront"></a>從 Microsoft Forefront 轉換到 Azure AD 應用程式 Proxy

此文章說明如何從 Microsoft Forefront 的 Threat Management Gateway (TMG) 與 United Access Gateway (UAG) 解決方案轉換到這些 Azuare AD 應用程式 Proxy：Web 應用程式 Proxy (WAP) 與 Azure AD 應用程式 Proxy (AADAP)。 

> [!NOTE]
> 應用程式 Proxy 是您升級至 Premium 或 Basic 版本的 Azure Active Directory 時才能使用的功能。 如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。
> 
 
許多客戶都詢問我們如何從 Forefront UAG 和 TMG 轉換到新的應用程式 Proxy。 如需詳細資訊，請參閱此[白皮書](http://download.microsoft.com/download/3/E/3/3E335D93-6DB8-4834-90A8-B86105419F05/Microsoft%20TMG%20and%20UAG%20EOL%20and%20transitioning%20to%20WAP%20and%20AADAP.docx)以了解詳細轉換方式。 
 
## <a name="tmguag-conversion-to-azure-ad-application-proxy-table"></a>TMG/UAG 轉換到 Azure AD 應用程式 Proxy 表
 
|**TMG/UAG 功能**|**Web 應用程式 Proxy (WAP) / Azure AD 應用程式 Proxy (AADAP)**|
|:-----|:-----|
|適用於瀏覽器應用程式的選擇性 HTTP 發佈|可在 Windows Server 2012 R2 中的 WAP 找到 (現在則可在 AADAP 中找到)|
|ADFS 整合|可在 Windows Server 2012 R2 中的 WAP 找到 (現在則可在 AADAP 中找到)|
|豐富的通訊協定發佈 (例如 Citrix、Lync、RDG)|可在 Windows Server 2012 R2 中的 WAP 找到 (現在則可在 AADAP 中找到)|
|ActiveSync 的預先驗證 (HTTP 基本) 與 RDG|將可在 Windows Server vNext 中的 WAP 找到 (將在 AADAP 推出)|
|入口網站|使用 Intune / 適用於 WAP 的 System Center (使用 AAD 存取面板或適用於 AADAP 的 Office 365 應用程式啟動器)|
|端點健康狀態偵測|使用 Intune / System Center|
|SSL 通道處理|使用 Windows SSL / VPN 功能|
|第 2 層/第 3 層防火牆|使用 Windows Server 功能|
|Web 應用程式防火牆|目前沒有 Microsoft 解決方案|
|安全 Web 閘道 (轉送 Proxy)|目前沒有 Microsoft 解決方案|


## <a name="next-steps"></a>後續步驟

[部署 Web 應用程式 Proxy](https://blogs.technet.microsoft.com/applicationproxyblog/tag/web-application-proxy)<br>
[部落格：應用程式 Proxy](https://blogs.technet.microsoft.com/applicationproxyblog/tag/aad-ap)



<!--HONumber=Feb17_HO1-->


