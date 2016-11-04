---
title: Azure Active Directory 應用程式和服務主體物件 | Microsoft Docs
description: Azure Active Directory 中的應用程式物件和服務主體物件之間的關聯性討論
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
services: active-directory
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/10/2016
ms.author: bryanla;mbaldwin

---
# Azure Active Directory 中的應用程式和服務主體物件
當您在閱讀有關 Azure Active Directory (AD)「應用程式」的文章時，其內容不一定能清楚表達究竟作者所指為何。本文的目的是要藉由定義 Azure AD 應用程式整合的概念和具體層面，並舉例說明如何註冊和同意[多租用戶應用程式](active-directory-dev-glossary.md#multi-tenant-application)，提供更清楚的說明。

## 概觀
Azure AD 應用程式遠遠不只是一套軟體。它是一個概念性詞彙，指的不只是應用程式軟體，還包括它與 Azure AD 的註冊關係 (也稱為︰身分識別組態)，這可讓它在執行階段參與驗證和授權「對話」。根據定義，應用程式的運作身分可以是[用戶端](active-directory-dev-glossary.md#client-application)角色 (取用資源)、[資源伺服器](active-directory-dev-glossary.md#resource-server)角色 (對用戶端公開 API)，或甚至身兼兩者。對話通訊協定是由 [OAuth 2.0 授權授與流程](active-directory-dev-glossary.md#authorization-grant)所定義，目標是要讓用戶端/資源能夠各自存取/保護資源的資料。現在讓我們再深入一點，看看 Azure AD 應用程式模型在內部是如何代表應用程式。

## 應用程式註冊
當您在 [Azure 傳統入口網站][AZURE-Classic-Portal]註冊應用程式，Azure AD 租用戶中會建立兩個物件︰應用程式物件和服務主體物件。

#### 應用程式物件
Azure AD 應用程式是由其唯一一個應用程式物件所「定義」，該物件位於應用程式註冊所在的 Azure AD 租用戶，也就是所謂的應用程式的「主要」租用戶。應用程式物件能夠為應用程式提供身分識別相關資訊，並可做為其對應服務主體物件的「衍生」範本，以在執行階段使用。

您可以將應用程式看做是應用程式的「全域」代表 (用於所有租用戶)，而將服務主體看做是「本機」代表 (用於特定租用戶)。Azure AD Graph [應用程式實體][AAD-Graph-App-Entity]會定義應用程式物件的結構描述。因此，應用程式物件與軟體應用程式具有 1:1 關聯性，而與其對應的「n」個服務主體物件具有 1:n 關聯性。

#### 服務主體物件
服務主體物件會定義應用程式的原則和權限，為安全性主體提供在執行階段存取資源時用來代表應用程式的基礎。Azure AD Graph [ServicePrincipal 實體][AAD-Graph-Sp-Entity]會定義服務主體物件的結構描述。

在每個租用戶中，只要其應用程式使用方式執行個體必須被代表，就必須要有服務主體物件，這樣才能安全地存取該租用戶的使用者帳戶所擁有的資源。單一租用戶應用程式將只有一個服務主體 (在其主要租用戶中)。多租用戶 [Web 應用程式](active-directory-dev-glossary.md#web-client)在租用戶的系統管理員或使用者已表示同意的每個租用戶中也會有一個服務主體，使其可以存取他們的資源。在同意之後，未來的授權要求都會參考服務主體物件。

> [!NOTE]
> 您對應用程式物件所做的任何變更也只會反映於它在應用程式的主要租用戶 (其註冊所在租用戶) 中的服務主體物件。就多租用戶應用程式而言，對應用程式物件所做的變更必須等到取用者租用戶移除存取權後再重新授與存取權，才會反映在任何取用者租用戶的服務主體物件上。
> 
> 

## 範例
下圖說明應用程式的應用程式物件與對應的服務主體物件之間的關係，是以一個稱為「HR 應用程式」的範例多租用戶應用程式為背景。此案例中有三個 Azure AD 租用戶︰

* **Adatum** - 開發 **HR 應用程式**之公司所使用的租用戶
* **Contoso** - Contoso 組織所使用的租用戶，其為 **HR 應用程式**的取用者
* **Fabrikam** - Fabrikam 組織所使用的租用戶，其亦會取用 **HR 應用程式**

![應用程式物件和服務主體物件之間的關聯性](./media/active-directory-application-objects/application-objects-relationship.png)

在前一張圖中，步驟 1 是在應用程式的主要租用戶中建立應用程式和服務主體物件的程序。

在步驟 2 中，當 Contoso 和 Fabrikam 的系統管理員完成同意，系統就會在其公司的 Azure AD 租用戶中建立服務主體物件，並指派系統管理員所授與的權限。也請注意，HR 應用程式可能會設定/設計為允許由使用者同意以進行個人使用。

在步驟 3 中，HR 應用程式的取用者租用戶 (Contoso 和 Fabrikam) 都分別擁有自己的服務主體物件。每個均代表他們在執行階段的應用程式執行個體使用，其中皆受到個別系統管理員所同意的權限控管。

## 後續步驟
若要存取應用程式的應用程式物件，可以透過 Azure AD Graph API 來存取 (如其 OData 的[應用程式實體][AAD-Graph-App-Entity]所代表)

若要存取應用程式的服務主體物件，可以透過 Azure AD Graph API 來存取 (如其 OData 的 [ServicePrincipal 實體][AAD-Graph-Sp-Entity]所代表)

<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AZURE-Classic-Portal]: https://manage.windowsazure.com

<!---HONumber=AcomDC_0810_2016------>