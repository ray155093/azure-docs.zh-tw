---
title: "什麼是 Azure Active Directory B2B 共同作業預覽？ | Microsoft Docs"
description: "Azure Active Directory B2B 共同作業讓企業合作夥伴選擇性地存取您的公司應用程式，以支援公司間的關係"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 1464387b-ee8b-4c7c-94b3-2c5567224c27
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/01/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 8fdd2993e6b3f0345948f35753ebb7a20ed174e3
ms.openlocfilehash: d5764ee2a8447de70798c24a3152a854327ae89d


---

# <a name="what-is-azure-ad-b2b-collaboration-preview"></a>什麼是 Azure AD B2B 共同作業預覽？

Azure AD B2B 共同作業功能可讓 IT 專業人員及資訊工作者與他們在全球任何其他組織的合作夥伴密切合作。 他們可提供對文件、資源及應用程式的存取權，同時又保有對其內部資料的完整控制權。 開發人員可以使用 Azure AD 企業對企業 API 來撰寫應用程式，以對資訊工作者來說順暢且可直覺式瀏覽的安全方式將兩個組織結合在一起。

Azure AD B2B 共同作業功能可讓所有規模大小及在所有產業的組織，不論它們有怎樣的合規性和管理要求，都能與全球的共同作者者輕鬆且安全地一起工作。 這就是這個 B2B 共同作業「公開預覽版更新」的目標。

## <a name="how-does-it-work"></a>運作方式

在目前的版本中 (在 [Azure 傳統入口網站](https://manage.windowsazure.com)中)，若要與某個組織建立關聯性，IT 專業人員和資訊工作者可以透過入口網站或「邀請管理員 API」，一次新增一個或一些來自另一個組織的使用者。 現在，系統管理員可以使用 Azure 中的新入口網站體驗 (https://portal.azure.com) 和 PowerShell 來進行此操作。 而資訊工作者則可以使用 http://myapps.microsoft.com 中的「存取面板」體驗。 開發人員可以建立使用 Azure AD B2B 邀請管理員 API 的應用程式，以新增 B2B 共同作業使用者及自訂邀請和上線工作流程。

通常讓 B2B 共同作業使用者上線的方式是透過一個邀請和兌換程序。 其運作方式如下。

1. 來自 WoodGrove 的 John Doe 想要使用 Sam Oogle 的 Gmail 地址 (gsamoogle@gmail.com) 來新增這位使用者

2. John 移至 WoodGrove 入口網站 (portal.azure.com) 或存取面板 (myapps.microsoft.com)，登入並將該使用者新增到 WoodGrove 目錄、群組或應用程式。

3. John 指定一個自訂邀請電子郵件來傳送給 Sam。

4. 一旦完成，就會在 WoodGrove AD 中建立下列使用者 (螢幕擷取畫面是來自 portal.azure.com 中的系統管理員 UX)：

  ![已新增使用者](media/active-directory-b2b-what-is-azure-ad-b2b/user-is-added.png)

5. 新增完這位使用者之後，Azure AD 將會傳送邀請電子郵件給 Sam：

  ![傳送給 Sam 的邀請郵件](media/active-directory-b2b-what-is-azure-ad-b2b/invitation-mail-sent-to-sam.png)

6. 現在 Sam 可以選取 [開始使用] 並登入。 此時，Azure AD 會以來自他權杖的資訊更新目錄中他的使用者物件 (螢幕擷取畫面是來自 [Azure 入口網站](https://portal.azure.com)中的系統管理員 UX)：

  ![已填入使用者設定檔](media/active-directory-b2b-what-is-azure-ad-b2b/user-profile-is-populated.png)

7. 既然 Sam 的邀請函已兌換，他便可以存取 WoodGrove 資源，當然，系統管理員也可像管理目錄中的任何其他使用者一樣管理 Sam (螢幕擷取畫面是來自 [Azure 入口網站](https://portal.azure.com)中的系統管理員 UX)：

  ![Sam 現在已是 Azure AD 中的使用者](media/active-directory-b2b-what-is-azure-ad-b2b/sam-now-user-in-azure-ad.png)

## <a name="public-preview-features"></a>公用預覽版功能
您一直以來使用我們的公用預覽版 B2B 共同作業功能，並提供我們大量絕佳的意見反應。 而我們也一直傾聽您的心聲！ 我們正將所做的一切改進都封裝在這份「公用預覽版更新」中。 以下是 B2B 共同作業「公開預覽版更新」中的主要功能：

1. 增強 B2B 體驗的系統管理員 UX
  - 來到 https://portal.azure.com
  - 系統管理員能夠邀請 B2B 使用者加入目錄、任何群組或應用程式

2. 可供資訊工作者在「存取面板」中使用的 B2B 共同作業自助邀請功能：https://myapps.microsoft.com。 資訊工作者可以邀請 B2B 共同作業使用者加入他們所管理的任何自助群組或應用程式。

3. 您現在可以透過任何電子郵件地址來邀請使用者。 不論使用者擁有的是 Office365 還是內部部署 Microsoft Exchange 電子郵件地址、outlook.com 電子郵件地址、任何社交電子郵件地址 (Gmail、Yahoo 等等)，現在都能透過以內嵌、輕量型的方式建立 Azure AD 帳戶或 Microsoft 帳戶，來順暢地存取受邀請的組織。

4. 享有專業、租用戶品牌化邀請電子郵件的好處。

5. 透過使用邀請 API 獲得自訂上線方式的廣泛能力。

6. 適用於邀請組織中的 B2B 共同作業使用者的多重要素驗證。

7. 能夠將邀請委派給非系統管理員。

8. 對 B2B 共同作業的 PowerShell 支援。

9. 稽核和報告功能。

## <a name="next-steps"></a>後續步驟

請瀏覽有關 Azure AD B2B 共同作業的其他文章：

* [B2B 共同作業邀請電子郵件的元素](active-directory-b2b-invitation-email.md)
* [Azure AD B2B 共同作業授權 (英文)](active-directory-b2b-licensing.md)
* [Azure Active Directory 中應用程式管理的文章索引](active-directory-apps-index.md)



<!--HONumber=Feb17_HO1-->


