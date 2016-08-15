<properties
pageTitle="Azure Active Directory 應用程式和服務主體物件 | Microsoft Azure"
description="Azure Active Directory 中的應用程式物件和服務主體物件之間的關聯性討論"
documentationCenter="dev-center-name"
authors="bryanla"
manager="mbaldwin"
services="active-directory"
editor=""/>

<tags
ms.service="active-directory"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="identity"
ms.date="07/14/2016"
ms.author="bryanla;mbaldwin"/>

# Azure Active Directory 中的應用程式和服務主體物件
當您在閱讀有關 Azure Active Directory (AD)「應用程式」的文章時，其內容不一定能清楚表達究竟作者所指為何。本文的目的就是要更加清楚地闡明其意義，因此我們將會定義 Azure AD 應用程式整合的概念和具體層面，接著舉例說明如何註冊和同意[多租用戶應用程式](active-directory-dev-glossary.md#multi-tenant-application)。

## 概觀
Azure AD 應用程式遠遠不只是一套軟體。它是一個概念性詞彙，指的不只是應用程式軟體，還包括它與 Azure AD 的註冊關係 (也稱為︰身分識別組態)，這可讓它在執行階段參與驗證和授權「對話」。根據定義，應用程式的運作身分可以是[用戶端](active-directory-dev-glossary.md#client-application)角色 (取用資源)、[資源伺服器](active-directory-dev-glossary.md#resource-server)角色 (對用戶端公開 API)，或甚至身兼兩者。對話通訊協定是由 [OAuth 2.0 授權授與流程](active-directory-dev-glossary.md#authorization-grant)所定義，目標是要讓用戶端/資源能夠各自存取/保護資源的資料。現在讓我們再深入一點，看看 Azure AD 應用程式模型在內部是如何代表應用程式。

## 應用程式註冊
當您在 [Azure 傳統入口網站](https://manage.windowsazure.com)註冊應用程式，Azure AD 租用戶中會建立兩個物件︰應用程式物件和服務主體物件。

#### 應用程式物件
Azure AD 應用程式是由其唯一一個應用程式物件所「定義」，該物件位於應用程式註冊所在的 Azure AD 租用戶，也就是所謂的應用程式的「主要」租用戶。應用程式物件能夠為應用程式提供身分識別相關資訊，並可做為其對應服務主體物件的「衍生」範本，以在執行階段使用。

您可以將應用程式看做是應用程式的「全域」代表 (用於所有租用戶)，而將服務主體看做是「本機」代表 (用於特定租用戶)。Azure AD Graph [應用程式實體][AAD-Graph-App-Entity]會定義應用程式物件的結構描述。因此，應用程式物件與軟體應用程式具有 1:1 關聯性，而與其對應的「n」個服務主體物件具有 1:n 關聯性。

#### 服務主體物件
服務主體物件可定義應用程式的原則和權限，為安全性主體提供在執行階段存取資源時用來代表應用程式的基礎。Azure AD Graph [ServicePrincipal 實體][AAD-Graph-Sp-Entity]會定義服務主體物件的結構描述。

在應用程式的使用方式執行個體所必須代表的每個租用戶中都必須要有服務主體物件，這樣才能安全存取該租用戶的使用者帳戶所擁有的資源。單一租用戶應用程式只會有一個服務主體 (在其主要租用戶中)；多租用戶 [Web 應用程式](active-directory-dev-glossary.md#web-client)也是一樣，但另外會在租用戶的系統管理員或使用者已同意該應用程式的每個租用戶上，擁有一個用來存取其資源的服務主體。在同意之後，未來的授權要求都會參考服務主體物件。

> [AZURE.NOTE] 您對應用程式物件所做的任何變更也只會反映於它在應用程式的主要租用戶 (其註冊所在租用戶) 中的服務主體物件。當您的應用程式設定為供多租用戶存取時，如果沒有先移除取用者租用戶的存取權，然後再重新授與存取權，則對應用程式物件的變更就不會反映在任何取用者租用戶的服務主體物件上。

## 範例
下圖說明應用程式的應用程式物件和對應的服務主體物件之間的關係，其背景是在稱為 **HR 應用程式**的多租用戶應用程式範例中。此案例中有三個 Azure AD 租用戶︰

- **Adatum** - 開發 **HR 應用程式**之公司所使用的租用戶
- **Contoso** - Contoso 組織所使用的租用戶，其為 **HR 應用程式**的取用者
- **Fabrikam** - Fabrikam 組織所使用的租用戶，其亦會取用 **HR 應用程式**

![應用程式物件和服務主體物件之間的關聯性](./media/active-directory-application-objects/application-objects-relationship.png)

上圖的步驟 1 是在應用程式的主要租用戶中建立應用程式和服務主體物件的程序。

在步驟 2 中，當 Contoso 和 Fabrikam 的系統管理員完成同意並授與存取權給應用程式，就會在其公司的 Azure AD 租用戶中建立服務主體物件，並對其指派公司的系統管理員所授與的權限。也請注意，HR 應用程式可能會設定/設計為允許由使用者同意以進行個人使用。

在步驟 3 中，HR 應用程式的取用者租用戶 (例如 Contoso 和 Fabrikam) 各會有自己的服務主體物件，以代表其在執行階段使用的應用程式執行個體，並且會由系統管理員所同意的權限來管理。

## 後續步驟
應用程式的應用程式物件可透過 Azure AD 圖形 API 來存取，如其 OData 的[應用程式實體](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference?branch=master#ApplicationEntity)所表示

應用程式的服務主體物件可透過 Azure AD 圖形 API 來存取，如其 OData 的 [ServicePrincipal 實體](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference?branch=master#ServicePrincipalEntity)所表示



<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#ApplicationEntity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipalentity
[AZURE-classic-portal]: https://manage.windowsazure.com

<!---HONumber=AcomDC_0803_2016-->