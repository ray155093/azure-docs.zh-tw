---
title: "如何將多租用戶應用程式新增至 Azure AD 應用程式庫 | Microsoft Docs"
description: "說明如何在 Azure AD 應用程式庫中列出自訂開發的多租用戶應用程式"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 9f3caff0e998d670ce82b847525a63f2a85572d3
ms.contentlocale: zh-tw
ms.lasthandoff: 04/17/2017

---

<a id="how-to-add-a-multi-tenant-application-to-the-azure-ad-application-gallery" class="xliff"></a>

# 如何將多租用戶應用程式新增至 Azure AD 應用程式庫

<a id="what-is-the-azure-ad-application-gallery" class="xliff"></a>

## 什麼是 Azure AD 應用程式庫？

Azure AD 應用程式庫是將您的應用程式呈現於所有數百萬位 Azure Active Directory 客戶面前的絕佳方法，以擴大該應用程式在 Marketplace 中的影響力與覆蓋率。 下列步驟說明如何在 Azure AD 應用程式庫中列出您的應用程式。

<a id="if-your-application-supports-saml-or-openidconnect" class="xliff"></a>

## 如果您的應用程式支援 SAML 或 OpenIDConnect
如果您想要在列出 Azure AD 應用程式庫中列出某個多租用戶應用程式，您必須先確定該應用程式支援下列其中一種單一登入技術：

1. **OpenID Connect** - 直接整合 Azure AD，使用 OpenID Connect 進行驗證，使用 Azure AD 同意 API 進行設定。 如果您是剛開始整合，而您的應用程式不支援 SAML，這是建議的模式。
2. **SAML** – 您的應用程式已能夠使用 SAML 通訊協定設定協力廠商識別提供者。

如果您的應用程式支援這其中一種單一登入模式，而且您想要在 Azure AD 應用程式庫列出您的多租用戶應用程式，即可依照本文件後續內容中的步驟執行。 若要即刻開始使用，請將電子郵件傳送至 **waadpartners@microsoft.com** 。

<a id="if-your-application-does-not-support-saml-or-openidconnect" class="xliff"></a>

## 如果您的應用程式不支援 SAML 或 OpenIDConnect
即使您的應用程式不支援這其中一種模式，我們仍然可以使用密碼單一登入技術，將它整合到我們的資源庫。 如果您想要探索此選項，您可以將電子郵件傳送至 **waadpartners@microsoft.com** 。

<a id="next-steps" class="xliff"></a>

## 後續步驟
[如何在 Azure Active Directory 應用程式庫中列出您的應用程式](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)

